---
title: Resurstyper som stöds via Azure Resource Health | Microsoft-dokument
description: Resurstyper som stöds via Azure Resource-hälsotillståndet
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3ba7b308c0c7671df8a652194830cb910cb89acc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258309"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Resurstyper och hälsokontroller i Azure-resurshälsa
Nedan finns en fullständig lista över alla kontroller som utförs via resurshälsa efter resurstyper.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servrar
|Utförda kontroller|
|---|
|<ul><li>Är servern igång?</li><li>Har servern på minne?</li><li>Startar servern?</li><li>Återställs servern?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Utförda kontroller|
|---|
|<ul><li>Är Api Management-tjänsten igång?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchKonton
|Utförda kontroller|
|---|
|<ul><li>Är batch-kontot igång?</li><li>Har poolkvoten överskridits för det här batchkontot?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Utförda kontroller|
|---|
|<ul><li>Är alla cachenoder igång?</li><li>Kan cachen nås inifrån datacentret?</li><li>Har cacheminnet nått det maximala antalet anslutningar?</li><li> Har cachen tömt sitt tillgängliga minne? </li><li>Har cachen ett stort antal sidfel?</li><li>Är cachen under tung belastning?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profil
|Utförda kontroller|
|---|
|<ul> <li>Är tilläggsportalen tillgänglig för CDN-konfigurationsåtgärder?</li><li>Finns det pågående leveransproblem med CDN-slutpunkterna?</li><li>Kan användare ändra konfigurationen av sina CDN-resurser?</li><li>Sprids konfigurationsändringar i förväntad takt?</li><li>Kan användare hantera CDN-konfigurationen med Hjälp av Azure-portalen, PowerShell eller API:et?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Har värdoperativsystemet startat?</li><li>Är behållaren för virtuell dator etablerad och påslagen?</li><li>Finns det nätverksanslutning mellan värden och lagringskontot?</li><li>Har uppstarten av gästoperativsystemet slutförts?</li><li>Finns det pågående planerat underhåll?</li><li>Är värdhårdvaran försämrad och förutspådd att misslyckas snart?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts Microsoft.cognitiveservices/accounts Microsoft.cognitiveservices/accounts Microsoft.
|Utförda kontroller|
|---|
|<ul><li>Kan kontot nås inifrån datacentret?</li><li>Är Cognitive Services Resource Provider tillgänglig?</li><li>Är cognitivetjänsten tillgänglig i rätt region?</li><li>Kan läsåtgärder utföras på lagringskontot som innehåller resursmetadata?</li><li>Har API-anropskvoten uppnåtts?</li><li>Har läsgränsen för API-anrop uppnåtts?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är servern värd för denna virtuella maskin igång?</li><li>Har värdoperativsystemet startat?</li><li>Är behållaren för virtuell dator etablerad och påslagen?</li><li>Finns det nätverksanslutning mellan värden och lagringskontot?</li><li>Har uppstarten av gästoperativsystemet slutförts?</li><li>Finns det pågående planerat underhåll?</li><li>Är värdhårdvaran försämrad och förutspådd att misslyckas snart?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/fabriker
|Utförda kontroller|
|---|
|<ul><li>Har det förekommit pipelinekörningsfel?</li><li>Är klustret som är värd för Data Factory felfritt?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts Microsoft.datalakeanalytics/accounts Microsoft.datalakeanalytics/accounts Microsoft.
|Utförda kontroller|
|---|
|<ul><li>Har användarna haft problem med att skicka in eller lista sina Data Lake Analytics-jobb?</li><li>Kan datasjöanalysjobb inte slutföras på grund av systemfel?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/konton
|Utförda kontroller|
|---|
|<ul><li>Har användarna haft problem med att ladda upp data till Data Lake Store?</li><li>Har användarna haft problem med att hämta data från Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/tjänster
|Utförda kontroller|
|---|
|<ul><li>Har databasmigreringstjänsten inte kunnat etableras?</li><li>Har databasmigreringstjänsten stoppats på grund av inaktivitet eller användarbegäran?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/konton
|Utförda kontroller|
|---|
|<ul><li>Är datadelningskontot igång?</li><li>Är klustret som är värd för dataresursen tillgängligt?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servrar
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servrar
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servrar
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Utförda kontroller|
|---|
|<ul><li>Är IoT-hubben igång?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseKonton
|Utförda kontroller|
|---|
|<ul><li>Har det inte serverats några databas- eller samlingsbegäranden på grund av att azure Cosmos DB-tjänsten inte är tillgängliga?</li><li>Har det inte förekommit några dokumentbegäranden som inte har serverats på grund av en Azure Cosmos DB-tjänst som inte är tillgängliga?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Utförda kontroller|
|---|
|<ul><li>Har namnområdet Event Hubs användargenererat fel?</li><li>Håller namnområdet Event Hubs just nu på att uppgraderas?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/kluster
|Utförda kontroller|
|---|
|<ul><li>Är kärntjänster tillgängliga i HDInsight-klustret?</li><li>Kan HDInsight-klustret komma åt nyckeln för BYOK-kryptering i vila?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/valv
|Utförda kontroller|
|---|
|<ul><li>Misslyckas begäranden till nyckelvalvet på grund av azure keyvault-plattformsproblem?</li><li>Begränsas begäranden om nyckelvalv på grund av för många förfrågningar från kunden?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Utförda kontroller|
|---|
|<ul><li>Är webbtjänsten igång?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Utförda kontroller|
|---|
|<ul><li>Är medietjänsten igång?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Utförda kontroller|
|---|
|<ul><li>Försämras programgatewayens prestanda?</li><li>Är programgatewayen tillgänglig?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/anslutningar
|Utförda kontroller|
|---|
|<ul><li>Är VPN-tunneln ansluten?</li><li>Finns det konfigurationskonflikter i anslutningen?</li><li>Är de fördelade nycklarna korrekt konfigurerade?</li><li>Kan VPN-enheten på lokal plats nås?</li><li>Finns det felmatchningar i IPSec/IKE-säkerhetsprincipen?</li><li>Är S2S VPN-anslutningen korrekt etablerad eller i ett misslyckat tillstånd?</li><li>Är VNET-till-VNET-anslutningen korrekt etablerad eller i ett misslyckat tillstånd?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Utförda kontroller|
|---|
|<ul><li>Är ExpressRoute-kretsen hälsosam?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Utförda kontroller|
|---|
|<ul><li>Svarar frontdörrens baksäten med fel på hälsosonder?</li><li>Fördröjs konfigurationsändringar?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Utförda kontroller|
|---|
|<ul><li>Är slutpunkterna för belastningsutjämning tillgängliga?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Utförda kontroller|
|---|
|<ul><li>Kan VPN-gatewayen nås från internet?</li><li>Är VPN-gatewayen i vänteläge?</li><li>Körs VPN-tjänsten på gatewayen?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Utförda kontroller|
|---|
|<ul><li>Kan körningsåtgärder som registrering, installation eller sändning utföras på namnområdet?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Utförda kontroller|
|---|
|<ul><li>Finns det indexeringsfördröjningar för arbetsytan?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Kapacitet
|Utförda kontroller|
|---|
|<ul><li>Är kapacitetsresursen igång?</li><li>Är alla arbetsbelastningar igång?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceKolla
|Utförda kontroller|
|---|
|<ul><li>Är värdoperativsystemet igång?</li><li>Kan arbetsytanKollection nås från utanför datacentret?</li><li>Är Power BI-resursleverantören tillgänglig?</li><li>Är Power BI-tjänsten tillgänglig i rätt region?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Utförda kontroller|
|---|
|<ul><li>Kan diagnostikåtgärder utföras på klustret?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namnområden
|Utförda kontroller|
|---|
|<ul><li>Har kunder användargenererade servicebussfel?</li><li>Har användarna en ökning av tillfälliga fel på grund av en servicebussnamnområdesuppgradering?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/kluster
|Utförda kontroller|
|---|
|<ul><li>Är Service Fabric-klustret igång?</li><li>Kan Service Fabric-klustret hanteras via Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databaser
|Utförda kontroller|
|---|
|<ul><li>Är databasen igång?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Server/databaser
|Utförda kontroller|
|---|
|<ul><li>Har det varit inloggningar till databasen?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Utförda kontroller|
|---|
|<ul><li>Misslyckas begäranden om att läsa data från lagringskontot på grund av problem med Azure Storage-plattformen?</li><li>Misslyckas begäranden om att skriva data till lagringskontot på grund av problem med Azure Storage-plattformen?</li><li>Är lagringsklustret där lagringskontot inte finns tillgängligt?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Utförda kontroller|
|---|
|<ul><li>Är alla värdar där jobbet körs igång?</li><li>Kunde jobbet inte starta?</li><li>Finns det pågående runtime uppgraderingar?</li><li>Körs eller stoppas jobbet av ett förväntat tillstånd (till exempel körs eller stoppas av kund)?</li><li>Har jobbet stött på undantag från minnet?</li><li>Finns det pågående schemalagda beräkningsuppdateringar?</li><li>Är Körningshanteraren (kontrollplan) tillgänglig?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Körs Internet Information Services?</li><li>Körs belastningsutjämnaren?</li><li>Kan App Service Plan nås inifrån datacentret?</li><li>Är lagringskontot som är värd för webbplatsens innehåll för serverFarm tillgänglig?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/webbplatser
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Körs Internet Information-servern?</li><li>Körs belastningsutjämnaren?</li><li>Kan webbappen nås inifrån datacentret?</li><li>Är lagringskontot som är värd för webbplatsens innehåll tillgängligt?</li></ul>|

## <a name="next-steps"></a>Efterföljande moment
-  Se [Introduktion till instrumentpanelen för Azure Service Health](service-health-overview.md) och Introduktion till Azure Resource [Health](resource-health-overview.md) för att förstå mer om dem. 
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
- Ställ in aviseringar så att du får ett meddelande om hälsoproblem. Mer information finns i [Konfigurera aviseringar för hälsohändelser för tjänsten](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
