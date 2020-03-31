---
title: Azure AD Connect Health – Hälsotjänstdata är inte uppdaterad | Microsoft-dokument
description: I det här dokumentet beskrivs orsaken till aviseringen "Hälsotjänstdata är inte uppdaterade" och hur du felsöker den.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897181"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstdata är inte uppdaterad varning

## <a name="overview"></a>Översikt

Agenterna på de lokala datorerna som Azure AD Connect Health övervakar överför regelbundet data till Azure AD Connect Health Service. Om tjänsten inte tar emot data från en agent är informationen som portalen presenterar inaktuell. För att belysa problemet kommer tjänsten att höja **hälsotjänstdata är inte uppdaterad** avisering. Den här aviseringen genereras när tjänsten inte har tagit emot fullständiga data under de senaste två timmarna.  

- **Varningsstatusvarningen** utlöses om hälsotjänsten endast har tagit emot **partiella** datatyper som skickats från servern under de senaste två timmarna. Varningsstatusvarningen utlöser inte e-postmeddelanden till konfigurerade mottagare. 
- **Felstatusvarningen** utlöses om hälsotjänsten inte har tagit emot några datatyper från servern under de senaste två timmarna. Felstatusvarningen utlöser e-postmeddelanden till konfigurerade mottagare.

Tjänsten hämtar data från agenter som körs på de lokala datorerna, beroende på tjänsttypen. I följande tabell visas de agenter som körs på datorn, vad de gör och vilka datatyper som tjänsten genererar. I vissa fall finns det flera tjänster som deltar i processen, så någon av dem kan vara den skyldige. 

## <a name="understanding-the-alert"></a>Förstå varningen

Bladet **Aviseringsinformation** visas när aviseringen inträffade och senast upptäcktes. En bakgrundsprocess som körs varannan timme genererar och utvärderar aviseringen igen. I följande exempel inträffade den första aviseringen 03/10 klockan 09:59. Aviseringen fanns kvar på 03/12 klockan 10:00 när aviseringen utvärderades igen. Bladet beskriver också när hälsotjänsten senast tog emot en viss datatyp. 
 
 ![Information om hälsoavisering i Azure AD Connect](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Följande tabell mappar tjänsttyper till motsvarande datatyper som krävs:

| Typ av tjänst | Agent (Windows-tjänstnamn) | Syfte | Datatyp som genereras  |
| --- | --- | --- | --- |  
| Azure AD Connect (synkronisering) | Azure AD Connect Health Sync Insights Service | Samla in AAD Connect-specifik information (kopplingar, synkroniseringsregler osv.) | - AadSyncService-synchronizationRules <br />  - AadSyncService-kopplingar <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Samla AAD Connect-specifika perf-räknare, ETW-spår, filer | Prestandaräknare |
| AD DS | Azure AD Connect Health AD DS Insights Service | Utföra syntetiska tester, samla in topologiinformation, replikeringsmetadata |  - Lägger till topologiInfo-Json <br /> - Common-TestData-Json (skapar testresultaten)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Samla in ADD-specifika perf-räknare, ETW-spår, filer | - Prestandaräknare  <br /> - Common-TestData-Json (laddar upp testresultaten)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Utföra syntetiska tester | TestResult (skapar testresultaten) | 
| | Azure AD Connect Health AD FS Insights Service  | Samla in ADFS-användningsmått | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Samla ADFS-specifika perf-räknare, ETW-spår, filer | TestResult (laddar upp testresultaten) |

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 

De steg som krävs för att diagnostisera problemet ges nedan. Den första är en uppsättning grundläggande kontroller som är gemensamma för alla tjänsttyper. 

> [!IMPORTANT] 
> Den här varningen följer [principen om lagring av](reference-connect-health-user-privacy.md#data-retention-policy) Anslutningshälsa

* Kontrollera att de senaste versionerna av agenterna är installerade. Visa [versionshistorik](reference-connect-health-version-history.md). 
* Kontrollera att Azure AD Connect Health Agents-tjänsterna **körs** på datorn. Anslut hälsa för AD FS bör till exempel ha tre tjänster.
  ![Verifiera Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Se till att gå över och uppfylla [kraven avsnitt](how-to-connect-health-agent-install.md#requirements).
* Använd [testanslutningsverktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) för att identifiera anslutningsproblem.
* Om du har en HTTP-proxy följer du dessa [konfigurationssteg](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Nästa steg
Om något av ovanstående steg identifierade ett problem åtgärdar du det och väntar på att aviseringen ska lösas. Aviseringsbakgrundsprocessen körs varannan timme, så det tar upp till 2 timmar att lösa aviseringen. 

* [Azure AD Connect-princip för lagring av datalagring](reference-connect-health-user-privacy.md#data-retention-policy)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
