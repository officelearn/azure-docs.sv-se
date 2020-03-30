---
title: Översikt över Log Analytics-agent
description: Det här avsnittet hjälper dig att förstå hur du samlar in data och övervakar datorer som finns i Azure, lokalt eller annan molnmiljö med Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 1ca03cde57a9496054d0860fbb70bd286caabe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533257"
---
# <a name="log-analytics-agent-overview"></a>Översikt över Log Analytics-agent
Azure Log Analytics-agenten har utvecklats för omfattande hantering över virtuella datorer i alla moln, lokala datorer och de som övervakas av [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Windows- och Linux-agenterna skickar insamlade data från olika källor till din Log Analytics-arbetsyta i Azure Monitor, samt alla unika loggar eller mått enligt definitionen i en övervakningslösning. Log Analytics-agenten stöder också insikter och andra tjänster i Azure Monitor, till exempel [Azure Monitor för virtuella datorer,](../insights/vminsights-enable-overview.md)Azure Security [Center](/azure/security-center/)och [Azure Automation](../../automation/automation-intro.md).

Den här artikeln innehåller en detaljerad översikt över agent-, system- och nätverkskraven och de olika distributionsmetoderna.

> [!NOTE]
> Du kan också se Log Analytics-agenten som kallas MICROSOFT Monitoring Agent (MMA) eller OMS Linux-agenten.

> [!NOTE]
> Azure Diagnostics-tillägget är en av de agenter som är tillgängliga för att samla in övervakningsdata från gästoperativsystemet med beräkningsresurser. Se [Översikt över Azure Monitor-agenter](agents-overview.md) för en beskrivning av de olika agenterna och vägledning om hur du väljer lämpliga agenter för dina behov.

## <a name="comparison-to-azure-diagnostics-extension"></a>Jämförelse med Azure diagnostics-tillägg
[Azure-diagnostiktillägget](diagnostics-extension-overview.md) i Azure Monitor kan också användas för att samla in övervakningsdata från gästoperativsystemet för virtuella Azure-datorer. Du kan välja att använda antingen eller båda beroende på dina krav. Se [Översikt över Azure Monitor-agenter](agents-overview.md) för en detaljerad jämförelse av Azure Monitor-agenter. 

De viktigaste skillnaderna att tänka på är:

- Azure Diagnostics Extension kan endast användas med virtuella Azure-datorer. Log Analytics-agenten kan användas med virtuella datorer i Azure, andra moln och lokalt.
- Azure Diagnostics-tillägget skickar data till Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (endast Windows) och Event Hubs. Log Analytics-agenten samlar in data till [Azure Monitor Logs](data-platform-logs.md).
- Log Analytics-agenten krävs för [lösningar,](../monitor-reference.md#insights-and-core-solutions) [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md)och andra tjänster som Azure Security [Center](/azure/security-center/).

## <a name="costs"></a>Kostnader
Det kostar inget för Log Analytics-agenten, men du kan debiteras avgifter för de data som förtärs. Kontrollera [Hantera användning och kostnader med Azure Monitor Logs](manage-cost-storage.md) för detaljerad information om priser för data som samlas in i en Log Analytics-arbetsyta.

## <a name="data-collected"></a>Insamlade data
I följande tabell visas de typer av data som du kan konfigurera en Log Analytics-arbetsyta för att samla in från alla anslutna agenter. Se [Vad övervakas av Azure Monitor?](../monitor-reference.md)

| Datakälla | Beskrivning |
| --- | --- |
| [Windows-händelseloggar](data-sources-windows-events.md) | Information som skickas till Windows händelseloggningssystem. |
| [Syslog](data-sources-syslog.md)                     | Information som skickas till Linux-händelseloggningssystemet. |
| [Prestanda](data-sources-performance-counters.md)  | Numeriska värden som mäter prestanda för olika aspekter av operativsystem och arbetsbelastningar. |
| [IIS-loggar](data-sources-iis-logs.md)                 | Användningsinformation för IIS-webbplatser som körs på gästoperativsystemet. |
| [Anpassade loggar](data-sources-custom-logs.md)           | Händelser från textfiler på både Windows- och Linux-datorer. |

## <a name="data-destinations"></a>Destinationer för data
Log Analytics-agenten skickar data till en Log Analytics-arbetsyta i Azure Monitor. Windows-agenten kan vara flerahomed att skicka data till flera arbetsytor och System Center Operations Manager hanteringsgrupper. Linux-agenten kan bara skicka till en enda destination.

## <a name="other-services"></a>Övriga tjänster
Agenten för Linux och Windows är inte bara för anslutning till Azure Monitor, den stöder också Azure Automation för att vara värd för Hybrid Runbook-arbetarrollen och andra tjänster som [ändringsspårning,](../../automation/change-tracking.md) [uppdateringshantering](../../automation/automation-update-management.md)och [Azure Security Center](../../security-center/security-center-intro.md). Mer information om hybridkörningsarbetsrollen finns i [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Installation och konfiguration

När du använder Log Analytics-agenterna för att samla in data måste du förstå följande för att planera distributionen av din agent:

* Om du vill samla in data från Windows-agenter kan du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](agent-windows.md), även när den rapporterar till en hanteringsgrupp för System Center Operations Manager. Windows-agenten kan rapportera upp till fyra arbetsytor.
* Linux-agenten stöder inte flera mål och kan bara rapportera till en enda arbetsyta.
* Windows-agenten stöder [FIPS 140-standarden](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), medan Linux-agenten inte stöder den.  

Om du använder System Center Operations Manager 2012 R2 eller senare:

* Varje Hanteringsgrupp för Operations Manager kan [bara anslutas till en arbetsyta](om-agents.md).
* Linux-datorer som rapporterar till en hanteringsgrupp måste konfigureras för att rapportera direkt till en Log Analytics-arbetsyta. Om dina Linux-datorer redan rapporterar direkt till en arbetsyta och du vill övervaka dem med Operations Manager följer du dessa steg för att [rapportera till en Hanteringsgrupp för Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Du kan installera Log Analytics Windows-agenten på Windows-datorn och låta den rapportera till både Operations Manager integrerat med en arbetsyta och en annan arbetsyta.


Det finns flera metoder för att installera Log Analytics-agenten och ansluta din dator till Azure Monitor beroende på dina behov. I följande tabell beskrivs varje metod för att avgöra vilken som fungerar bäst i organisationen.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
|Azure VM| [Manuellt från Azure-portalen](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Ange virtuella datorer som ska distribueras från log analytics-arbetsytan. |
| | Logga virtuellt filnamnstillägg för Analytics för [Windows](../../virtual-machines/extensions/oms-windows.md) eller [Linux](../../virtual-machines/extensions/oms-linux.md) med Azure CLI eller med en Azure Resource Manager-mall | Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar dem i en befintlig Azure Monitor-arbetsyta. |
| | [Azure Monitor för virtuella datorer](../insights/vminsights-enable-overview.md) | När du aktiverar övervakning med Azure Monitor för virtuella datorer installeras log analytics-tillägget och agenten. |
| | [Automatisk etablering av Azure Security Center](../../security-center/security-center-enable-data-collection.md) | Azure Security Center kan etablera Log Analytics-agenten på alla azure-virtuella datorer som stöds och alla nya som skapas om du aktiverar den för att övervaka säkerhetsproblem och hot. Om det är aktiverat etableras alla nya eller befintliga virtuella datorer utan en installerad agent. |
| Windows-hybriddator| [Manuell installation](agent-windows.md) | Installera Microsoft Monitoring-agenten från kommandoraden. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatisera installationen med Azure Automation DSC. |
| | [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Använd en Azure Resource Manager-mall om du har distribuerat Microsoft Azure Stack i ditt datacenter.| 
| Linux-hybriddator| [Manuell installation](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett omslagsskript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med logganalys](../../azure-monitor/platform/om-agents.md) | Konfigurera integrering mellan Operations Manager- och Azure Monitor-loggar för att vidarebefordra insamlade data från Windows-datorer som rapporterar till en hanteringsgrupp.|  


## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Operativsystemet Windows stöds officiellt för Windows-agenten:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, version 1709 och 1803
* Windows 7 SP1, Windows 8 Enterprise och Pro samt Windows 10 Enterprise and Pro

>[!NOTE]
>Log Analytics-agenten för Windows har utformats för att stödja scenarier för serverövervakning, men vi inser att du kan köra Windows-klienten för att stödja arbetsbelastningar som konfigurerats och optimerats för serveroperativsystemet. Agenten stöder Windows-klienten, men våra övervakningslösningar fokuserar inte på scenarier för klientövervakning om inte uttryckligen anges.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Det här avsnittet innehåller information om linuxdistributioner som stöds.

Från och med versioner som släpps efter augusti 2018 gör vi följande ändringar i vår supportmodell:  

* Endast serverversionerna stöds, inte klient.  
* Fokusera supporten på någon av [de Azure Linux-stödda distributioner](../../virtual-machines/linux/endorsed-distros.md). Observera att det kan finnas en viss fördröjning mellan en ny distribution/version som Azure Linux-signerad och den stöds för Log Analytics Linux-agenten.
* Alla mindre versioner stöds för varje huvudversion som anges.
* Versioner som har passerat tillverkarens supportdatum stöds inte.  
* Nya versioner av AMI stöds inte.  
* Endast versioner som kör SSL 1.x som standard stöds.

>[!NOTE]
>Om du använder en distribution eller version som för närvarande inte stöds och inte anpassar sig till vår supportmodell rekommenderar vi att du förfalskar den här reporäntan och bekräftar att Microsofts support inte hjälper till med klurda agentversioner.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) och 7 (x64)  
* Oracle Linux 6 och 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) och 7 (x64)
* Debian GNU/Linux 8 och 9 (x86/x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x86/x64) och 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) och 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 stöds endast på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1.x stöds inte på någon plattform.
>

### <a name="agent-prerequisites"></a>Agent förutsättningarna

I följande tabell beskrivs de paket som krävs för Linux-distributioner som stöds som agenten kommer att installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0.x eller 1.1.x |
|Curl | cURL-webbklient | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Antingen rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standardsystemlog daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux version (sysklog) stöds inte för syslog händelsesamling. För att samla in syslog data från den här versionen av dessa distributioner, bör rsyslog demon installeras och konfigureras för att ersätta sysklog.

## <a name="tls-12-protocol"></a>TLS 1.2-protokoll

För att säkerställa säkerheten för data under överföring till Azure Monitor-loggar rekommenderar vi starkt att du konfigurerar agenten så att den använder minst TLS (Transport Layer Security) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och medan de fortfarande arbetar för att tillåta bakåtkompatibilitet rekommenderas de **inte**.  Mer information finns i [Skicka data på ett säkert sätt med TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Stödkrav för STÖD FÖR SHA-2-kodsignering för Windows
Windows-agenten börjar använda SHA-2-signering den 18 maj 2020. Den här ändringen påverkar kunder som använder Log Analytics-agenten på ett äldre operativsystem som en del av alla Azure-tjänster (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). Ändringen kräver ingen kundåtgärd om du inte kör agenten på en äldre OS-version (Windows 7, Windows Server 2008 R2 och Windows Server 2008). Kunder som körs på en äldre OS-version måste vidta följande åtgärder på sina datorer före den 18 maj 2020 eller så slutar deras agenter att skicka data till sina Log Analytics-arbetsytor:

1. Installera det senaste Service Pack-paketet för ditt operativsystem. De service pack-versioner som krävs är:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installera SHA-2-signeringen av Windows-uppdateringar för ditt operativsystem enligt beskrivningen i [2019 SHA-2-kodsigneringsstödkravet för Windows och WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Uppdatera till den senaste versionen av Windows-agenten (version 10.20.18029).
4. Rekommenderas att konfigurera agenten så att [den använder TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Nätverkskrav
Agenten för Linux och Windows kommunicerar utgående till Azure Monitor-tjänsten via TCP-port 443, och om datorn ansluter via en brandvägg eller proxyserver för att kommunicera via Internet, granska kraven nedan för att förstå nätverkskonfigurationen Krävs. Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet kan du konfigurera en [Log Analytics-gateway](gateway.md) och sedan konfigurera agenten för att ansluta via gatewayen till Azure Monitor-loggar. Agenten kan sedan ta emot konfigurationsinformation och skicka data som samlats in beroende på vilka datainsamlingsregler och övervakningslösningar du har aktiverat på arbetsytan.

![Kommunikationsdiagram för logganalysagent](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Krav på nätverksbrandvägg
Informationen nedan visar den proxy- och brandväggskonfigurationsinformation som krävs för att Linux- och Windows-agenten ska kunna kommunicera med Azure Monitor-loggar.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  

Brandväggsinformation som krävs för Azure Government finns i [Azure Government management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera dig med Tjänsten Automation för att använda runbooks eller hanteringslösningar i din miljö, måste den ha åtkomst till portnumret och webbadresserna som beskrivs i [Konfigurera nätverket för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Windows- och Linux-agenten stöder kommunikation antingen via en proxyserver eller Log Analytics-gateway till Azure Monitor med HTTPS-protokollet.  Både anonym och grundläggande autentisering (användarnamn/lösenord) stöds.  För Windows-agenten som är ansluten direkt till tjänsten anges proxykonfigurationen under installationen eller [efter distributionen](agent-manage.md#update-proxy-settings) från Kontrollpanelen eller med PowerShell.  

För Linux-agenten anges proxyservern under installationen eller [efter installationen](agent-manage.md#update-proxy-settings) genom att ändra konfigurationsfilen proxy.conf.  Konfigurationsvärdet för Linux-agentproxy har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver att du autentiserar krävs fortfarande att Linux-agenten tillhandahåller en pseudoanvändare/ett lösenord. Detta kan vara vilket användarnamn eller lösenord som helst.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|användare | Valfritt användarnamn för proxyautentisering |
|password | Valfritt lösenord för proxyautentisering |
|proxyhost (proxyhost) | Adress eller FQDN för proxyservern/Log Analytics-gatewayen |
|port | Valfritt portnummer för proxyservern/Log Analytics-gatewayen |

Exempel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken\@som " " i lösenordet visas ett proxyanslutningsfel eftersom värdet tolkas felaktigt.  Du kan undvika det här problemet genom att koda lösenordet i URL:en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Nästa steg

* Granska [datakällor](agent-data-sources.md) för att förstå de datakällor som är tillgängliga för att samla in data från ditt Windows- eller Linux-system. 
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar. 
* Lär dig mer om [övervakningslösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och även samlar in data på Log Analytics-arbetsytan.

