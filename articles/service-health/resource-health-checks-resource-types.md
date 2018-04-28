---
title: Stöd för resurstyper via Azure Resource Health | Microsoft Docs
description: Resurstyper som stöds via Azure Resource health
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
ms.openlocfilehash: 3b4d99fe883cf52ca7f1ef98e70b7f3a1bccd5ae
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Hälsa och resurstyper kontrollerar i Azure resurshälsa
Nedan visas en fullständig lista över alla kontroller via resurshälsa av resurstyper.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Kontroller som utförs|
|---|
|<ul><li>Api Management-tjänsten är igång och körs?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Kontroller som utförs|
|---|
|<ul><li>Är alla cachenoderna igång?</li><li>Cachen når från inom datacentret?</li><li>Cachen har nått det maximala antalet anslutningar?</li><li> Cachen har förbrukat det tillgängliga minnet? </li><li>Cachen har uppstått ett stort antal sidfel?</li><li>Cacheminnet är hårt belastad?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Kontroller som utförs|
|---|
|<ul> <li>Har någon av slutpunkterna stoppats, borttagna eller felkonfigurerad?</li><li>Är den kompletterande portalen tillgänglig för CDN konfigurationsåtgärder?</li><li>Finns det pågående leverans problem med CDN-slutpunkter?</li><li>Användare kan ändra konfigurationen av sina CDN-resurser?</li><li>Sprider konfigurationsändringar på den förväntade hastigheten?</li><li>Användarna kan hantera CDN-konfigurationen med Azure-portalen, PowerShell eller API: et?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Kontroller som utförs|
|---|
|<ul><li>Är värdservern igång?</li><li>Värd-OS startar slutfördes?</li><li>Behållare för virtuell dator etablerats och aktiverades?</li><li>Det finns en nätverksanslutning mellan värden och storage-konto?</li><li>Start av gästoperativsystemet slutfördes?</li><li>Finns det pågående planerat underhåll?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/Accounts
|Kontroller som utförs|
|---|
|<ul><li>Kontot når från inom datacentret?</li><li>Finns kognitiva Services Resursprovidern?</li><li>Är tjänsten kognitiva i rätt region?</li><li>Kan läsa åtgärder utföras på lagringskontot hålla resursmetadata?</li><li>Uppnått kvoten för API-anrop?</li><li>Har den API-anrop Läs-gränsen?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualmachines
|Kontroller som utförs|
|---|
|<ul><li>Servern är är värd för den här virtuella datorn in och kör?</li><li>Värd-OS startar slutfördes?</li><li>Behållare för virtuell dator etablerats och aktiverades?</li><li>Det finns en nätverksanslutning mellan värden och storage-konto?</li><li>Start av gästoperativsystemet slutfördes?</li><li>Finns det pågående planerat underhåll?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/Accounts
|Kontroller som utförs|
|---|
|<ul><li>Kan användare skicka jobb till Data Lake Analytics i region?</li><li>Utföra grundläggande jobb köras och slutföras i region?</li><li>Användare kan visa katalogobjekt i region?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/Accounts
|Kontroller som utförs|
|---|
|<ul><li>Användare kan överföra data till Data Lake Store i region?</li><li>Användare kan hämta data från Data Lake Store i region?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/iothubs

|Kontroller som utförs|
|---|
|<ul><li>Är IoT-hubben igång?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Kontroller som utförs|
|---|
|<ul><li>Det har förekommit några databas eller en samling förfrågningar som inte hanteras på grund av en Azure DB som Cosmos-tjänsten inte finns?</li><li>Det har förekommit alla begäranden för dokument som inte hanteras på grund av en Azure DB som Cosmos-tjänsten inte finns?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/Connections
|Kontroller som utförs|
|---|
|<ul><li>Ansluts VPN-tunnel</li><li>Är anslutningen konfigurationskonflikterna?</li><li>I förväg delade nycklar konfigureras korrekt?</li><li>Nås lokala VPN-enhet?</li><li>Finns det avvikelser i säkerhetsprincipen IPSec/IKE?</li><li>Är S2S VPN-anslutningen korrekt etablerade eller i ett felaktigt tillstånd?</li><li>Är VNET-till-VNET-anslutningen korrekt etablerade eller i ett felaktigt tillstånd?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Kontroller som utförs|
|---|
|<ul><li>Är den VPN-gatewayen kan nås från internet?</li><li>Är den VPN-Gateway i vänteläge?</li><li>Körs tjänsten VPN på gatewayen?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Kontroller som utförs|
|---|
|<ul><li> Kan runtime åtgärder som registrering, installation eller skicka utföras på namnområdet?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Kontroller som utförs|
|---|
|<ul><li>Är värd-OS igång?</li><li>Är workspaceCollection kan nås från utanför datacentret?</li><li>Är Resursprovidern PowerBI tillgängligt?</li><li>Är PowerBI Service i rätt region?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Kontroller som utförs|
|---|
|<ul><li>Kan diagnostik åtgärder utföras på klustret?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Kontroller som utförs|
|---|
|<ul><li> Har det inloggning på databasen?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Kontroller som utförs|
|---|
|<ul><li>Är alla värdar där jobbet körs in och kör?</li><li>Inte jobbet starta?</li><li>Finns det pågående runtime uppgraderingar?</li><li>Jobb i ett förväntat tillstånd (t ex igång eller stoppad av kund)?</li><li>Jobbet uppstod ut av minne undantag?</li><li>Finns det pågående schemalagda beräkning uppdateringar?</li><li>Finns Körhanteraren (plan för åtkomstkontroll)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Kontroller som utförs|
|---|
|<ul><li>Är värdservern igång?</li><li>Körs Internet Information Services</li><li>Körs belastningsutjämnaren?</li><li>Kan den Apptjänstplan nås från inom datacentret?</li><li>Storage-konto som är värd för innehållet för serverFarm tillgängliga platser?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Kontroller som utförs|
|---|
|<ul><li>Är värdservern igång?</li><li>Internet Information server kör?</li><li>Körs belastningsutjämnaren?</li><li>Webbprogrammet når från inom datacentret?</li><li>Storage-konto som är värd för webbplatsens innehåll tillgängligt?</li></ul>|

# <a name="next-steps"></a>Nästa steg
-  Se [introduktion till Azure Service hälsoinstrumentpanel](service-health-overview.md) och [introduktion till Azure Resource Health](resource-health-overview.md) vill veta mer om dem. 
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
- Ställa in aviseringar så meddelas du om hälsotillstånd. Mer information finns i [konfigurera aviseringar för hälsotillstånd händelser](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
