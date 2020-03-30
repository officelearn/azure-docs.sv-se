---
title: Felsöka Storm med hjälp av Azure HDInsight
description: Få svar på vanliga frågor om hur du använder Apache Storm med Azure HDInsight.
keywords: Azure HDInsight, Storm, Vanliga frågor och svar, felsökningsguide, vanliga problem
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271933"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Felsöka Apache Storm med hjälp av Azure HDInsight

Lär dig mer om de viktigaste problemen och deras lösningar för att arbeta med [Apache Storm](https://storm.apache.org/) nyttolaster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hur kommer jag åt stormgränssnittet i ett kluster?

Du har två alternativ för att komma åt stormgränssnittet från en webbläsare:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Gå till Ambari-instrumentpanelen.
2. Välj **Storm**i listan över tjänster .
3. Välj **Stormgränssnitt**på **snabblänksmenyn** .

### <a name="direct-link"></a>Direkt länk

Du kan komma åt användargränssnittet Storm på följande WEBBADRESS:

`https://<cluster DNS name>/stormui`

Exempel: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hur överför jag information om stormhändelsehubbens pipkontrollpunkt från en topologi till en annan?

När du utvecklar topologier som läses från Azure Event Hubs med hjälp av HDInsight Storm-händelsehubbens pip.jar-fil, måste du distribuera en topologi som har samma namn i ett nytt kluster. Du måste dock behålla kontrollpunktsdata som har åtagits [apache zookeeper](https://zookeeper.apache.org/) på det gamla klustret.

### <a name="where-checkpoint-data-is-stored"></a>Var kontrollpunktsdata lagras

Kontrollpunktsdata för förskjutningar lagras av händelsehubbutloppet i ZooKeeper i två rotsökvägar:

- Kontrollpunkter för icke-transaktionell pip `/eventhubspout`lagras i .

- Transaktionspipkontrollpunktsdata lagras `/transactional`i .

### <a name="how-to-restore"></a>Så här återställer du

Information om hur du hämtar skript och bibliotek som du använder för att exportera data från ZooKeeper och sedan importerar data tillbaka till ZooKeeper med ett nytt namn finns i [EXEMPEL på HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib-mappen har .jar-filer som innehåller implementeringen för export-/importåtgärden. Bash-mappen har ett exempelskript som visar hur du exporterar data från ZooKeeper-servern i det gamla klustret och sedan importerar tillbaka den till ZooKeeper-servern i det nya klustret.

Kör [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skriptet från ZooKeeper-noderna för att exportera och importera sedan data. Uppdatera skriptet till rätt Hortonworks Data Platform (HDP) version. (Vi arbetar på att göra dessa skript generiska i HDInsight. Allmänna skript kan köras från valfri nod i klustret utan ändringar av användaren.)

Exportkommandot skriver metadata till en HDFS-sökväg (Apache Hadoop Distributed File System) (i Azure Blob Storage eller Azure Data Lake Storage) på en plats som du anger.

### <a name="examples"></a>Exempel

#### <a name="export-offset-metadata"></a>Exportera offsetmetadata

1. Använd SSH för att gå till ZooKeeper-klustret i klustret som kontrollpunktsförskjutningen måste exporteras från.
2. Kör följande kommando (när du har uppdaterat HDP-versionssträngen) `/stormmetadta/zkdata` för att exportera ZooKeeper-förskjutningsdata till HDFS-sökvägen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importera offsetmetadata

1. Använd SSH för att gå till ZooKeeper-klustret i klustret som kontrollpunktsförskjutningen måste importeras från.
2. Kör följande kommando (när du har uppdaterat HDP-versionssträngen) för `/stormmetadata/zkdata` att importera ZooKeeper-förskjutningsdata från HDFS-sökvägen till ZooKeeper-servern i målklustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Ta bort offsetmetadata så att topologier kan börja bearbeta data från början, eller från en tidsstämpel som användaren väljer

1. Använd SSH för att gå till ZooKeeper-klustret i klustret där kontrollpunktsförskjutningen måste tas bort.
2. Kör följande kommando (när du har uppdaterat HDP-versionssträngen) om du vill ta bort alla ZooKeeper-förskjutningsdata i det aktuella klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hur hittar jag Storm-binärfiler i ett kluster?

Stormbinärer för den aktuella `/usr/hdp/current/storm-client`HDP-stacken finns i . Platsen är densamma både för huvudnoder och för arbetsnoder.

Det kan finnas flera binärfiler för specifika HDP-versioner i `/usr/hdp/2.5.0.1233/storm`/usr/hdp (till exempel). Mappen `/usr/hdp/current/storm-client` är symlinked till den senaste versionen som körs på klustret.

Mer information finns i [Anslut till ett HDInsight-kluster med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hur avgör jag distributionstopologin för ett Storm-kluster?

Identifiera först alla komponenter som är installerade med HDInsight Storm. Ett Storm-kluster består av fyra nodkategorier:

* Gateway-noder
* Huvudnoder
* ZooKeeper-noder
* Arbetsnoder

### <a name="gateway-nodes"></a>Gateway-noder

En gateway-nod är en gateway- och reverse proxytjänst som möjliggör offentlig åtkomst till en aktiv Ambari-hanteringstjänst. Det hanterar också Ambari ledare val.

### <a name="head-nodes"></a>Huvudnoder

Stormhuvudnoder kör följande tjänster:
* Nimbus
* Ambari server
* Ambari Metrics server
* Ambari Statistik Collector
 
### <a name="zookeeper-nodes"></a>ZooKeeper-noder

HDInsight levereras med en tre-nod ZooKeeper kvorum. Kvorumstorleken är fast och kan inte konfigureras om.

Stormtjänster i klustret är konfigurerade för att automatiskt använda ZooKeeper-kvorumet.

### <a name="worker-nodes"></a>Arbetsnoder

Stormarbetarenoder kör följande tjänster:
* Chef
* Virtuella arbetsdatorer för Arbetare java (JVMs), för att köra topologier
* Ambari agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hur hittar jag Storm-händelsehubbpip-binärer för utveckling?

Mer information om hur du använder Storm-händelsehubbens pip.jar-filer med topologin finns i följande resurser.

### <a name="java-based-topology"></a>Java-baserad topologi

[Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-baserad topologi (Mono på HDInsight 3,4+ Linux Storm-kluster)

[Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Senaste Apache Storm händelse nav pip binärer för HDInsight 3,5 + Linux Storm kluster

Mer information om hur du använder den senaste Storm-händelsehubben som fungerar med HDInsight 3.5+ Linux Storm-kluster finns i [filen mvn-repo readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Exempel på källkod

Se [exempel på](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) hur du läser och skriver från Azure Event Hub med hjälp av en Apache Storm-topologi (skriven i Java) på ett Azure HDInsight-kluster.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hur hittar jag Storm Log4J 2-konfigurationsfiler i kluster?

Så här identifierar du konfigurationsfiler för [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) för Storm-tjänster.

### <a name="on-head-nodes"></a>På huvudnoder

Nimbus Log4J-konfigurationen `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`läss från .

### <a name="on-worker-nodes"></a>På arbetsnoder

Administratören Log4J-konfigurationen `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`läss från .

Konfigurationsfilen för worker Log4J läss från `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Exempel:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Inte ett ledareundantag

När du skickar en topologi kan användaren få `Topology submission exception, cause not a leader, the current leader is NimbusInfo`ett felmeddelande som liknar: .

För att lösa kan användaren behöva lämna in en biljett för att få noderna startas om/startas om. Mer information finns [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)i .

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
