---
title: Ansluta datakällor till Azure Sentinel | Microsoft-dokument
description: Lär dig hur du ansluter datakällor till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 9d2d1985b23e1c7f5e0f7d9fd2795bd85e28ace0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240088"
---
# <a name="connect-data-sources"></a>Ansluta till datakällor

För att vara ombord på Azure Sentinel måste du först ansluta till dina datakällor. Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga direkt och ger integrering i realtid, inklusive Microsoft Threat Protection-lösningar och Microsoft 365-källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda kopplingar till det bredare säkerhetsekosystemet för lösningar som inte kommer från Microsoft. Du kan också använda vanligt händelseformat, Syslog eller REST-API för att ansluta dina datakällor med Azure Sentinel också.  

1. Välj **Datakopplingar**på menyn . På den här sidan kan du se den fullständiga listan över anslutningsappar som Azure Sentinel tillhandahåller och deras status. Markera den koppling som du vill ansluta och välj **Öppna kopplingssida**. 

   ![Datainsamlare](./media/collect-data/collect-data-page.png)

1. På den specifika anslutningssidan kontrollerar du att du har uppfyllt alla förutsättningar och följer instruktionerna för att ansluta data till Azure Sentinel. Det kan ta lite tid för loggarna att börja synkronisera med Azure Sentinel. När du har anslutit visas en sammanfattning av data i diagrammet **Data som tas emot** och anslutningsstatus för datatyperna.

   ![Anslut samlare](./media/collect-data/opened-connector-page.png)
  
1. Klicka på fliken **Nästa steg** om du vill få en lista över direkt innehåll som Azure Sentinel tillhandahåller för den specifika datatypen.

   ![Datainsamlare](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Dataanslutningsmetoder

Följande dataanslutningsmetoder stöds av Azure Sentinel:

- **Integrering av service till tjänst:**<br> Vissa tjänster är inkopplade internt, till exempel AWS och Microsoft-tjänster, dessa tjänster utnyttjar Azure-grunden för integrering utanför boxen, följande lösningar kan anslutas med några få klick:
    - [Amazon webbtjänster - CloudTrail](connect-aws.md)
    - [Azure-aktivitet](connect-azure-activity.md)
    - [Azure AD-granskningsloggar och inloggningar](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domännamnserver](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft-brandväggen för webbaserade program](connect-microsoft-waf.md)
    - [Windows-brandvägg](connect-windows-firewall.md)
    - [Windows säkerhetshändelser](connect-windows-security-events.md)

- **Externa lösningar via API:** Vissa datakällor är anslutna med API:er som tillhandahålls av den anslutna datakällan. Vanligtvis ger de flesta säkerhetstekniker en uppsättning API:er genom vilka händelseloggar kan hämtas. API:erna ansluter till Azure Sentinel och samlar in specifika datatyper och skickar dem till Azure Log Analytics. Apparater som är anslutna via API inkluderar:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen-brandväggen](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externa lösningar via agent:** Azure Sentinel kan anslutas till alla andra datakällor som kan utföra loggströmning i realtid med Syslog-protokollet, via en agent. <br>De flesta enheter använder Syslog-protokollet för att skicka händelsemeddelanden som innehåller själva loggen och data om loggen. Loggernas format varierar, men de flesta enheter stöder CEF-baserad formatering (Common Event Format) för loggdata. <br>Azure Sentinel-agenten, som baseras på Log Analytics-agenten, konverterar CEF-formaterade loggar till ett format som kan intas av Log Analytics. Beroende på typ av apparat installeras agenten antingen direkt på apparaten eller på en dedikerad Linux-server. Agenten för Linux tar emot händelser från Syslog-demonen över UDP, men om en Linux-maskin förväntas samla in en hög volym Syslog-händelser skickas de över TCP från Syslog-demonen till agenten och därifrån till Log Analytics.
    - Brandväggar, proxyservrar och slutpunkter:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Forcepoint-produkter](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Andra FSE-apparater](connect-common-event-format.md)
        - [Andra Syslog-apparater](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-lösningar
    - [Leverantörer av hotunderrättelser](connect-threat-intelligence.md)
    - [DNS-maskiner](connect-dns.md) - agent installerad direkt på DNS-datorn
    - Linux-servrar
    - Andra moln
    
## <a name="agent-connection-options"></a>Alternativ för agentanslutning<a name="agent-options"></a>

För att ansluta din externa installation till Azure Sentinel måste agenten distribueras på en dedikerad dator (virtuell dator eller lokal) för att stödja kommunikationen mellan installationen och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är endast tillgänglig om din dedikerade dator är en ny virtuell dator som du skapar i Azure. 


![FSE i Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativt kan du distribuera agenten manuellt på en befintlig Azure VM, på en virtuell dator i ett annat moln eller på en lokal dator.

![FSE på plats](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mappa datatyper med anslutningsalternativ för Azure Sentinel


| **Datatyp** | **Ansluta** | **Dataanslutning?** | **Kommentar** |
|------|---------|-------------|------|
| AWSCloudTrail | [Ansluta AWS](connect-aws.md) | V | |
| AzureActivity | Översikt över [Azure-aktivitets-](connect-azure-activity.md) och [aktivitetsloggar](../azure-monitor/platform/platform-logs-overview.md)| V | |
| AuditLogs | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | V | |
| Logga inLoggningar | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | V | |
| AzureFirewall (på andra) |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Azure Information Protection-rapporter](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Ansluta till Azure Information Protection](connect-azure-information-protection.md)  | V | Detta använder vanligtvis funktionen **InformationProtectionEvents** utöver datatypen. Mer information finns i [Så här ändrar du rapporterna och skapar anpassade frågor](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Trafik analytiska schema](../network-watcher/traffic-analytics.md) [Trafikanalys](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog (CommonSecurityLog)  | [Anslut FSE](connect-common-event-format.md)  | V | |
| OfficeAktivitet | [Ansluta till Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Ansluta till Windows säkerhetshändelser](connect-windows-security-events.md)  | V | Arbetsboksinställningar för osäkra protokoll finns [i Arbetsboksinställningar för osäkra protokoll](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Syslog | [Ansluta till Syslog](connect-syslog.md) | V | |
| Brandvägg för Microsoft Web Application (WAF) – (AzureDiagnostics) |[Ansluta brandvägg för Microsoft-webbprogram](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Anslut Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Ansluta till hotinformation](connect-threat-intelligence.md)  | V | |
| VMAnslutning <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Tjänstöversikt för Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor VM-insikter ombord](../azure-monitor/insights/vminsights-onboard.md) <br> [Aktivera insikter om virtuella Azure Monitor-datorer](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Använda enkel vm-ombordstigning](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Använda on-boarding via policy](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Arbetsbok för VM-insikter  |
| DnsEvents (1) | [Anslut DNS](connect-dns.md) | V | |
| W3CIISLog | [Anslut IIS-loggar](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData (WireData) | [Anslut tråddata](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall (windowsfirewall) | [Ansluta Windows-brandväggen](connect-windows-firewall.md) | V | |
| AADIP-säkerhetAlert  | [Ansluta Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SäkerhetAlert  | [Ansluta till Azure ATP](connect-azure-atp.md) | V | |
| ASC-säkerhetAlert  | [Ansluta till Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS-säkerhetAlert  | [Ansluta Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SäkerhetAlert | | | |
| Sysmon (Händelse) | [Anslut Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Ansluta Windows-händelser](../azure-monitor/platform/data-sources-windows-events.md) <br> [Hämta Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Sysmon-samling installeras inte som standard på virtuella datorer. Mer information om hur du installerar Sysmon Agent finns i [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData (KonfigurationData)  | [Automatisera VM-lager](../automation/automation-vm-inventory.md)| X | |
| KonfigurationÄndning  | [Automatisera vm-spårning](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Ansluta F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Ansluta till Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du [lägger in dina data i Azure Sentinel](quickstart-onboard.md)och får insyn i dina data och [potentiella hot](quickstart-get-visibility.md).
