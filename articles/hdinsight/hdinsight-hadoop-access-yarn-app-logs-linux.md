---
title: Åtkomst Apache Hadoop garn program loggar på Linux-baserade HDInsight – Azure
description: Lär dig hur du kommer åt garn program loggar i ett Linux-baserat HDInsight-kluster (Apache Hadoop) med både kommando raden och en webbläsare.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 2b230f91b9d6b169b89b125bdd0394c2c7ecd96f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879860"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Åtkomst Apache Hadoop garn program loggar på Linux-baserade HDInsight

Lär dig hur du kommer åt loggarna för [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ännu andra resurs Negotiator) i ett [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight.

## <a name="YARNTimelineServer"></a>GARN tids linje server

[Apache HADOOP garn tids linje server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) innehåller allmän information om slutförda program

GARN tids linje server innehåller följande typ av data:

* Program-ID, en unik identifierare för ett program
* Användaren som startade programmet
* Information om försök som gjorts för att slutföra programmet
* De behållare som används av ett angivet program försök

## <a name="YARNAppsAndLogs"></a>GARN program och loggar

GARN stöder flera programmerings modeller ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) är en av dem) genom att koppla från resurs hantering från program schemaläggning/övervakning. GARN använder en global *ResourceManager* (RM), per Work-Node- *) nodemanagers* (NMS) och per-program- *ApplicationMasters* (AMS). Per program förhandlar om resurser (CPU, minne, disk, nätverk) för att köra ditt program med RM. RM fungerar med NMs för att tilldela dessa resurser, vilka beviljas som *behållare*. FM ansvarar för att spåra förloppet för de behållare som tilldelas den av RM. Ett program kan kräva många behållare beroende på programmets beskaffenhet.

Varje program kan bestå av flera *program försök*. Om ett program Miss lyckas kan det göras ett nytt försök. Varje försök körs i en behållare. I en mening tillhandahåller en behållare kontexten för grundläggande arbets enheter som utförs av ett garn program. Allt arbete som utförs inom ramen för en behållare utförs på den enskild arbetsnoden där behållaren allokerades. Se [Apache HADOOP garn begrepp](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) för ytterligare referens.

Program loggar (och tillhör ande behållar loggar) är viktiga vid fel sökning av problematiska Hadoop-program. GARN är ett bra ramverk för att samla in, aggregera och lagra program loggar med funktionen för [logg agg regering](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . Funktionen för insamling av loggar ger åtkomst till program loggar mer deterministisk. Den sammanställer loggar över alla behållare på en arbetsnoden och lagrar dem som en sammanställd loggfil per arbets nod. Loggen lagras i standard fil systemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnoden sammanställs alltid i en enda fil. Det finns därför bara 1 logg per arbetsnoden som används av ditt program. Logg agg regering är aktiverat som standard i HDInsight-kluster version 3,0 och senare. Sammanställda loggar finns i standard lagrings utrymmet för klustret. Följande sökväg är HDFS-sökvägen till loggarna:

    /app-logs/<user>/logs/<applicationId>

I sökvägen `user` är namnet på den användare som startade programmet. `applicationId` Är den unika identifierare som tilldelas ett program av garn RM.

De sammanställda loggarna kan inte läsas direkt, eftersom de skrivs i ett [TFile][T-file], [binärt format][binary-format] som indexeras av container. Använd garn-ResourceManager-loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

## <a name="yarn-cli-tools"></a>GARN CLI-verktyg

Om du vill använda garn CLI-verktygen måste du först ansluta till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Du kan visa dessa loggar som oformaterad text genom att köra något av följande kommandon:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Ange applicationId->, &lt;användare-som-startat-program >, &lt;containerId > och &lt;Work-Node-address > information när du kör dessa kommandon. &lt;

## <a name="yarn-resourcemanager-ui"></a>GARN-ResourceManager-gränssnitt

Användar gränssnittet för garn-ResourceManager körs på klustrets huvudnoden. Den nås via Ambari-webbgränssnittet. Använd följande steg för att Visa garn loggarna:

1. Navigera till https://CLUSTERNAME.azurehdinsight.net i webbläsaren. Ersätt kluster namn med namnet på ditt HDInsight-kluster.
2. I listan över tjänster till vänster väljer du **garn**.

    ![Garn tjänst vald](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. I list rutan **snabb länkar** väljer du en av klustrets huvud-noder och väljer sedan **ResourceManager-logg**.

    ![Snabb Länkar för garn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    En lista med länkar till garn loggar visas.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
