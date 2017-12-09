---
title: "Felsöka Storm med Azure HDInsight | Microsoft Docs"
description: "Få svar på vanliga frågor om hur du använder Apache Storm med Azure HDInsight."
keywords: "Azure HDInsight, Storm, vanliga frågor och svar, felsökningsguide för vanliga problem"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 399ed17e997baf5dcf484f7798d3c4679522c633
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Felsöka Storm med Azure HDInsight

Läs mer om de vanligaste problemen och sina lösningar för att arbeta med Apache Storm-nyttolaster i Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hur kommer jag åt Storm-Användargränssnittet på ett kluster?
Har du två alternativ för att komma åt Storm-Användargränssnittet från en webbläsare:

### <a name="ambari-ui"></a>Ambari UI
1. Gå till instrumentpanelen Ambari.
2. Välj i listan över tjänster, **Storm**.
3. I den **snabblänkar** väljer du **Storm-Användargränssnittet**.

### <a name="direct-link"></a>Direktlänk
Du kan komma åt Storm-Användargränssnittet på följande URL:

https://\<klustrat DNS-namn\>/stormui

Exempel:

 https://stormcluster.azurehdinsight.NET/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hur överför Storm hubb kanal kontrollpunkt händelseinformation från en topologi till en annan?

När du utvecklar topologier som läses från Azure Event Hubs genom att använda HDInsight Storm event hub-kanalen .jar-fil, måste du distribuera en topologi som har samma namn på ett nytt kluster. Du måste dock behålla kontrollpunktsdata som har utförts till Apache ZooKeeper på det gamla klustret.

### <a name="where-checkpoint-data-is-stored"></a>Kontrollpunktsdata ska lagras
Kontrollpunktsdata för förskjutningarna lagras av event hub kanal i ZooKeeper i två rot-sökvägar:
- Icke-transaktionell kanal kontrollpunkter lagras i /eventhubspout.
- Transaktionell kanal kontrollpunktsdata lagras i / transaktionella.

### <a name="how-to-restore"></a>Så här återställer du
Skript och bibliotek som används för att exportera data från ZooKeeper och importera sedan data tillbaka till ZooKeeper med ett nytt namn finns [HDInsight Storm exempel](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Mappen lib har .jar-filer som innehåller implementeringen för exporten/importen. Bash-mappen innehåller ett exempelskript som visar hur du exporterar data från ZooKeeper-server på det gamla klustret och sedan importera den till ZooKeeper-server på det nya klustret.

Kör den [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skriptet från ZooKeeper-noder för att exportera och importera data. Uppdatera skriptet till rätt Hortonworks Data Platform (HDP) version. (Vi arbetar på att skripten generiska i HDInsight. Allmänt skript kan köras från en nod i klustret utan ändringar av användaren.)

Kommandot Exportera skriver metadata till en Apache Hadoop Distributed File System (HDFS)-sökväg (i Azure Blob Storage eller Azure Data Lake Store store) på en plats som du anger.

### <a name="examples"></a>Exempel

#### <a name="export-offset-metadata"></a>Exportera offset metadata
1. Använda SSH för att gå till ZooKeeper-kluster på klustret där kontrollpunkten förskjutning måste exporteras.
2. Kör följande kommando (när du har uppdaterat Versionsträngen HDP) om du vill exportera ZooKeeper offset data till /stormmetadta/zkdata HDFS-sökväg:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importera offset metadata
1. Använda SSH för att gå till ZooKeeper-kluster på klustret där kontrollpunkten förskjutning måste exporteras.
2. Kör följande kommando (när du har uppdaterat Versionsträngen HDP) för att importera ZooKeeper offset data från HDFS sökväg /stormmetadata/zkdata till målklustret ZooKeeper-servern:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Ta bort offset metadata så att topologier kan starta databearbetning från början, eller från en tidsstämpel som användaren väljer
1. Använda SSH för att gå till ZooKeeper-kluster på klustret där kontrollpunkten förskjutning måste exporteras.
2. Kör följande kommando (när du har uppdaterat Versionsträngen HDP) för att ta bort alla ZooKeeper offset data i det aktuella klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hur jag för att hitta Storm binärfiler i ett kluster?
Storm-binärfiler för den aktuella HDP-stacken är i /usr/hdp/current/storm-client. Platsen är samma både för huvudnoderna och arbetsnoder.
 
Det kan finnas flera binärfiler för specifika HDP versioner i /usr/hdp (till exempel /usr/hdp/2.5.0.1233/storm). Mappen /usr/hdp/current/storm-client är symlinked till den senaste versionen som körs på klustret.

Mer information finns i [Anslut till ett HDInsight-kluster med hjälp av SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hur vet topologi för distribution av ett Storm-kluster?
Först identifiera alla komponenter som installeras med Storm på HDInsight. Ett Storm-kluster består av fyra nod:

* Gateway-noder
* HEAD-noder
* ZooKeeper-noder
* Arbetsnoder
 
### <a name="gateway-nodes"></a>Gateway-noder
En gateway-noden är en gateway och en omvänd proxy-tjänst som gör offentlig åtkomst till en aktiv Ambari management-tjänsten. Den hanterar också Ambari ledande val.
 
### <a name="head-nodes"></a>HEAD-noder
Storm huvudnoderna kör följande tjänster:
* Nimbus
* Ambari-server
* Ambari mått server
* Ambari mått insamlaren
 
### <a name="zookeeper-nodes"></a>ZooKeeper-noder
HDInsight levereras med ett kvorum för ZooKeeper tre noder. Kvorum storleken är fast och kan inte konfigureras.
 
Storm-tjänster i klustret har konfigurerats för att automatiskt använda ZooKeeper kvorum.
 
### <a name="worker-nodes"></a>Arbetsnoder
Storm arbetarnoder kör följande tjänster:
* Överordnad
* Worker Java virtuella datorer (JVMs) för topologier som körs
* Ambari-agent
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hur jag för att hitta Storm event hub kanal binärfiler för utveckling?
 
Mer information om hur du använder Storm event hub kanal .jar filer med din topologi finns i följande resurser.
 
### <a name="java-based-topology"></a>Java-baserad topologi
[Bearbeta händelser från Azure Event Hubs med Storm på HDInsight (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-baserade topologi (Mono på HDInsight 3.4 + Linux Storm-kluster)
[Process events from Azure Event Hubs with Storm on HDInsight (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (C#))
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Senaste Storm event hub-kanalen binärfiler för HDInsight 3.5 + Linux Storm-kluster
Information om hur du använder den senaste Storm event hub-kanal som fungerar med HDInsight 3.5 + Linux Storm-kluster finns mvn-lagringsplatsen [Readme-filen](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Käll-kodexempel
Se [exempel](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) på hur du kan läsa och skriva från Azure Event Hub med en Apache Storm-topologi (skriven i Java) på ett Azure HDInsight-kluster.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Hur jag för att hitta Storm Log4J konfigurationsfiler på kluster?
 
Identifiera Apache Log4J konfigurationsfiler för Storm-tjänster.
 
### <a name="on-head-nodes"></a>På huvudnoderna
Nimbus Log4J konfigurationen läses från/usr/hdp/\<HDP version\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>På arbetsnoder
Konfigurationen av handledarens Log4J läses från/usr/hdp/\<HDP version\>/storm/log4j2/cluster.xml.
 
Worker Log4J-konfigurationsfilen har lästs från/usr/hdp/\<HDP version\>/storm/log4j2/worker.xml.
 
Exempel: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)