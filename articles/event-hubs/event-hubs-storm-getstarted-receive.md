---
title: Ta emot händelser från Azure Event Hubs använder Apache Storm | Microsoft Docs
description: Börja ta emot från Event Hubs använder Apache Storm
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2018
ms.author: sethm
ms.openlocfilehash: 6f558ff0613937d17f2dd7c2c9db6eb2de31ab9e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Ta emot händelser från Event Hubs använder Apache Storm

[Apache Storm](https://storm.incubator.apache.org) är ett system för distribuerade beräkningar i realtid som förenklar tillförlitliga bearbetningen av unbounded dataströmmar. Det här avsnittet visar hur du använder en Azure Event Hubs Storm-kanal för att ta emot händelser från Event Hubs. Med Apache Storm kan du dela upp händelser över flera processer som ligger på olika noder. Händelsehubbar integrering med Storm förenklar händelsekonsumtion genom att transparent kontrollpunkter förloppet använder Storm's Zookeeper installation, hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs.

Mer information om Händelsehubbar får mönster, finns det [översikt av Händelsehubbar][Event Hubs overview].

## <a name="create-project-and-add-code"></a>Skapa projektet och Lägg till kod

Den här kursen använder en [HDInsight Storm] [ HDInsight Storm] installation som medföljer den Händelsehubbar kanal som redan finns.

1. Följ den [HDInsight Storm - komma igång](../hdinsight/storm/apache-storm-overview.md) proceduren för att skapa ett nytt HDInsight-kluster och ansluta till den via fjärrskrivbord.
2. Kopiera den `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` filen till din lokala utvecklingsmiljö. Innehåller händelser-storm-kanal.
3. Använd följande kommando för att installera paketet i det lokala arkivet Maven. På så sätt kan du lägga till den som en referens i projektet Storm i ett senare steg.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Skapa ett nytt Maven-projekt i Eclipse (klicka på **filen**, sedan **ny**, sedan **projekt**).
   
    ![][12]
5. Välj **använda standardplatsen för arbetsytan**, klicka på **nästa**
6. Välj den **maven-archetype-Snabbstart** archetype, klicka på **nästa**
7. Infoga en **GroupId** och **artefakt-ID**, klicka på **Slutför**
8. I **pom.xml**, Lägg till följande beroenden i den `<dependency>` nod.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. I den **src** mapp, skapa en fil med namnet **Config.properties** och kopiera följande innehåll, ersätter den `receive rule key` och `event hub name` värden:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Värdet för **eventhub.receiver.credits** avgör hur många händelser grupperas innan du lanserar Storm-pipeline. För enkelhetens skull anger det här exemplet du värdet till 10. I produktion sättas det vanligtvis till högre värden. till exempel 1024.
10. Skapa en ny klass med namnet **LoggerBolt** med följande kod:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Storm bulten loggar innehållet i de mottagna händelserna. Detta kan enkelt utökas för att lagra tupplar i en storage-tjänst. Den [HDInsight Storm med Event Hub exemplet] använder samma metod för att lagra data i Azure Storage och Power BI.
11. Skapa en klass med namnet **LogTopology** med följande kod:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Den här klassen skapas en ny kanal för Händelsehubbar, med hjälp av egenskaperna i konfigurationsfilen för att skapa en instans av den. Det är viktigt att Observera att det här exemplet skapar så många aktiviteter för kanaler som antalet partitioner i händelsehubben, för att kunna använda maximala parallellitet tillåts av den händelsehubben.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm med Event Hub exemplet]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
