---
title: Samla in data i en hybridmiljö med Azure Log Analytics-agenten | Microsoft Docs
description: Det här avsnittet hjälper dig att samla in data och övervaka datorer i din lokala eller andra moln med Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 6c58967255270231f61c59205ed12a9cdfec6897
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958703"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Samla in data i en hybridmiljö med Log Analytics-agenten

Azure Log Analytics kan samla in och agera på data från datorer som kör operativsystemet Windows eller Linux som körs i:

* [Azure-datorer](log-analytics-quick-collect-azurevm.md) med Log Analytics VM-tillägg 
* Ditt datacenter som fysiska servrar eller virtuella datorer
* Virtuella datorer i en molnbaserad tjänst som Amazon Web Services (AWS)

Datorer i din miljö kan anslutas direkt till Log Analytics eller om du redan övervakar de här datorerna med System Center Operations Manager 2012 R2 eller senare kan du integrera din Operations Manager-hanteringsgrupp med Log Analytics och fortsätta att underhålla IT service operations-processen.  

## <a name="overview"></a>Översikt

![log-Analytics-Agent-Direct-Connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Innan du analysera och agera på insamlade data, måste du först installera och ansluta agenter för alla datorer som du vill skicka data till Log Analytics-tjänsten. Du kan installera agenter på lokala datorer med hjälp av installationsprogrammet, kommandorad, eller med Desired State Configuration (DSC) i Azure Automation. 

Agenten för Linux och Windows kommunicerar utgående med Log Analytics-tjänsten via TCP-port 443 och om datorn är ansluten till en brandvägg eller proxy server kommunicerar via Internet, granska kraven nedan för att förstå nätverkskonfigurationen krävs.  Om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket att ansluta till Internet, du kan ställa in en [Log Analytics gateway](log-analytics-oms-gateway.md) och sedan konfigurera agenten för att ansluta via gatewayen till Log Analytics. Agenten kan sedan ta emot konfigurationsinformation och skicka data som samlas in beroende på vilka regler för insamling av data och lösningar som du har aktiverat. 

Om du övervakar datorer med System Center Operations Manager 2012 R2 eller senare, kan det vara multihomed med Log Analytics-tjänsten för att samla in data och vidarebefordra till tjänsten och fortfarande att övervakas av [Operations Manager](log-analytics-om-agents.md). Linux-datorer som övervakas av en Operations Manager-hanteringsgrupp som är integrerat med Log Analytics tar inte emot konfiguration för datakällor och vidarebefordra insamlade data via hanteringsgruppen. Windows-agent kan rapportera upp till fyra arbetsytor, medan Linux-agenten stöder endast rapporterar till en enda arbetsyta.  

Agenten för Linux och Windows är inte bara för att ansluta till Log Analytics, det stöder även Azure Automation Hybrid Runbook worker-roll-värd och hanteringslösningar som ändringsspårning och uppdateringshantering.  Mer information om Hybrid Runbook Worker-rollen finns i [Azure Automation Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds
Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2008 Service Pack 1 (SP1) eller senare
* Windows 7 SP1 och senare.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds
Det här avsnittet innehåller information om Linux-distributioner som stöds.    

Från och med versioner som lanseras efter augusti 2018 har gör vi följande ändringar i vår supportmodell:  

* Endast de server-versioner stöds, inte klienten.  
* Nya versioner av [Azure Linux-godkända distributioner](../virtual-machines/linux/endorsed-distros.md) stöds alltid.  
* Alla mindre versioner stöds för varje större version i listan.
* Versioner som har klarat sina tillverkarens support upphör datum stöds inte.  
* Nya versioner av AMI stöds inte.  
* Endast de versioner som kör SSL 1.x som standard som stöds.

Om du använder en distribution eller en version som stöds för närvarande inte och inte justeras till vår supportmodell, rekommenderar vi att du Förgrena lagringsplatsen uppmärksammades att Microsoft-supporten inte kommer ge hjälp med förgrenade agent versioner.

* Amazon Linux 2017.09 (x 64)
* CentOS Linux 6 (x86/x64) och 7 (x 64)  
* Oracle Linux 6 och 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) och 7 (x 64)
* Debian GNU/Linux 8 och 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) och 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x 64)

>[!NOTE]
>OpenSSL 1.1.0 stöds bara på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1.x inte stöds på valfri plattform.
>

## <a name="tls-12-protocol"></a>TLS 1.2-protokollet
Om du vill se till att skydda data under överföringen till Log Analytics, rekommenderar vi starkt att du kan konfigurera att agenten ska du använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**.  Mer information [skickar data på ett säkert sätt med hjälp av TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Krav på brandvägg
Informationen nedan proxy och brandvägg konfigurationsuppgifter som efterfrågas för Linux och Windows-agenten kan kommunicera med Log Analytics.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Inkommande och utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Inkommande och utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Inkommande och utgående|Ja |  
|*.azure-automation.net |Port 443 |Inkommande och utgående|Ja |  


Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten att använda runbooks i din miljö, måste den ha åtkomst till portnumret och URL: erna som beskrivs i [konfigurera nätverket för den Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#network-planning). 

Windows och Linux-agenten har stöd för kommunikation antingen via en proxyserver eller Log Analytics-gatewayen till Log Analytics-tjänsten med hjälp av HTTPS-protokollet.  Både anonyma och grundläggande autentisering (användarnamn/lösenord) stöds.  För Windows-agenten är ansluten direkt till tjänsten proxykonfigurationen har angetts under installationen eller [efter distributionen](log-analytics-agent-manage.md#update-proxy-settings) från Kontrollpanelen eller med PowerShell.  

För Linux-agenten proxyservern har angetts under installationen eller [efter installationen](log-analytics-agent-manage.md#update-proxy-settings) genom att ändra konfigurationsfilen proxy.conf.  Konfigurationsvärdet för proxyn för Linux-agenten har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver att autentisera, kräver Linux-agenten fortfarande ett pseudo/användarlösenord. Detta kan vara valfri användarnamn eller lösenord.

|Egenskap | Beskrivning |
|--------|-------------|
|Protokoll | https |
|Användare | Valfritt användarnamn för proxy-autentisering |
|lösenord | Lösenord för proxyautentisering |
|proxyhost | Adressen eller FQDN för proxy server/Log Analytics-gateway |
|port | Valfritt portnumret för proxy server/Log Analytics-gateway |

Exempel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som ”\@” i ditt lösenord kan du får ett anslutningsfel för proxy eftersom värdet tolkas felaktigt.  Undvik problemet genom att koda lösenordet i URL: en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installera och konfigurera agenten 
Ansluta dina lokala datorer direkt med Log Analytics kan utföras med hjälp av olika metoder beroende på dina krav. Följande tabell visar varje metod för att avgöra vilken som fungerar bäst i din organisation.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
| Windows-dator|- [Manuell installation](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installera Microsoft Monitoring agent från kommandoraden eller med hjälp av en automatiserad metod, till exempel Azure Automation DSC [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), eller med en Azure Resource Manager-mall om du har distribuerat Microsoft Azure Stack i ditt datacenter.| 
|Linux-dator| [Manuell installation](log-analytics-quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett wrapper-skript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med Log Analytics](log-analytics-om-agents.md) | Konfigurera integrering mellan Operations Manager och Log Analytics för att vidarebefordra som samlas in data från Linux- och Windows-datorer som rapporterar till en hanteringsgrupp.|  

## <a name="next-steps"></a>Nästa steg

* Granska [datakällor](log-analytics-data-sources.md) att förstå datakällorna som är tillgängliga för att samla in data från din Windows- eller Linux-system. 

* Lär dig mer om [loggsökningar](log-analytics-log-search.md) att analysera data som samlas in från datakällor och lösningar. 

* Lär dig mer om [lösningar](../monitoring/monitoring-solutions.md) som lägger till funktioner i Log Analytics och också samla in data till Log Analytics-arbetsytan.
