---
title: Åtkomst Apache Hadoop YARN-programloggar på Linux-baserade HDInsight - Azure
description: Lär dig mer om att få åtkomst till YARN-programloggar i ett kluster för Linux-baserat HDInsight (Apache Hadoop) med hjälp av kommandoraden och en webbläsare.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 302f2f96a7f17699411ab9fdbdb6ab1f9de149c8
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277608"
---
# <a name="access-apache-yarn-application-logs-on-linux-based-hdinsight"></a>Åtkomst Apache YARN-programloggar på Linux-baserat HDInsight

Lär dig hur du kommer åt loggarna för Apache YARN (ännu en annan Resource Negotiator)-program på ett Apache Hadoop-kluster i Azure HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystem som används på HDInsight version 3.6 eller senare. Mer information finns i [versionshantering för HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

Den [Apache YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) visar allmän information om slutförda program

YARN Timeline Server innehåller följande typ av data:

* Program-ID, ett unikt ID för ett program
* Den användare som startade programmet
* Information om försök görs att slutföra programmet
* Behållare som används av alla program-försök

## <a name="YARNAppsAndLogs"></a>YARN-program och loggfiler

YARN stöder flera programmeringsmodeller (MapReduce som en av dem) genom Frikoppling resurshantering från schemaläggning/programövervakning. YARN använder en global *ResourceManager* (RM) per arbetsnod *NodeManagers* (NMs), och programspecifika *ApplicationMasters* (AMs). Programspecifika AM förhandlar resurser (processor, minne, disk, nätverk) för att köra ditt program med RM. RM fungerar med NMs att bevilja dessa resurser, som beviljas som *behållare*. AM ansvarar för att spåra förloppet för de behållare som tilldelats av RM. Ett program kan kräva många behållare beroende på typen av programmet.

Varje program kan bestå av flera *program försök*. Om ett program inte kan skickas igen som ett nytt försök. Varje försök körs i en behållare. I en mening erbjuder ger kontext för grundläggande enheten för arbete som utförs av en YARN-program i en behållare. Allt arbete som görs inom ramen för en behållare utförs på enskild worker-noden som behållaren allokerades. Se [YARN begrepp] [ YARN-concepts] ytterligare referens.

Programloggar (och associerade behållarloggarna) är avgörande felsöker problematiska Hadoop-program. YARN tillhandahåller ett ramverk som är bra för att samla in, sammanställa och lagra programloggar med den [Log aggregering] [ log-aggregation] funktionen. Funktionen Log aggregering gör åtkomst till programloggarna mer deterministisk. Den sammanställer loggar över alla behållare på en underordnad nod och lagrar dem som en sammansatt loggfil per arbetsnod. Loggen lagras på standardfilsystemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod alltid räknas avgifter för en enskild fil. Så finns det bara 1 log per arbetsnod som används av ditt program. Log aggregering är aktiverat som standard på HDInsight-kluster version 3.0 och senare. Sammanställda loggfiler finns i Standardlagringsutrymmet för klustret. Följande är HDFS-sökvägen till loggarna:

    /app-logs/<user>/logs/<applicationId>

I sökvägen, `user` är namnet på den användare som startade programmet. Den `applicationId` är den unika identifieraren som tilldelats till ett program med YARN-RM.

Sammanställda loggfiler kan inte direkt läsas som de är skrivna en [TFile][T-file], [binärformat] [ binary-format] indexeras av behållare. Använda loggar för YARN ResourceManager eller CLI-verktyg för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

## <a name="yarn-cli-tools"></a>YARN CLI-verktyg

Om du vill använda YARN CLI-verktyg, måste du först ansluta till HDInsight-kluster med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Du kan visa dessa loggar som oformaterad text genom att köra ett av följande kommandon:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Ange den &lt;applicationId >, &lt;användaren som-igång-program >, &lt;behållar-ID >, och &lt;worker-nod-adress > information när du kör dessa kommandon.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI körs på klustrets huvudnod. Den kan nås via Ambari-webbgränssnittet. Använd följande steg för att visa YARN-loggar:

1. I din webbläsare, navigerar du till https://CLUSTERNAME.azurehdinsight.net. Ersätt CLUSTERNAME med namnet på ditt HDInsight-kluster.
2. Från listan över tjänster till vänster, Välj **YARN**.

    ![Yarn-tjänsten som valts](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Från den **snabblänkar** listrutan väljer du något av klustrets huvudnoder och välj sedan **ResourceManager Log**.

    ![Snabblänkar för yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Visas en lista med länkar till YARN-loggar.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
