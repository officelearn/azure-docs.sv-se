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
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429035"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstinformationen är inte uppdaterad avisering

## <a name="overview"></a>Översikt
Agenter på lokala datorer som Azure AD Connect Health övervakar regelbundet överför data till Azure AD Connect Health Service. Om tjänsten inte ta emot data från en agent blir informationen på portalen inaktuella. För att fokusera på problemet, tjänsten höjer **hälsotjänstinformationen är inte uppdaterad** aviseringen. Det här genereras när tjänsten inte har tagit emot data under de senaste två timmarna.  

* Den **varning** status aviseringen utlöses om Connect Health inte tar emot partiella dataelement som skickats från servern i två timmar. Status för en varning utlöses inte e-postmeddelanden till Innehavaradministratör.
* Den **fel** status aviseringen utlöses om Connect Health inte får några dataelement som skickats från servern i två timmar. Fel status avisering utlösare e-postmeddelanden till Innehavaradministratör.


## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 

> [!IMPORTANT] 
> Den här aviseringen följer Connect Health [policy för datalagring](reference-connect-health-user-privacy.md#data-retention-policy)

* Se till att Azure AD Connect Health-agenterna tjänster körs på datorn. Connect Health för AD FS bör till exempel har tre tjänster.  
  ![Verifiera Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Se till att gå igenom och uppfylla de [kravavsnitt](how-to-connect-health-agent-install.md#requirements).
* Använd [test anslutningsverktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) att identifiera problem med nätverksanslutningen.
* Om du har en HTTP-Proxy, följer du dessa [konfigurationssteg](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

Bladet aviseringsinformation listar ut saknade data element från en server. I följande tabell kan begränsa problemet ytterligare. 
### <a name="connect-health-for-sync"></a>Connect Health för synkronisering

| Dataelement | Felsökningsanvisningar |
| --- | --- | 
| PerfCounter | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [SSL-kontroll för utgående trafik filtreras eller inaktiveras](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health för AD FS

Ytterligare åtgärder för att validera för AD FS och följ arbetsflödet i [hjälp om AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Dataelement | Felsökningsanvisningar |
| --- | --- | 
| PerfCounter, TestResult | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [SSL-kontroll för utgående trafik filtreras eller inaktiveras](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | Utgående anslutningar baserat på IP-adresser, referera till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>Connect Health för ADDS

| Dataelement | Felsökningsanvisningar |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
