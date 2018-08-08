---
title: Åtkomst till Hadoop YARN-programloggar via programmering - Azure
description: Få åtkomst till-programloggar som programmässigt på ett Hadoop-kluster i HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 42484f2a93ab5effdcafca0f0769c3fb4cdbb926
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600191"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Åtkomst till YARN-programloggar på Windows-baserade HDInsight
Det här dokumentet beskriver hur du kommer åt loggar för YARN-program som har gått ut på en Windows-baserade Hadoop-kluster i Azure HDInsight

> [!IMPORTANT]
> Informationen i det här dokumentet gäller endast för Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om åtkomst till YARN-loggar i Linux-baserade HDInsight-kluster finns i [åtkomst till YARN-programloggar på Linux-baserat Hadoop i HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Förutsättningar
* Ett Windows-baserade HDInsight-kluster.  Se [skapa Windows-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN Timeline Server
Den <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> visar allmän information om slutförda program samt som ramverksspecifik programinformation via två olika gränssnitt. Närmare bestämt:

* Lagring och hämtning av information om allmänna program på HDInsight-kluster har varit aktiverat med version 3.1.1.374 eller högre.
* Ramverksspecifik information PROGRAMKOMPONENTEN för tidslinje-servern är inte tillgänglig på HDInsight-kluster.

Allmän information om program innehåller följande typer av data:

* Program-ID, ett unikt ID för ett program
* Den användare som startade programmet
* Information om försök görs att slutföra programmet
* Behållare som används av alla program-försök

Den här informationen lagras på dina HDInsight-kluster med Azure Resource Manager. Informationen sparas i historiken butiken i standardlagringen för klustret. Allmänna data på slutförda program kan hämtas via ett REST-API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN-program och loggfiler
YARN stöder flera programmeringsmodeller genom Frikoppling resurshantering från schemaläggning/programövervakning. YARN använder en global *ResourceManager* (RM) per arbetsnod *NodeManagers* (NMs), och programspecifika *ApplicationMasters* (AMs). Programspecifika AM förhandlar resurser (processor, minne, disk, nätverk) för att köra ditt program med RM. RM fungerar med NMs att bevilja dessa resurser, som beviljas som *behållare*. AM ansvarar för att spåra förloppet för de behållare som tilldelats av RM. Ett program kan kräva många behållare beroende på typen av programmet.

* Varje program kan bestå av flera *program försök*. 
* Behållare beviljas till en specifik försök av ett program. 
* En behållare ger kontext för en grundläggande enheten för arbete. 
* Arbete som görs inom ramen för en behållare utförs på enskild worker-noden som behållaren har tilldelats. 

Mer information finns i [YARN begrepp][YARN-concepts].

Programloggar (och associerade behållarloggarna) är avgörande felsöker problematiska Hadoop-program. YARN tillhandahåller ett ramverk som är bra för att samla in, sammanställa och lagra programloggar med den [Log aggregering] [ log-aggregation] funktionen. Funktionen Log aggregering gör åtkomst till programloggarna mer deterministisk, eftersom den sammanställer loggar över alla behållare på en underordnad nod och lagrar dem som en sammansatt loggfil per arbetsnod på standardfilsystemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnod sammanställs till en fil, vilket resulterar i en fil per arbetsnod som används av ditt program. Log aggregering är aktiverat som standard på HDInsight-kluster (version 3.0 och senare), och sammanställda loggfiler finns i standardbehållaren för ditt kluster på följande plats:

    wasb:///app-logs/<user>/logs/<applicationId>

På den platsen *användaren* är namnet på den användare som startade programmet, och *applicationId* är den unika identifieraren för ett program som tilldelats av YARN-RM.

Sammanställda loggfiler kan inte direkt läsas som de är skrivna en [TFile][T-file], [binärformat] [ binary-format] indexeras av behållare. YARN tillhandahåller CLI-verktyg för att dumpa loggarna som oformaterad text för program eller behållare av intresse. Du kan visa dessa loggar som oformaterad text genom att köra något av följande YARN-kommandon direkt på klusternoderna (när du har anslutit till den via RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI körs på klustrets huvudnod och kan nås via instrumentpanelen för Azure portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På menyn till vänster klickar du på **Bläddra**, klickar du på **HDInsight-kluster**, klickar du på ett Windows-baserade kluster som du vill ha åtkomst till YARN-programloggar.
3. Klicka på den översta menyn **instrumentpanelen**. Du ser en sida öppnas på en ny webbläsare fliken med namnet **HDInsight Frågekonsol**.
4. Från **HDInsight Frågekonsol**, klickar du på **Yarn-Användargränssnittet**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
