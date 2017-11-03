---
title: "Åtkomst Hadoop YARN programloggar på Linux-baserade HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du kommer åt YARN programloggar på en Linux-baserat HDInsight (Hadoop)-kluster med hjälp av kommandoraden och en webbläsare."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: eea30e60a793563fbde96d11ab4bdb2d01241e60
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Åtkomst programloggar YARN på Linux-baserat HDInsight

Lär dig hur du kommer åt loggarna för YARN (ännu en annan resurs förhandlare) program på ett Hadoop-kluster i Azure HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight component-versioning](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN tidslinjen Server

Den [YARN tidslinjen Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) ger allmän information om slutförda program och programinformation om framework-specifika via två olika gränssnitt. Närmare bestämt:

* Lagring och hämtning av information för Allmänt program på HDInsight-kluster har aktiverat med version 3.1.1.374 eller högre.
* Framework-specifik information PROGRAMKOMPONENTEN tidslinjen Server är inte tillgänglig på HDInsight-kluster.

Allmän information om program innehåller följande typ av data:

* Program-ID, en unik identifierare för ett program
* Den användare som startade programmet
* Information om försök görs att slutföra programmet
* De behållare som används av ett visst program försök

## <a name="YARNAppsAndLogs"></a>YARN program och loggfiler

YARN stöder flera programmeringsmodeller (MapReduce är en av dem) genom Frikoppling resurshantering från schemaläggning/programövervakning. YARN använder en global *ResourceManager* (RM) per arbetsnoden *NodeManagers* (NMs), och programspecifika *ApplicationMasters* (AMs). Programspecifika AM förhandlar resurser (processor, minne, disk, nätverk) för att köra programmet med RM. RM fungerar med NMs att bevilja dessa resurser, som beviljas som *behållare*. AM ansvarar för att spåra förloppet för de behållare som tilldelats av RM. Ett program kan kräva många behållare beroende på typ av programmet.

Varje program kan bestå av flera *program försöker*. Om ett program inte kan du göra det som ett nytt försök. Varje försök körs i en behållare. I en mening ger en behållare kontext för grundläggande enheten för arbete som utförs av en YARN-programmet. Allt arbete som utförs inom ramen för en behållare har utförts på enskild arbetsnoden som behållaren har tilldelats. Se [YARN begrepp] [ YARN-concepts] ytterligare referens.

Programloggarna (och associerade behållaren loggar) är kritiska felsöka problematiska Hadoop-program. YARN tillhandahåller ett bra ramverk för att samla in, sammanställa och lagra programloggarna med den [loggen aggregering] [ log-aggregation] funktion. Funktionen Log aggregering gör att nå programloggarna mer deterministisk. Den sammanställer loggar över alla behållare på en arbetsnod och lagrar dem som en sammansatt loggfil per arbetsnoden. Loggen är lagrade på filsystemet när ett program har slutförts. Programmet kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod aggregeras alltid till en fil. Det är därför bara 1 loggen per arbetsnoden som används av ditt program. Aggregering av loggen är aktiverad som standard på HDInsight-kluster av version 3.0 och senare. Sammanställda loggarna finns i standardlagring för klustret. Följande är HDFS-sökvägen till loggar:

    /app-logs/<user>/logs/<applicationId>

I sökvägen `user` är namnet på den användare som startade programmet. Den `applicationId` är den unika identifieraren som tilldelats till ett program av YARN RM.

Sammanställda loggar är inte direkt läsbar, som de är skrivna en [TFile][T-file], [binärformat] [ binary-format] indexeras av behållare. Använda YARN ResourceManager loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

## <a name="yarn-cli-tools"></a>YARN CLI-verktygen

Om du vill använda YARN CLI-verktygen måste du först ansluta till HDInsight-klustret via SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Du kan visa dessa loggar som oformaterad text genom att köra något av följande kommandon:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Ange den &lt;applicationId >, &lt;användaren som-igång-program >, &lt;behållar-ID >, och &lt;worker-nod-adress > information när du kör kommandona.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager-Användargränssnittet

YARN ResourceManager-Användargränssnittet körs på klustret headnode. Den kan nås via Ambari-webbgränssnittet. Använd följande steg om du vill visa YARN-loggar:

1. Navigera till https://CLUSTERNAME.azurehdinsight.net i din webbläsare. Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.
2. Listan över tjänster till vänster och välj **YARN**.

    ![Yarn-tjänsten som markerats](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Från den **snabblänkar** listrutan, Välj en av klusternoderna head och välj sedan **ResourceManager loggen**.

    ![Yarn snabblänkar](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Visas en lista med länkar till YARN-loggar.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
