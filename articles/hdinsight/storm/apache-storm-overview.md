---
title: Vad är Apache Storm – Azure HDInsight
description: Med Apache Storm kan du bearbeta dataströmmar i realtid. Med Azure HDInsight kan du enkelt skapa Storm-kluster på Azure-molnet. Med Visual Studio kan du skapa Storm-lösningar med hjälp av C# och sedan distribuera dem till dina HDInsight Storm-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm användarfall,stormkluster,vad är apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9c7e49fe522859f97f00f760822d5eef60db5f69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228862"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Vad är Apache Storm på Azure HDInsight?

[Apache Storm](https://storm.apache.org/) är ett distribuerat, feltolerant beräkningssystem med öppen källkod. You can use Storm to process streams of data in real time with [Apache Hadoop](https://hadoop.apache.org/). Storm-lösningar kan även ge garanterad bearbetning av data med möjlighet att på nytt spela upp data som inte bearbetades första gången.

## <a name="why-use-apache-storm-on-hdinsight"></a>Why use Apache Storm on HDInsight?

Storm på HDInsight har följande funktioner:

* __99 % serviceavtal (SLA) för Storm-drifttid__: Mer information finns i dokumentet [SLA information for HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) (SLA-information för HDInsight).

* Stöder enkel anpassning genom skriptkörning mot ett Storm-kluster när det skapas eller i efterhand. Mer information finns i [Customize HDInsight clusters using script action](../hdinsight-hadoop-customize-cluster-linux.md) (Anpassa HDInsight-kluster med skriptåtgärder).

* **Skapa lösningar på flera språk**: Du kan skriva Storm-komponenter på valfritt språk, till exempel Java, C# och Python.

    * Integrerar Visual Studio med HDInsight för utveckling, hantering och övervakning av C#-topologier. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

    * Stöder Java-gränssnittet Trident. Du kan skapa Storm-topologier som stöder engångsbearbetning av meddelanden, transaktionell datalagringsbeständighet och en uppsättning vanliga Stream Analytics-åtgärder.

* **Dynamisk skalning**: Du kan lägga till eller ta bort arbetsnoder utan att det påverkar de Storm-topologier som körs.

    * Du måste inaktivera och återaktivera topologier som körs för att kunna utnyttja de nya noderna som läggs till genom skalningsåtgärder.

* **Create streaming pipelines using multiple Azure services**: Storm on HDInsight integrates with other Azure services such as Event Hubs, SQL Database, Azure Storage, and Azure Data Lake Storage.

    For an example solution that integrates with Azure services, see [Process events from Event Hubs with Apache Storm on HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

En lista över företag som använder Apache Storm för sina lösningar för analys i realtid finns i [Företag som använder Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

To get started using Storm, see [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>How does Apache Storm work

Storm runs topologies instead of the [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  jobs that you might be familiar with. Storm-topologier består av flera komponenter som är ordnade i en riktad acyklisk graf (DAG). Data flödar mellan komponenter i diagrammet. Varje komponent använder en eller flera dataströmmar och kan du generera en eller flera strömmar. Följande diagram illustrerar hur data flödar mellan komponenterna i en grundläggande ordräkningstopologi:

![Exempel på hur komponenter är ordnade i en Storm-topologi](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Kanalkomponenter överför data till en topologi. De sänder en eller flera strömmar till topologin.

* Bultkomponenter förbrukar strömmar som sänts ut från kanaler eller andra bultar. Bultar kan eventuellt sända strömmar till topologin. Bultar ansvarar för att skriva data till extern lagring, till exempel HDFS, Kafka eller HBase.

## <a name="reliability"></a>Tillförlitlighet

Apache Storm ser till att varje inkommande meddelande alltid bearbetas helt, även om dataanalysen är utspridd över hundratals noder.

The Nimbus node provides functionality similar to the Apache Hadoop JobTracker, and it assigns tasks to other nodes in a cluster through [Apache ZooKeeper](https://zookeeper.apache.org/). Zookeeper-noder samordnar kluster och underlättar kommunikationen mellan Nimbus och övervakarprocessen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Apache Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar Storm-klustret till den sekundära noden medan den primära noden återställs. Följande diagram illustrerar konfigurationen av uppgiftsflödet för Storm i HDInsight:

![Diagram över nimbus och zookeeper och övervakaren](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Enkelt att skapa

Du kan skapa ett nytt Storm-kluster i HDInsight på några minuter. For more information on creating a Storm cluster, see [Create Apache Hadoop clusters using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Enkel användning

* __Secure Shell-anslutningar (SSH)__ : Du kan komma åt huvudnoderna i ditt Storm-kluster via Internet med SSH. Du kan köra kommandon direkt i klustret med hjälp av SSH.

  Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* __Webbanslutningar__: Alla HDInsight-kluster ger åtkomst till Ambari-webbgränssnittet. Du kan enkelt övervaka, konfigurera och hantera tjänster i klustret med hjälp av Ambari-webbanvändargränssnittet. Storm-kluster ger också Storm-användargränssnitt. Du kan övervaka och hantera Storm-topologier som körs från din webbläsare med hjälp av Storm-användargränssnittet.

  For more information, see the [Manage HDInsight using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) and [Monitor and manage using the Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) documents.

* __Azure PowerShell and Azure Classic CLI__: PowerShell and classic CLI both provide command-line utilities that you can use from your client system to work with HDInsight and other Azure services.

* __Visual Studio integration__: Azure Data Lake Tools for Visual Studio include project templates for creating C# Storm topologies by using the SCP.NET framework. Data Lake Tools innehåller också verktyg för att distribuera, övervaka och hantera lösningar med Storm i HDInsight.

  Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

## <a name="integration-with-other-azure-services"></a>Integration med andra Azure-tjänster

* __Azure Data Lake Storage__: For an example of using Data Lake Storage with a Storm cluster, see [Use Azure Data Lake Storage with Apache Storm on HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Ett exempel på hur du använder Event Hubs med Storm-kluster finns i följande exempel:

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ och __HBase__: Mallexempel ingår i Data Lake Tools för Visual Studio. For more information, see [Develop a C# topology for Apache Storm on HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Support

Storm i HDInsight levereras med fullständig, fortlöpande support på företagsnivå. Storm i HDInsight har även ett SLA med 99,9 % drifttid. Det innebär att Microsoft garanterar att ett Storm-kluster har en extern anslutning minst 99,9 procent av tiden.

Mer information finns på [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Apache Storm användningsområden

Nedan följer några vanliga scenarier där du kan använda Storm i HDInsight:

* Sakernas Internet (IoT)
* Upptäckt av bedrägerier
* Sociala analyser
* Extrahering, transformering och inläsning (ETL)
* Nätverksövervakning
* Sök
* Mobile engagement

For information about real-world scenarios, see the [How companies are using Apache Storm](https://storm.apache.org/Powered-By.html) document.

## <a name="development"></a>Utveckling

.NET-utvecklare kan utforma och implementera topologier i C# med hjälp av Data Lake Tools för Visual Studio. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter.

Mer information finns i [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Du kan också skapa Java-lösningar med hjälp av valfri IDE. For more information, see [Develop Java topologies for Apache Storm on HDInsight](apache-storm-develop-java-topology.md).

Du kan också utveckla Storm-komponenter med Python. For more information, see [Develop Apache Storm topologies using Python on HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Vanliga utvecklingsmönster

### <a name="guaranteed-message-processing"></a>Garanterad meddelandebehandling

Apache Storm kan ge olika nivåer av garanterad meddelandebehandling. For example, a basic Storm application can guarantee at-least-once processing, and [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) can guarantee exactly once processing.

Mer information finns i [Garantier om databearbetning](https://storm.apache.org/about/guarantees-data-processing.html) på apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

The pattern of reading an input tuple, emitting zero or more tuples, and then acknowledging the input tuple immediately at the end of the execute method is common. Storm innehåller ett [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html)-gränssnitt för att automatisera detta mönster.

### <a name="joins"></a>Kopplingar

Hur dataströmmar kopplas varierar mellan olika program. Du kan till exempel koppla samman varje tuppel från flera strömmar till en ny ström eller så kan du koppla bara batchar av tupplar för ett specifikt fönster. Oavsett vilket kan du ansluta med hjälp av [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Fältetgruppering är ett sätt att definiera hur tupplar skickas till bultar.

I Java-exemplet nedan används fieldsGrouping för att dirigera tupplar som kommer från komponenterna ”1”, ”2” och ”3” till bulten MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Batchar

Apache Storm tillhandahåller en intern tidsmekanism som kallas ”tidstuppel”. Du kan ange hur ofta en tidstuppel har genererats i topologin.

Ett exempel på hur du använder en tidstuppel från en C#-komponent finns i [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

Minnesintern cachelagring används ofta som en mekanism för snabbare bearbetning eftersom den bevarar ofta använda tillgångar i minnet. Eftersom en topologi distribueras över flera noder, och har flera processer i varje nod, bör du överväga att använda [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Använd `fieldsGrouping` för att se till att tupplar som innehåller fälten som används för cachesökning alltid dirigeras till samma process. Denna grupperingsfunktion förhindrar duplicering av cacheposter mellan processer.

### <a name="stream-top-n"></a>Dataström för ”högsta N”

Om topologin är beroende av att ett värde för ”högsta N” beräknas, bör du beräkna värdet för högsta N parallellt. Koppla sedan utdata från dessa beräkningar till ett globalt värde. Den här åtgärden kan utföras med hjälp av [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) för att ställa in fältruttning för parallell bearbetning. Du kan sedan vidarebefordra till en bult som globalt avgör det högsta N-värdet.

Ett exempel på en beräkning av ett ”högsta N”-värde finns i [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Loggning

Storm uses [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) to log information. Stora mängder data loggas som standard och det kan vara svårt att gå igenom informationen. Du kan ta med en konfigurationsfil för loggning som en del av Storm-topologin för att styra loggningsbeteendet.

En exempeltopologi som visar hur du konfigurerar loggning finns i ett exempel på [Java-baserad WordCount](apache-storm-develop-java-topology.md) för Storm på HDInsight.

## <a name="next-steps"></a>Nästa steg

Learn more about real-time analytics solutions with Apache Storm on HDInsight:

* [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md)
* [Exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md)
