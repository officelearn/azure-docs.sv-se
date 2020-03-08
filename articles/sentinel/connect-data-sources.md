---
title: Anslut data källor till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter data källor till Azure Sentinel.
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
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668841"
---
# <a name="connect-data-sources"></a>Ansluta till datakällor

För att kunna använda Azure Sentinel måste du först ansluta till dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft Threat Protection-lösningar och Microsoft 365 källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda vanliga händelse format, syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.  

1. På menyn väljer du **data kopplingar**. På den här sidan kan du se en fullständig lista över de anslutningar som Azure Sentinel tillhandahåller och deras status. Välj den anslutning som du vill ansluta och välj **Öppna kopplings sida**. 

   ![Data insamlare](./media/collect-data/collect-data-page.png)

1. På sidan för den aktuella anslutningen ser du till att du har uppfyllt alla krav och följer anvisningarna för att ansluta data till Azure Sentinel. Det kan ta lite tid innan loggarna börjar synkronisera med Azure Sentinel. När du har anslutit visas en sammanfattning av data i grafen med **mottagna data** och anslutnings status för data typerna.

   ![Anslut insamlare](./media/collect-data/opened-connector-page.png)
  
1. Klicka på fliken **Nästa steg** om du vill hämta en lista över innehåll i Azure Sentinel som är färdiga för den aktuella data typen.

   ![Data insamlare](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Data anslutnings metoder

Följande data anslutnings metoder stöds av Azure Sentinel:

- **Integrering av tjänst till tjänst**:<br> Vissa tjänster är anslutna till varandra, till exempel AWS och Microsoft-tjänster, de här tjänsterna utnyttjar Azure Foundation för out of Box-integreringen. följande lösningar kan vara anslutna med några klick:
    - [Amazon Web Services-CloudTrail](connect-aws.md)
    - [Azure-aktivitet](connect-azure-activity.md)
    - [Gransknings loggar och inloggningar i Azure AD](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domän namn server](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Brand vägg för Microsoft-webbprogram](connect-microsoft-waf.md)
    - [Windows-brandvägg](connect-windows-firewall.md)
    - [Windows säkerhets händelser](connect-windows-security-events.md)

- **Externa lösningar via API**: vissa data källor är anslutna via API: er som tillhandahålls av den anslutna data källan. Oftast tillhandahåller de flesta säkerhets tekniker en uppsättning API: er via vilka händelse loggar som kan hämtas. API: erna ansluter till Azure Sentinel och samlar in vissa data typer och skickar dem till Azure Log Analytics. Enheter som är anslutna via API inkluderar:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen-brandvägg](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (säkerhet)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint-DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec-ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externa lösningar via agent**: Azure Sentinel kan anslutas till alla andra data källor som kan utföra logg strömning i real tid med syslog-protokollet via en agent. <br>De flesta apparater använder syslog-protokollet för att skicka händelse meddelanden som innehåller själva loggen och data om loggen. Loggens format varierar, men de flesta apparater stöder common Event format (CEF)-baserad formatering för loggdata. <br>Azure Sentinel-agenten, som baseras på Log Analytics agent, konverterar CEF-formaterade loggar till ett format som kan matas in av Log Analytics. Beroende på typ av installation installeras agenten antingen direkt på enheten eller på en särskild Linux-server. Agenten för Linux tar emot händelser från syslog-daemon över UDP, men om en Linux-dator förväntas samla in en stor mängd Syslog-händelser skickas de via TCP från syslog-daemon till agenten och därifrån att Log Analytics.
    - Brand väggar, proxyservrar och slut punkter:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop (x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Forcepoint-produkter](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo-nätverk](connect-paloalto.md)
        - [En identitet som skyddar](connect-one-identity.md)
        - [Andra CEF-enheter](connect-common-event-format.md)
        - [Andra syslog-enheter](connect-syslog.md)
        - [Trend Micro djupgående säkerhet](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-lösningar
    - [Hot informations leverantörer](connect-threat-intelligence.md)
    - [DNS-datorer](connect-dns.md) – Agent installeras direkt på DNS-datorn
    - Linux-servrar
    - Andra moln
    
## Anslutnings alternativ för agent<a name="agent-options"></a>

Om du vill ansluta din externa installation till Azure Sentinel måste agenten distribueras på en dedikerad dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är bara tillgängligt om din dedikerade dator är en ny virtuell dator som du skapar i Azure. 


![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mappa data typer med anslutnings alternativ för Azure Sentinel


| **Datatyp** | **Så här ansluter du** | **Data koppling?** | **Kommentarer** |
|------|---------|-------------|------|
| AWSCloudTrail | [Anslut AWS](connect-aws.md) | V | |
| AzureActivity | Översikt över [Anslut Azure-aktivitet](connect-azure-activity.md) och [aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md)| V | |
| AuditLogs | [Anslut Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Anslut Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Azure Information Protection rapporter](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Anslut Azure Information Protection](connect-azure-information-protection.md)  | V | Detta använder vanligt vis **InformationProtectionEvents** -funktionen tillsammans med data typen. Mer information finns i [ändra rapporterna och skapa anpassade frågor](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | Trafik [analys schema](../network-watcher/traffic-analytics.md) [](../network-watcher/traffic-analytics.md) analys  | | |
| CommonSecurityLog  | [Anslut CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Anslut Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Anslut säkerhets händelser i Windows](connect-windows-security-events.md)  | V | Arbets böckerna för oskyddade protokoll finns i [arbets boks konfiguration för oskyddade protokoll](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Syslog | [Anslut syslog](connect-syslog.md) | V | |
| Microsoft-brand vägg för webbaserade program (WAF) – (AzureDiagnostics) |[Anslut brand väggen för Microsoft-webbprogram](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Anslut Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Anslut Hot information](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor tjänst karta](../azure-monitor/insights/service-map.md)<br>[Azure Monitor VM Insights onboarding](../azure-monitor/insights/vminsights-onboard.md) <br> [Aktivera Azure Monitor VM-insikter](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Använda en enda virtuell dator](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Använda fordonsbaserad via princip](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Arbets bok för VM Insights  |
| DnsEvents | [Anslut DNS](connect-dns.md) | V | |
| W3CIISLog | [Anslut IIS-loggar](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [Anslut tråd data](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Anslut Windows-brandväggen](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Anslut Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Anslut Azure ATP](connect-azure-atp.md) | V | |
| ASC-SecurityAlert  | [Anslut Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Anslut Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (händelse) | [Anslut Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Anslut Windows-händelser](../azure-monitor/platform/data-sources-windows-events.md) <br> [Hämta Sysmon-parsern](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Sysmon-samlingen installeras inte som standard på virtuella datorer. Mer information om hur du installerar Sysmon-agenten finns i [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatisera VM-inventering](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange  | [Automatisera spårning av virtuella datorer](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Anslut F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Anslut Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
