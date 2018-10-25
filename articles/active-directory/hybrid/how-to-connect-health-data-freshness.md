---
title: Azure AD Connect Health - hälsotjänstinformationen är inte upp till datum aviseringen | Microsoft Docs
description: Det här dokumentet beskriver orsaken till ”hälsotjänstinformationen är inte uppdaterad” aviseringen och felsökning av den.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: e470a44732b881311eacecfdf2bd2211598d880a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984868"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstinformationen är inte uppdaterad avisering

## <a name="overview"></a>Översikt
<li>Azure AD Connect Health genererar data färska avisering när den inte tar emot alla datapunkter från servern i två timmar. Aviseringens namn är **hälsotjänstinformationen är inte uppdaterad**. </li>
<li>Den **varning** status aviseringen utlöses om Connect Health inte tar emot partiella dataelement som skickats från servern i två timmar. Status för en varning utlöses inte e-postmeddelanden till Innehavaradministratör. </li>
<li>Den **fel** status aviseringen utlöses om Connect Health inte får några dataelement som skickats från servern i två timmar. Fel status avisering utlösare e-postmeddelanden till Innehavaradministratör. </li>

>[!IMPORTANT] 
> Den här aviseringen följer Connect Health [policy för datalagring](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 
* Se till att gå igenom och uppfylla de [kravavsnitt](how-to-connect-health-agent-install.md#requirements).
* Använd [test anslutningsverktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) att identifiera problem med nätverksanslutningen.
* Om du har HTTP-Proxy [konfigurationssteg här](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-adfs"></a>Connect Health för AD FS
Ytterligare åtgärder för att validera för AD FS och följ arbetsflödet i [hjälp om AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

### <a name="data-collection-map-required-steps"></a>Datainsamling mappa steg som krävs
| Tjänstnamn | Dataelement | Felsökningsanvisningar |
| --- | --- | --- | 
| Connect Health för AD FS | PerfCounter TestResult | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [SSL-kontroll för utgående trafik filtreras eller inaktiveras](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Tillåt avsedda webbplatser om Förbättrad säkerhetskonfiguration är aktiverad](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AD FS-UsageMetrics | Utgående anslutningar baserat på IP-adresser, referera till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) | 
| Connect Health för synkronisering | PerfCounter | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [SSL-kontroll för utgående trafik filtreras eller inaktiveras](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Tillåt avsedda webbplatser om Förbättrad säkerhetskonfiguration är aktiverad](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AadSyncService SynchronizationRules <br /> AadSyncService-kopplingar <br /> AadSyncService GlobalConfigurations <br /> AadSyncService RunProfileResults <br /> AadSyncService ServiceConfigurations <br /> AadSyncService ServiceStatus | – Utgående anslutningar baserat på IP-adresser, referera till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 
| Connect Health för ADDS  | PerfCounter, lägger till-TopologyInfo-Json, vanliga-TestData-Json | - [Utgående anslutning till Azure-tjänsteslutpunkt](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [SSL-kontroll för utgående trafik filtreras eller inaktiveras](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Brandväggsportar på servern som kör agenten](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Tillåt avsedda webbplatser om Förbättrad säkerhetskonfiguration är aktiverad](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) <br />  – Utgående anslutningar baserat på IP-adresser, referera till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)  |




## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
