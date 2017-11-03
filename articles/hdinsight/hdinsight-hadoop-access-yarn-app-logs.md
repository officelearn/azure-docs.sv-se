---
title: "Åtkomst Hadoop YARN programloggar programmässigt - Azure | Microsoft Docs"
description: "Programmet Access loggar programmässigt på ett Hadoop-kluster i HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 90323af4a1f4526ab9b26811c8679337076112d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Åtkomst programloggar YARN på Windows-baserade HDInsight
Det här avsnittet beskriver hur du kommer åt loggarna för YARN (ännu en annan resurs förhandlare)-program som har gått ut på en Windows-baserade Hadoop-kluster i Azure HDInsight

> [!IMPORTANT]
> Informationen i det här dokumentet gäller endast för Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). För information om hur du använder YARN loggar på Linux-baserade HDInsight-kluster, se [åtkomst YARN programloggar på Linux-baserade Hadoop i HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Krav
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

I HDInsight-kluster lagras informationen i Azure Resource Manager till en butik historik i standardbehållaren för ditt Azure Storage-standardkonto. Allmänna data på slutförda program kan hämtas via ett REST-API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN program och loggfiler
YARN stöder flera programmeringsmodeller (MapReduce är en av dem) genom Frikoppling resurshantering från schemaläggning/programövervakning. Detta görs via en global *ResourceManager* (RM) per arbetsnoden *NodeManagers* (NMs), och programspecifika *ApplicationMasters* (AMs). Programspecifika AM förhandlar resurser (processor, minne, disk, nätverk) för att köra programmet med RM. RM fungerar med NMs att bevilja dessa resurser, som beviljas som *behållare*. AM ansvarar för att spåra förloppet för de behållare som tilldelats av RM. Ett program kan kräva många behållare beroende på typ av programmet.

Dessutom kan varje program kan bestå av flera *program försöker* för att avsluta med krascher eller på grund av förlust av kommunikation mellan en AM och en RM. Därför måste beviljas behållare till en specifik försök av ett program. En behållare ger kontext för grundläggande enheten för arbete som utförs av en YARN-program i en mening och allt arbete som utförs inom ramen för en behållare utförs på enskild worker-nod där behållaren har tilldelats. Se [YARN begrepp] [ YARN-concepts] ytterligare referens.

Programloggarna (och associerade behållaren loggar) är kritiska felsöka problematiska Hadoop-program. YARN tillhandahåller ett bra ramverk för att samla in, sammanställa och lagra programloggarna med den [loggen aggregering] [ log-aggregation] funktion. Funktionen Log aggregering gör att nå programloggarna mer deterministisk aggregerar loggar över alla behållare på en arbetsnod och lagrar dem som en sammansatt loggfil per arbetsnoden på filsystemet när ett program har slutförts. Programmet kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod kommer alltid att slås samman till en fil, vilket resulterar i en loggfil per arbetsnoden som används av ditt program. Aggregering av loggen är aktiverad som standard i HDInsight-kluster (version 3.0 och senare), och aggregerade loggar finns i standardbehållaren på klustret på följande plats:

    wasb:///app-logs/<user>/logs/<applicationId>

På den platsen kan *användare* är namnet på den användare som startade programmet, och *applicationId* är den unika identifieraren för ett program som tilldelats av YARN RM.

Sammanställda loggar är inte direkt läsbar, som de är skrivna en [TFile][T-file], [binärformat] [ binary-format] indexeras av behållare. YARN tillhandahåller CLI-verktyg som dump loggarna som oformaterad text för program eller behållare av intresse. Du kan visa dessa loggar som oformaterad text genom att köra något av följande YARN kommandon direkt på klusternoderna (när du ansluter till den via RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager-Användargränssnittet
YARN ResourceManager-Användargränssnittet körs på klustret headnode och kan nås via Azure portal instrumentpanelen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på den vänstra menyn **Bläddra**, klickar du på **HDInsight-kluster**, klickar du på ett Windows-baserade kluster som du vill komma åt programmet YARN-loggarna.
3. Klicka på den översta menyn **instrumentpanelen**. Du ser en sida öppnas på en ny webbläsare flik med namnet **HDInsight frågan konsolen**.
4. Från **HDInsight frågan konsolen**, klickar du på **Yarn-Användargränssnittet**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
