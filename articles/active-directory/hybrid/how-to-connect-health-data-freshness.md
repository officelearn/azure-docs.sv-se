---
title: Azure AD Connect Health - hälsotjänstinformationen är inte upp till datum aviseringen | Microsoft Docs
description: Det här dokumentet beskriver orsaken till ”hälsotjänstinformationen är inte uppdaterad” aviseringen och felsökning av den.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349957"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstinformationen är inte uppdaterad avisering

## <a name="overview"></a>Översikt

Agenter på lokala datorer som Azure AD Connect Health övervakar regelbundet ladda upp data till Azure AD Connect Health-tjänsten. Om tjänsten inte ta emot data från en agent blir information i portalen inaktuella. För att fokusera på problemet, tjänsten returnerar den **hälsotjänstinformationen är inte uppdaterad** aviseringen. Den här aviseringen genereras när tjänsten inte har tagit emot fullständiga data under de senaste två timmarna.  

- Den **varning** status aviseringen utlöses om tjänsten för hälsotillstånd har tagit emot endast **partiella** datatyper som skickats från servern under de senaste två timmarna. Status för varning utlöses inte e-postmeddelanden till angivna mottagare. 
- Den **fel** status aviseringen utlöses om tjänsten för hälsotillstånd inte har fått några datatyper från servern under de senaste två timmarna. Fel status avisering utlösare e-postmeddelanden till angivna mottagare.

Tjänsten hämtar data från agenter som körs på lokala datorer, beroende på vilken tjänsttyp. I följande tabell listar de agenter som körs på datorn, vad de gör och vilka datatyper av som tjänsten genererar. I vissa fall kan finns det flera tjänster som ingår i processen, så någon av dem kan vara orsaken. 

## <a name="understanding-the-alert"></a>Förstå aviseringen

Den **aviseringsinformation** bladet ser när aviseringen inträffade och identifierades senast. En bakgrundsprocess som körs varannan timme genererar och omvärderas aviseringen. I följande exempel: inledande aviseringen inträffade på 03/10 kl. 9:59. Aviseringen fortfarande låg på 03/12 klockan 10:00 när aviseringen utvärderades igen. Bladet beskriver också den tid som tjänsten för hälsotillstånd senast tog emot en viss datatyp. 
 
 ![Azure AD Connect Health-aviseringsinformation](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
I följande tabell visas tjänsttyper till motsvarande nödvändiga data-typer:

| Typ av tjänst | Agent (Windows-tjänstens namn) | Syfte | Datatypen som genererats  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Azure AD Connect Health Sync Insights Service | Samla in AAD Connect-specifik information (anslutningar, Synkroniseringsregler, osv.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Samla in AAD Connect-specifika prestandaräknare, ETW-spårning, filer | Prestandaräknare |
| AD DS | Azure AD Connect Health AD DS Insights Service | Utföra syntetiska tester, samla in information om nätverkstopologin, Replikeringens metadata |  - Adds-TopologyInfo-Json <br /> -Gemensamma-TestData-Json (skapar testresultaten)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Samla in ADDS-specifika prestandaräknare, ETW-spårning, filer | -Prestandaräknare  <br /> -Gemensamma-TestData-Json (Överför testresultaten)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Utföra syntetiska tester | TestResult (skapar testresultaten) | 
| | Azure AD Connect Health AD FS Insights Service  | Samla in användningsstatistik för AD FS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Samla in AD FS-specifika prestandaräknare, ETW-spårning, filer | TestResult (Överför testresultaten) |

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 

De steg som krävs för att diagnostisera problemet anges nedan. Först är en uppsättning grundläggande kontroller som är gemensamma för alla typer av tjänster. Tabellen nedan som visar ut specifika steg för varje typ av tjänst och -datatypen. 

> [!IMPORTANT] 
> Den här aviseringen följer Connect Health [policy för datalagring](reference-connect-health-user-privacy.md#data-retention-policy)

* Kontrollera att de senaste versionerna av agenterna är installerade. Visa [versionshistorik](reference-connect-health-version-history.md). 
* Se till att Azure AD Connect Health-agenterna tjänster är **kör** på datorn. Connect Health för AD FS bör till exempel har tre tjänster.
  ![Verifiera Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Se till att gå igenom och uppfylla de [kravavsnitt](how-to-connect-health-agent-install.md#requirements).
* Använd [test anslutningsverktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) att identifiera problem med nätverksanslutningen.
* Om du har en HTTP-Proxy, följer du dessa [konfigurationssteg](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Nästa steg
Om någon av ovanstående steg har identifierat ett problem, lösa det och vänta på att lösa aviseringen. Aviseringen bakgrunden körs varannan timme, så att det tar upp till två timmar att lösa aviseringen. 

* [Azure AD Connect Health-datakvarhållningsprincip](reference-connect-health-user-privacy.md#data-retention-policy)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
