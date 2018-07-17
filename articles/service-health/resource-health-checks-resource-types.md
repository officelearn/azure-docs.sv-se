---
title: Stöd för resurstyper genom Azure Resource Health | Microsoft Docs
description: Stöds resurstyper genom Azure Resource health
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 633501b03a362fbd07e14e308e18488f03af5af5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069218"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Resurstyper och hälsokontroller i Azure resource health
Nedan visas en fullständig lista över alla kontroller som utförs genom resource health av resurstyper.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Utförda kontroller|
|---|
|<ul><li>Är servern igång?</li><li>Har servern slut på minne?</li><li>Servern startas?</li><li>Återställs servern?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Utförda kontroller|
|---|
|<ul><li>Api Management-tjänsten är igång och köra?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Utförda kontroller|
|---|
|<ul><li>Är alla cachenoderna igång?</li><li>Cachen når från inom datacentret?</li><li>Cachen har nått det maximala antalet anslutningar?</li><li> Cachen har utförts dess tillgängligt minne? </li><li>Cachen upplever ett stort antal sidfel?</li><li>Är cachen vid hög belastning?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Utförda kontroller|
|---|
|<ul> <li>Är den kompletterande portalen för CDN konfigurationsåtgärder?</li><li>Finns det problem med kontinuerlig leverans med CDN-slutpunkter?</li><li>Användare kan ändra konfigurationen av sina CDN-resurser?</li><li>Är konfigurationsändringar sprider priset som förväntat?</li><li>Användarna kan hantera CDN-konfigurationen med hjälp av Azure portal, PowerShell eller API: et?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Har värd-OS startar slutförts?</li><li>Den virtuella behållaren etablerats och påslagen?</li><li>Det finns en nätverksanslutning mellan värden och storage-konto?</li><li>Start av gästoperativsystemet slutfördes?</li><li>Finns det pågående planerat underhåll?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/Accounts
|Utförda kontroller|
|---|
|<ul><li>Kontot når från inom datacentret?</li><li>Är Cognitive Services-Resursprovidern tillgängligt?</li><li>Är Cognitive Service tillgänglig i aktuell region?</li><li>Kan läsa åtgärder utföras på det lagringskonto som innehåller resursmetadata?</li><li>Har API-anrop kvot uppnåtts?</li><li>Har API-anrop Läs-gräns har nåtts?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är servern som är värd för den här virtuella datorn upp och körs?</li><li>Har värd-OS startar slutförts?</li><li>Den virtuella behållaren etablerats och påslagen?</li><li>Det finns en nätverksanslutning mellan värden och storage-konto?</li><li>Start av gästoperativsystemet slutfördes?</li><li>Finns det pågående planerat underhåll?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/Accounts
|Utförda kontroller|
|---|
|<ul><li>Har användarna råkat ut för problem att skicka eller lista över sina Data Lake Analytics-jobb?</li><li>Är Data Lake Analytics-jobb inte slutföra system fel?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/Accounts
|Utförda kontroller|
|---|
|<ul><li>Användare ha uppstått problem med att överföra data till Data Lake Store?</li><li>Användare ha uppstått problem som hämtar data från Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices /

|Utförda kontroller|
|---|
|<ul><li>Är IoT hub igång?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Utförda kontroller|
|---|
|<ul><li>Det har förekommit några databasen eller samling förfrågningar som inte visas på grund av ett Azure Cosmos DB-tjänsten otillgänglighet?</li><li>Det har förekommit alla begäranden om dokument visas inte på grund av ett Azure Cosmos DB-tjänsten otillgänglighet?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Utförda kontroller|
|---|
|<ul><li>Begäranden till nyckelvalvet misslyckas på grund av problem med Azure KeyVault-plattformen?</li><li>Är begäranden till nyckelvalvet begränsas på grund av för många förfrågningar som kunden har gjort?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/Connections
|Utförda kontroller|
|---|
|<ul><li>Är den VPN-tunneln ansluten?</li><li>Är anslutningen konfigurationskonflikter?</li><li>I förväg delade nycklar konfigureras korrekt?</li><li>Nås den lokala VPN-enheten?</li><li>Finns det matchningsfel i säkerhetsprincipen IPSec/IKE?</li><li>Är S2S VPN-anslutningen korrekt etablerade eller i ett felaktigt tillstånd?</li><li>Är VNET-till-VNET-anslutningen korrekt etablerade eller i ett felaktigt tillstånd?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Utförda kontroller|
|---|
|<ul><li>Är den VPN-gatewayen kan nås från internet?</li><li>Är den VPN-Gateway i vänteläge?</li><li>Är den VPN-tjänsten som körs på gatewayen?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Utförda kontroller|
|---|
|<ul><li> Runtime-åtgärder som registrering, installation eller skicka utföras i namnområdet?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Utförda kontroller|
|---|
|<ul><li>Är värdens operativsystem igång?</li><li>Är workspaceCollection kan nås från utanför datacentret?</li><li>Finns Power BI-Resursprovidern?</li><li>Är PowerBI Service tillgänglig i aktuell region?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Utförda kontroller|
|---|
|<ul><li>Diagnostik åtgärder utföras i klustret?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Utförda kontroller|
|---|
|<ul><li> Har det varit inloggning på databasen?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Utförda kontroller|
|---|
|<ul><li>Begäranden om att läsa data från Storage-kontot misslyckas på grund av problem med Azure Storage-plattformen?</li><li>Begäranden om att skriva data till lagringskontot misslyckas på grund av problem med Azure Storage-plattformen?</li><li>Är lagringsklustret där lagringskontot finns inte tillgänglig?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Utförda kontroller|
|---|
|<ul><li>Är alla värdar där jobbet körs upp och köra?</li><li>Var jobbet kunde inte starta?</li><li>Finns det pågående körning uppgraderingar?</li><li>Är en uppgift i ett förväntat tillstånd (till exempel igång eller stoppad av kunden)?</li><li>Jobbet påträffade ut av minne undantag?</li><li>Finns det pågående schemalagda beräkning uppdateringar?</li><li>Finns Körhanteraren (kontrollplan)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Körs Internet Information Services?</li><li>Belastningsutjämnaren kör?</li><li>App Service-planen når från inom datacentret?</li><li>Storage-konto som är värd för webbplatser innehållet för serverklustret tillgängliga rubrikrad?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Utförda kontroller|
|---|
|<ul><li>Är värdservern igång?</li><li>Internet Information server kör?</li><li>Belastningsutjämnaren kör?</li><li>Kan nås Webbappen från inom datacentret?</li><li>Storage-konto som är värd för webbplatsens innehåll tillgängligt?</li></ul>|

# <a name="next-steps"></a>Nästa steg
-  Se [introduktion till Azure Service Health-instrumentpanelen](service-health-overview.md) och [introduktion till Azure Resource Health](resource-health-overview.md) vill veta mer om dem. 
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
- Ställa in aviseringar så att du blir informerad om problem med hälsotillstånd. Mer information finns i [konfigurera aviseringar för service health-händelser](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
