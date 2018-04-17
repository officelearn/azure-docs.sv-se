---
title: Åtkomst Hadoop YARN programloggar programmässigt - Azure | Microsoft Docs
description: Programmet Access loggar programmässigt på ett Hadoop-kluster i HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: aab7865548c034cb550874c31977b05936dc45b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Åtkomst programloggar YARN på Windows-baserade HDInsight
Det här dokumentet förklarar hur du kommer åt loggarna för YARN-program som har gått ut på en Windows-baserade Hadoop-kluster i Azure HDInsight

> [!IMPORTANT]
> Informationen i det här dokumentet gäller endast för Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). För information om hur du använder YARN loggar på Linux-baserade HDInsight-kluster, se [åtkomst YARN programloggar på Linux-baserade Hadoop i HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Förutsättningar
* Ett Windows-baserade HDInsight-kluster.  Se [skapa Windows-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN tidslinjen Server
Den <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN tidslinjen Server</a> innehåller allmän information om slutförda program samt som framework-specifik information om programmet via två olika gränssnitt. Närmare bestämt:

* Lagring och hämtning av information för Allmänt program på HDInsight-kluster har aktiverat med version 3.1.1.374 eller högre.
* Framework-specifik information PROGRAMKOMPONENTEN tidslinjen Server är inte tillgänglig på HDInsight-kluster.

Allmän information om program innehåller följande typer av data:

* Program-ID, en unik identifierare för ett program
* Den användare som startade programmet
* Information om försök görs att slutföra programmet
* De behållare som används av ett visst program försök

Den här informationen lagras i HDInsight-kluster i Azure Resource Manager. Informationen sparas i historiken arkivet i standardlagring för klustret. Allmänna data på slutförda program kan hämtas via ett REST-API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN program och loggfiler
YARN stöder flera programmeringsmodeller genom Frikoppling resurshantering från schemaläggning/programövervakning. YARN använder en global *ResourceManager* (RM) per arbetsnoden *NodeManagers* (NMs), och programspecifika *ApplicationMasters* (AMs). Programspecifika AM förhandlar resurser (processor, minne, disk, nätverk) för att köra programmet med RM. RM fungerar med NMs att bevilja dessa resurser, som beviljas som *behållare*. AM ansvarar för att spåra förloppet för de behållare som tilldelats av RM. Ett program kan kräva många behållare beroende på typ av programmet.

* Varje program kan bestå av flera *program försöker*. 
* Behållare beviljas till en specifik försök av ett program. 
* En behållare innehåller kontexten för en grundläggande arbetsenhet. 
* Arbete som utförs inom ramen för en behållare har utförts på enskild arbetsnoden som behållaren har tilldelats. 

Mer information finns i [YARN begrepp][YARN-concepts].

Programloggarna (och associerade behållaren loggar) är kritiska felsöka problematiska Hadoop-program. YARN tillhandahåller ett bra ramverk för att samla in, sammanställa och lagra programloggarna med den [loggen aggregering] [ log-aggregation] funktion. Funktionen Log aggregering gör att nå programloggarna mer deterministisk aggregerar loggar över alla behållare på en arbetsnod och lagrar dem som en sammansatt loggfil per arbetsnoden på filsystemet när ett program har slutförts. Programmet kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod sammanställs till en fil, vilket resulterar i en fil per arbetsnoden som används av ditt program. Aggregering av loggen är aktiverad som standard i HDInsight-kluster (version 3.0 och senare), och aggregerade loggar finns i standardbehållaren på klustret på följande plats:

    wasb:///app-logs/<user>/logs/<applicationId>

På den platsen kan *användare* är namnet på den användare som startade programmet, och *applicationId* är den unika identifieraren för ett program som tilldelats av YARN RM.

Sammanställda loggar är inte direkt läsbar, som de är skrivna en [TFile][T-file], [binärformat] [ binary-format] indexeras av behållare. YARN tillhandahåller CLI-verktyg som dump loggarna som oformaterad text för program eller behållare av intresse. Du kan visa dessa loggar som oformaterad text genom att köra något av följande YARN kommandon direkt på klusternoderna (när du ansluter till den via RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager-Användargränssnittet
YARN ResourceManager-Användargränssnittet körs på klustret headnode och kan nås via Azure portal instrumentpanelen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på den vänstra menyn **Bläddra**, klickar du på **HDInsight-kluster**, klickar du på ett Windows-baserade kluster som du vill komma åt programmet YARN-loggarna.
3. Klicka på den översta menyn **instrumentpanelen**. En sida öppnas på en ny webbläsare flik med namnet **HDInsight frågan konsolen**.
4. Från **HDInsight frågan konsolen**, klickar du på **Yarn-Användargränssnittet**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
