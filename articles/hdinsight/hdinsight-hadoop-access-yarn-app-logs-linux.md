---
title: Få tillgång till Apache Hadoop YARN-programloggar - Azure HDInsight
description: Lär dig hur du kommer åt YARN-programloggar på ett Linux-baserat HDInsight-kluster (Apache Hadoop) med både kommandoraden och en webbläsare.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764382"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Få tillgång till Apache Hadoop YARN-programloggar på Linux-baserade HDInsight

Lär dig hur du kommer åt loggarna för [Apache Hadoop YARN-program](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ännu en resursförhandlare) i ett [Apache Hadoop-kluster](https://hadoop.apache.org/) i Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Vad är Apache YARN?

YARN stöder flera programmeringsmodeller[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) är en av dem) genom att frikoppla resurshantering från programplanering/övervakning. YARN använder en global *ResourceManager* (RM), *nodmandagers* (NMs) per arbetare och *programansvariga* (AMs). Enligt AM per program förhandlar resurser (CPU, minne, disk, nätverk) för att köra programmet med RM. RM arbetar med NMs att bevilja dessa resurser, som beviljas som *behållare*. Am ansvarar för att spåra förloppet för de behållare som tilldelats den av RM. Ett program kan kräva många behållare beroende på programmets art.

Varje program kan bestå av flera *programförsök*. Om ett program misslyckas kan det göras om som ett nytt försök. Varje försök körs i en behållare. På sätt och vis ger en behållare kontexten för grundläggande arbetsenhet som utförs av ett YARN-program. Allt arbete som utförs inom ramen för en behållare utförs på den enda arbetarnod som behållaren allokerades på. Se [Hadoop: Skriva GARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), eller Apache [Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) för ytterligare referens.

Om du vill skala klustret så att det stöder större bearbetningsdataflöde kan du använda [Automatisk skalning](hdinsight-autoscale-clusters.md) eller [Skala klustren manuellt med några olika språk](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>GARN Tidslinje Server

[Apache Hadoop YARN Tidslinje Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) ger allmän information om slutförda program

YARN Tidslinje Server innehåller följande typ av data:

* Ansökans-ID, en unik identifierare för ett program
* Användaren som startade programmet
* Information om försök som gjorts att slutföra ansökan
* Behållarna som används av ett givet programförsök

## <a name="yarn-applications-and-logs"></a>YARN-applikationer och stockar

YARN stöder flera programmeringsmodeller[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) är en av dem) genom att frikoppla resurshantering från programplanering/övervakning. YARN använder en global *ResourceManager* (RM), *nodmandagers* (NMs) per arbetare och *programansvariga* (AMs). Enligt AM per program förhandlar resurser (CPU, minne, disk, nätverk) för att köra programmet med RM. RM arbetar med NMs att bevilja dessa resurser, som beviljas som *behållare*. Am ansvarar för att spåra förloppet för de behållare som tilldelats den av RM. Ett program kan kräva många behållare beroende på programmets art.

Varje program kan bestå av flera *programförsök*. Om ett program misslyckas kan det göras om som ett nytt försök. Varje försök körs i en behållare. På sätt och vis ger en behållare kontexten för grundläggande arbetsenhet som utförs av ett YARN-program. Allt arbete som utförs inom ramen för en behållare utförs på den enda arbetarnod som behållaren allokerades på. Se [Apache Hadoop YARN Begrepp](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) för ytterligare referens.

Programloggar (och tillhörande behållarloggar) är viktiga för felsökning av problematiska Hadoop-program. YARN ger ett trevligt ramverk för insamling, aggregering och lagring av programloggar med [funktionen Logga aggregering.](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) Funktionen Logga aggregering gör åtkomst till programloggar mer deterministiska. Den sammanställer loggar över alla behållare på en arbetsnod och lagrar dem som en aggregerad loggfil per arbetsnod. Loggen lagras i standardfilsystemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod aggregeras alltid till en enda fil. Så det finns bara en logg per arbetsnod som används av ditt program. Loggaggregering är aktiverad som standard på HDInsight-kluster version 3.0 och senare. Aggregerade loggar finns i standardlagring för klustret. Följande sökväg är HDFS-sökvägen till loggarna:

```
/app-logs/<user>/logs/<applicationId>
```

I sökvägen `user` är namnet på den användare som startade programmet. Den `applicationId` unika identifierare som tilldelats ett program av YARN RM.

De aggregerade loggarna är inte direkt läsbara, eftersom de är skrivna i en [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binärt format](https://issues.apache.org/jira/browse/HADOOP-3315) indexerat efter behållare. Använd YARN ResourceManager-loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Garnloggar i ett ESP-kluster

Två konfigurationer måste läggas `mapred-site` till i anpassade i Ambari.

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Från Ambari UI navigerar du till **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

1. Lägg till *någon* av följande egenskaper:

    **Ställ in 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Ställ in 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Spara ändringar och starta om alla tjänster som påverkas.

## <a name="yarn-cli-tools"></a>YARN CLI-verktyg

1. Använd kommandot ssh för att ansluta till [klustret.](./hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Lista alla program-ID:er för de garnprogram som körs med följande kommando:

    ```bash
    yarn top
    ```

    Observera program-ID `APPLICATIONID` från kolumnen vars loggar ska laddas ner.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Du kan visa dessa loggar som oformaterad text genom att köra något av följande kommandon:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Ange &lt;applicationId->, &lt;användar-vem-startade-programmet>, &lt;containerId> och &lt;arbetar-nod-adress> information när du kör dessa kommandon.

### <a name="other-sample-commands"></a>Andra exempelkommandon

1. Ladda ner Yarn behållare loggar för alla program mästare med kommandot nedan. Då skapas loggfilen `amlogs.txt` med namnet textformat.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Hämta Garn-behållarloggar för endast den senaste programhanteraren med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Hämta YARN-behållarloggar för de två första programbakgrunderna med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Ladda ner alla Yarn containerloggar med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Hämta garnbehållareloggen för en viss behållare med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>GARN ResourceManager UI

UI-användargränssnittet YARN ResourceManager körs på klusterhuvudnoden. Den nås via Ambari webbgränssnittet. Gör så här för att visa YARN-loggarna:

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net`i webbläsaren . Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.

2. Välj **YARN**i listan över tjänster till vänster .

    ![Apache Ambari Garn tjänst vald](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Välj en av klusterhuvudnoderna i listrutan **Snabblänkar** och välj sedan **ResourceManager Log**.

    ![Apache Ambari Garn snabblänkar](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Du presenteras med en lista med länkar till YARN loggar.
