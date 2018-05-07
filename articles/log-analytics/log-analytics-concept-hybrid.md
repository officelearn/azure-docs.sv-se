---
title: Samla in data från din miljö med Azure Log Analytics | Microsoft Docs
description: Det här avsnittet hjälper dig att förstå hur samla in data och övervaka datorer i din lokala eller andra molnmiljö med logganalys.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: magoedte
ms.openlocfilehash: 1ac956d638be1e79547ff931ba5b0c7e5de1ae65
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Samla in data från datorer i din miljö med logganalys

Azure logganalys kan samla in och arbeta med data från Windows- eller Linux-datorer som finns i:

* [Virtuella Azure-datorer](log-analytics-quick-collect-azurevm.md) med Log Analytics VM-tillägget 
* Ditt datacenter som fysiska servrar eller virtuella datorer
* Virtuella datorer i en molnbaserad tjänst som Amazon Web Services (AWS)

Datorer i din miljö kan anslutas direkt till Log Analytics, eller om du redan övervakar dessa datorer med System Center Operations Manager 2012 R2 2016 eller version 1801, kan du integrera hanteringsgruppen Operations hantera med Logga Analytics och fortsätta att underhålla IT service operations-processen.  

## <a name="overview"></a>Översikt

![log-Analytics-Agent-Direct-Connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Innan du analysera och fungerar på insamlade data, behöver du först installera och ansluta agenter för alla datorer som du vill skicka data till logganalys-tjänsten. Du kan installera agenter på datorerna lokalt med hjälp av kommandoraden installationen eller med önskad tillstånd Configuration (DSC) i Azure Automation. 

Agent för Linux och Windows kommunicerar utgående med Log Analytics-tjänsten via TCP-port 443 och om datorn är ansluten till en brandvägg eller proxyserver server för att kommunicera över Internet, granska [avsnittet förutsättningar](#prerequisites) till Förstå nätverkskonfigurationer.  Om din IT-säkerhetsprinciper inte tillåter datorer i nätverket för att ansluta till Internet, kan du konfigurera en [OMS Gateway](log-analytics-oms-gateway.md) och sedan konfigurera agenten för att ansluta till gatewayen till logganalys. Agenten kan sedan ta emot information om konfiguration och skicka data som samlas in beroende på vilka regler för insamling av data och lösningar som du har aktiverat. 

Om du övervakar datorn med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 kan det vara multi-homed Log Analytics-tjänsten för att samla in data och vidarebefordra till tjänsten och fortfarande övervakas av [Operations Manager ](log-analytics-om-agents.md). Linux-datorer som övervakas av en Operations Manager-hanteringsgrupp som är integrerad med logganalys får inte någon konfiguration för datakällor och vidarebefordra insamlade data till hanteringsgruppen. Windows-agent kan rapportera upp till fyra arbetsytor, medan den Linux-agenten stöder endast rapporterar till en enda arbetsyta.  

Agent för Linux och Windows är inte bara för att ansluta till logganalys, det stöder också Azure Automation till värd Hybrid Runbook worker-rollen och lösningar för hantering, till exempel ändringsspårning och hantering av uppdateringar.  Läs mer om Hybrid Runbook Worker-rollen [Azure Automation Hybrid Runbook Worker](../automation/automation-offering-get-started.md#automation-architecture-overview).  

## <a name="prerequisites"></a>Förutsättningar
Granska följande information för att verifiera att du uppfyller systemkraven innan du börjar.

### <a name="windows-operating-system"></a>Windows-operativsystem
Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2008 Service Pack 1 (SP1) eller senare
* Windows 7 SP1 och senare.

> [!NOTE]
> Agenten för Windows har endast stöd för Transport Layer Security (TLS) 1.0- och 1.1.  

#### <a name="network-configuration"></a>Nätverkskonfiguration
Informationen nedan lista över proxy- och brandväggsinställningarna configuration information som krävs för Windows-agenten kan kommunicera med logganalys. Trafiken är utgående från nätverket till logganalys-tjänsten. 

| Agentresurs | Portar | Kringgå HTTPS-kontroll|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Ja |
|*.oms.opinsights.azure.com | 443 | Ja | 
|*.blob.core.windows.net | 443 | Ja | 
|*.azure-automation.net | 443 | Ja | 

### <a name="linux-operating-systems"></a>Linux-operativsystem
Följande Linux-distributioner stöds officiellt.  Linux-agenten kan också köra på andra distributioner som inte finns.  Om inget annat anges stöds alla mindre versioner för varje större version i listan.  

* Amazon Linux 2012.09 till 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)  
* Oracle Linux 5, 6 och 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

#### <a name="network-configuration"></a>Nätverkskonfiguration
Informationen nedan lista över proxy- och brandväggsinställningarna configuration information som krävs för Linux-agenten kan kommunicera med logganalys.  

|Agentresurs| Portar | Riktning |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | Port 443 | Inkommande och utgående|  
|*.oms.opinsights.azure.com | Port 443 | Inkommande och utgående|  
|*.blob.core.windows.net | Port 443 | Inkommande och utgående|  
|*.azure-automation.net | Port 443 | Inkommande och utgående|  

Linux-agenten stöder kommunikation via en proxyserver eller OMS Gateway till Log Analytics-tjänsten med hjälp av HTTPS-protokollet.  Stöd för både anonyma och grundläggande autentisering (användarnamn/lösenord).  Proxyservern kan anges under installationen eller genom att ändra konfigurationsfilen proxy.conf efter installationen.  

Konfigurationsvärdet proxy har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver autentisera, kräver Linux-agenten fortfarande ett pseudokolumner/lösenord. Detta kan vara alla användarnamn eller lösenord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|användare | Valfritt användarnamn för proxyautentisering |
|lösenord | Lösenord för proxyautentisering |
|proxyhost | Adress eller FQDN för proxy server/OMS Gateway |
|port | Valfria portnumret för proxyservern server/OMS Gateway |

Exempel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som ”@” lösenordet, du får en proxy-anslutningsfel eftersom värdet tolkas felaktigt.  Undvik problemet genom att koda lösenordet i URL-Adressen med ett verktyg som [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installera och konfigurera agenten 
Ansluta dina lokala datorer direkt till Log Analytics kan åstadkommas med hjälp av olika metoder beroende på dina krav. Följande tabell visar varje metod för att avgöra vilka som fungerar bäst i din organisation.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
| Windows-dator|- [Manuell installation](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installera Microsoft Monitoring agent från kommandoraden eller med hjälp av en automatiserad metod, till exempel Azure Automation DSC [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), eller med en Azure Resource Manager-mall om du har distribuerat Microsoft Azure-stacken i ditt datacenter.| 
|Linux-dator| [Manuell installation](log-analytics-quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett wrapper-skript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med logganalys](log-analytics-om-agents.md) | Konfigurera integrering mellan Operations Manager och Log Analytics för att vidarebefordra samlat in data från Linux och Windows-datorer som rapporterar till en hanteringsgrupp.|  

## <a name="next-steps"></a>Nästa steg

* Granska [datakällor](log-analytics-data-sources.md) att förstå datakällorna som är tillgängliga för att samla in data från din Windows- eller Linux-system. 

* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 

* Lär dig mer om [lösningar](log-analytics-add-solutions.md) att lägga till funktioner till logganalys och också samla in data till OMS-databasen.