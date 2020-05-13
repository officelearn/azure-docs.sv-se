---
title: Översikt över Log Analytics agent
description: Det här avsnittet hjälper dig att förstå hur du samlar in data och övervakar datorer som finns i Azure, lokalt eller i en annan moln miljö med Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 0a29ee1536c7c808fe7d15c0abe26f27042bc962
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196252"
---
# <a name="log-analytics-agent-overview"></a>Översikt över Log Analytics agent
Azure Log Analytics-agenten har utvecklats för omfattande hantering av virtuella datorer i alla moln, lokala datorer och de som övervakas av [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Windows-och Linux-agenterna skickar insamlade data från olika källor till din Log Analytics arbets yta i Azure Monitor, samt alla unika loggar eller mått som definierats i en övervaknings lösning. Log Analytics agenten stöder också insikter och andra tjänster i Azure Monitor som [Azure Monitor for VMS](../insights/vminsights-enable-overview.md), [Azure Security Center](/azure/security-center/)och [Azure Automation](../../automation/automation-intro.md).

Den här artikeln innehåller en detaljerad översikt över agent-, system-och nätverks krav och olika distributions metoder.

> [!NOTE]
> Du kan också se Log Analytics-agenten som kallas Microsoft Monitoring Agent (MMA) eller OMS Linux-agenten.

> [!NOTE]
> Azure-diagnostik tillägget är en av de agenter som är tillgängliga för att samla in övervaknings data från gäst operativ systemet för beräknings resurser. Se [Översikt över Azure Monitor agenter](agents-overview.md) för en beskrivning av de olika agenterna och vägledningen för att välja lämpliga agenter för dina behov.

## <a name="comparison-to-azure-diagnostics-extension"></a>Jämförelse med Azure Diagnostics-tillägget
[Tillägget Azure Diagnostics](diagnostics-extension-overview.md) i Azure Monitor kan också användas för att samla in övervaknings data från gäst operativ systemet på virtuella Azure-datorer. Du kan välja att använda antingen eller båda beroende på dina behov. En detaljerad jämförelse av de Azure Monitor agenterna finns i [Översikt över Azure Monitors agenter](agents-overview.md) . 

Viktiga skillnader att tänka på är:

- Azure-diagnostik-tillägget kan bara användas med virtuella Azure-datorer. Log Analytics agenten kan användas med virtuella datorer i Azure, andra moln och lokalt.
- Azure-diagnostik-tillägget skickar data till Azure Storage, [Azure Monitor mått](data-platform-metrics.md) (endast Windows) och Event Hubs. Log Analytics agent samlar in data till [Azure Monitor loggar](data-platform-logs.md).
- Log Analytics agent krävs för [lösningar](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor for VMS](../insights/vminsights-overview.md)och andra tjänster som [Azure Security Center](/azure/security-center/).

## <a name="costs"></a>Kostnader
Det kostar inget att Log Analytics agent, men du kan debiteras avgifter för inmatade data. Se [Hantera användning och kostnader med Azure Monitor loggar](manage-cost-storage.md) för detaljerad information om priser för data som samlas in i en Log Analytics arbets yta.

## <a name="data-collected"></a>Insamlade data
I följande tabell visas de typer av data som du kan konfigurera en Log Analytics arbets yta att samla in från alla anslutna agenter. Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en lista med insikter, lösningar och andra lösningar som använder Log Analytics-agenten för att samla in andra typer av data.

| Datakälla | Beskrivning |
| --- | --- |
| [Händelse loggar i Windows](data-sources-windows-events.md) | Information som skickas till händelse loggnings systemet i Windows. |
| [Syslog](data-sources-syslog.md)                     | Information som skickas till händelse loggnings systemet i Linux. |
| [Historik](data-sources-performance-counters.md)  | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| [IIS-loggar](data-sources-iis-logs.md)                 | Användnings information för IIS-webbplatser som körs på gäst operativ systemet. |
| [Anpassade loggar](data-sources-custom-logs.md)           | Händelser från textfiler på både Windows-och Linux-datorer. |

## <a name="data-destinations"></a>Data destinationer
Log Analytics agent skickar data till en Log Analytics arbets yta i Azure Monitor. Windows-agenten kan vara multihomed för att skicka data till flera arbets ytor och System Center Operations Manager hanterings grupper. Linux-agenten kan bara skicka till ett enda mål.

## <a name="other-services"></a>Övriga tjänster
Agenten för Linux och Windows är inte bara för att ansluta till Azure Monitor. den har också stöd för Azure Automation som värd för Hybrid Runbook Worker-rollen och andra tjänster som [ändringsspårning](../../automation/change-tracking.md), [uppdateringshantering](../../automation/automation-update-management.md)och [Azure Security Center](../../security-center/security-center-intro.md). Mer information om Hybrid Runbook Worker-rollen finns i [Azure Automation hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Installation och konfiguration

När du använder Log Analyticss agenter för att samla in data måste du förstå följande för att kunna planera agent distributionen:

* Om du vill samla in data från Windows-agenter kan du [Konfigurera varje agent så att den rapporterar till en eller flera arbets ytor](agent-windows.md), även om den rapporterar till en System Center Operations Manager hanterings grupp. Windows-agenten kan rapportera upp till fyra arbets ytor.
* Linux-agenten stöder inte Multi-värdar och kan bara rapportera till en enda arbets yta.
* Windows-agenten stöder [FIPS 140-standarden](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), medan Linux-agenten inte stöder det.  

Om du använder System Center Operations Manager 2012 R2 eller senare:

* Varje Operations Manager hanterings grupp kan [endast anslutas till en arbets yta](om-agents.md).
* Linux-datorer som rapporterar till en hanterings grupp måste konfigureras att rapportera direkt till en Log Analytics-arbetsyta. Om Linux-datorerna redan rapporterar direkt till en arbets yta och du vill övervaka dem med Operations Manager följer du dessa steg för att [rapportera till en Operations Manager hanterings grupp](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Du kan installera Log Analytics Windows-agenten på Windows-datorn och låta den rapportera till båda Operations Manager integrerade med en arbets yta och en annan arbets yta.


Det finns flera metoder för att installera Log Analytics-agenten och ansluta datorn till Azure Monitor beroende på dina behov. I följande tabell beskrivs varje metod för att avgöra vilken som fungerar bäst i din organisation.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
|Azure VM| [Manuellt från Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Ange de virtuella datorer som ska distribueras från arbets ytan Log Analytics. |
| | Log Analytics VM-tillägg för [Windows](../../virtual-machines/extensions/oms-windows.md) eller [Linux](../../virtual-machines/extensions/oms-linux.md) med hjälp av Azure CLI eller med en Azure Resource Manager-mall | Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar dem i en befintlig Azure Monitor arbets yta. |
| | [Azure Monitor för virtuella datorer](../insights/vminsights-enable-overview.md) | När du aktiverar övervakning med Azure Monitor for VMs installeras Log Analytics-tillägget och agenten. |
| | [Azure Security Center automatisk etablering](../../security-center/security-center-enable-data-collection.md) | Azure Security Center kan etablera Log Analytics-agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om du aktiverar den för att övervaka säkerhets problem och hot. Om den aktive ras kommer alla nya eller befintliga virtuella datorer utan en installerad agent att tillhandahållas. |
| Windows-hybriddator| [Manuell installation](agent-windows.md) | Installera Microsoft Monitoring Agent från kommando raden. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatisera installationen med Azure Automation DSC. |
| | [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Använd en Azure Resource Manager mall om du har distribuerat Microsoft Azure Stack i ditt data Center.| 
| Linux-hybriddator| [Manuell installation](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett omslutnings skript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurera integration mellan Operations Manager och Azure Monitor loggar för att vidarebefordra insamlade data från Windows-datorer som rapporterar till en hanterings grupp.|  


## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2019
* Windows Server 2016, version 1709 och 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise och Pro
* Windows 8 Enterprise och Pro 
* Windows 7 SP1

>[!NOTE]
>Log Analytics agenten för Windows har utformats för att stödja server övervaknings scenarier, vi inser att du kan köra Windows-klienten för att stödja arbets belastningar som kon figurer ATS och optimerats för serveroperativ systemet. Agenten har stöd för Windows-klienten, men våra övervaknings lösningar fokuserar inte på klient övervaknings scenarier om inget annat anges explicit.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Det här avsnittet innehåller information om Linux-distributioner som stöds.

Från och med versioner som publicerats efter 2018 augusti gör vi följande ändringar i vår support modell:  

* Endast Server versionerna stöds, inte klienten.  
* Fokusera på en [Azure Linux-distributioner som har bekräftats](../../virtual-machines/linux/endorsed-distros.md). Observera att det kan uppstå en fördröjning mellan en ny distribution/version som är godkänd i Azure Linux och stöds för Log Analytics Linux-agenten.
* Alla mindre versioner stöds för varje huvud version som anges.
* Versioner som har passerat support datumet stöds inte.  
* Nya versioner av AMI stöds inte.  
* Endast versioner som kör SSL 1. x stöds som standard.

>[!NOTE]
>Om du använder en distribution eller version som inte stöds och inte justeras till vår support modell, rekommenderar vi att du förlitar dig på den här lagrings platsen, vilket erkänner att Microsoft Support inte kommer att ge hjälp med förgrenade agent versioner.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x64) och 7 (x64)  
* Oracle Linux 6 och 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) och 8 (x64)
* Debian GNU/Linux 8 och 9 (x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x64) och 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) och 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 stöds endast på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1. x stöds inte på någon plattform.
>

### <a name="agent-prerequisites"></a>Krav för agent

I följande tabell visas de paket som krävs för Linux-distributioner som stöds av-agenten som agenten ska installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0. x eller 1.1. x |
|Curl | webb klient för sväng | 7.15.5 |
|Python – ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Antingen rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard syslog-daemonen på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för händelse insamling i syslog. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska rsyslog daemon installeras och konfigureras för att ersätta sysklog.

## <a name="tls-12-protocol"></a>TLS 1,2-protokoll

För att säkerställa säkerheten för data som överförs till Azure Monitor loggar, rekommenderar vi starkt att du konfigurerar agenten att använda minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**.  Mer information finns i [skicka data på ett säkert sätt med TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Stöd krav för SHA-2-kod signering för Windows
Windows-agenten börjar uteslutande använda SHA-2-signering den 17 augusti 2020. Den här ändringen påverkar kunder som använder Log Analytics agenten på ett äldre operativ system som en del av en Azure-tjänst (Azure Monitor, Azure Automation, Azure Uppdateringshantering, Azure Ändringsspårning, Azure Security Center, Azure Sentinel, Windows Defender ATP). Ändringen kräver ingen kund åtgärd om du inte kör agenten på en äldre OS-version (Windows 7, Windows Server 2008 R2 och Windows Server 2008). Kunder som kör på en äldre OS-version måste vidta följande åtgärder på sina datorer före den 17 augusti 2020, annars kommer deras agenter att sluta skicka data till sina Log Analytics arbets ytor:

1. Installera den senaste Service Pack-versionen för ditt operativ system. De nödvändiga service pack versionerna är:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installera SHA-2-signering av Windows-uppdateringar för ditt operativ system enligt beskrivningen i [2019 SHA-2 kod signering support krav för Windows och WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Uppdatera till den senaste versionen av Windows-agenten (version 10.20.18029).
4. Vi rekommenderar att du konfigurerar agenten att [använda TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Nätverkskrav
Agenten för Linux och Windows kommunicerar utgående till Azure Monitor tjänsten via TCP-port 443, och om datorn ansluter via en brand vägg eller proxyserver för att kommunicera via Internet kan du läsa igenom kraven nedan för att förstå den nätverks konfiguration som krävs. Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan du konfigurera en [Log Analytics Gateway](gateway.md) och sedan Konfigurera agenten så att den ansluter genom gatewayen till Azure Monitor loggar. Agenten kan sedan ta emot konfigurations information och skicka insamlade data beroende på vilka data insamlings regler och övervaknings lösningar som du har aktiverat i din arbets yta.

![Diagram över Log Analytics-agent kommunikation](./media/log-analytics-agent/log-analytics-agent-01.png)

I följande tabell visas den konfigurations information för proxy och brand vägg som krävs för att Linux-och Windows-agenter ska kunna kommunicera med Azure Monitor loggar.

### <a name="firewall-requirements"></a>Brandväggsförutsättningar

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Inkommande och utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Inkommande och utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Inkommande och utgående|Ja |
|*.azure-automation.net |Port 443 |Inkommande och utgående|Ja |

För brand Väggs information som krävs för Azure Government, se [Azure Government hantering](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten för att använda Runbooks eller hanterings lösningar i din miljö, måste den ha åtkomst till port numret och de URL: er som beskrivs i [Konfigurera ditt nätverk för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Proxykonfiguration

Windows-och Linux-agenten stöder kommunikation antingen via en proxyserver eller Log Analytics Gateway för att Azure Monitor med HTTPS-protokollet.  Både anonym och grundläggande autentisering (användar namn/lösen ord) stöds.  För Windows-agenten som är ansluten direkt till tjänsten anges proxykonfigurationen under installationen eller [efter distributionen](agent-manage.md#update-proxy-settings) från kontroll panelen eller med PowerShell.  

För Linux-agenten anges proxyservern under installationen eller [efter installationen](agent-manage.md#update-proxy-settings) genom att ändra konfigurations filen proxy. conf.  Konfiguration svärdet för Linux-agentens proxy har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver att du autentiserar måste Linux-agenten fortfarande tillhandahålla en pseudo-användare/-lösen ord. Detta kan vara valfritt användar namn eller lösen ord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|användare | Valfritt användar namn för proxyautentisering |
|password | Valfritt lösen ord för proxyautentisering |
|proxyhost | Adress eller FQDN för proxyservern/Log Analytics Gateway |
|port | Valfritt port nummer för proxyservern/Log Analytics Gateway |

Exempelvis: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som " \@ " i ditt lösen ord får du ett anslutnings fel på grund av att värdet parsas felaktigt.  Undvik det här problemet genom att koda lösen ordet i URL: en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Nästa steg

* Granska [data källorna](agent-data-sources.md) för att förstå de data källor som är tillgängliga för att samla in data från Windows-eller Linux-systemet. 
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar. 
* Lär dig mer om att [övervaka lösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och även samla in data i arbets ytan Log Analytics.

