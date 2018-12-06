---
title: Förstå Windows-agenten kontrollera resultaten i Azure uppdateringshantering
description: Lär dig hur du felsöker problem med hantering av agenten.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 68bac54de4f6a9f16f54032b10031bcf7222c676
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969319"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Förstå Windows-agenten kontrollera resultaten i hantering av uppdateringar

Det finns många orsaker till varför en dator i Azure inte kanske visar en **redo** status i Azure uppdateringshantering. Du kan kontrollera hälsotillståndet för en Hybrid Worker-agent för att fastställa underliggande problem i hantering av uppdateringar. Den här artikeln beskriver hur du kör Felsökaren för uppdateringshantering från Azure-portalen och offline-scenarier.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

I Azure-portalen i **felsöka Update-agenten** sidan visar problem med agenten. På sidan finns en länk till den här artikeln kan hjälpa dig med felsökning av problem. Gå till den **felsöka Update-agenten** väljer den **felsöka** länken i den **uppdatera Agentberedskap** kolumn.

![Uppdatera av hanteringslistan över virtuella datorer](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Om du vill kontrollera hälsotillståndet för en agent måste du köra den virtuella datorn. Om den virtuella datorn inte körs kan en **starta den virtuella datorn** visas knappen.

På den **felsöka Update-agenten** väljer **kör kontroller för** vill starta felsökaren. Felsökaren använder [kör kommandot](../../virtual-machines/windows/run-command.md) att köra ett skript på datorn för att verifiera beroenden för klientagenten. När felsökaren är klar, returnerar resultatet av kontrollerna.

![Felsöka Update Agent-sidan](../media/update-agent-issues/troubleshoot-page.png)

Resultaten visas på sidan när den är klar. Den [kontrollerar avsnitt](#prerequisiste-checks) visar vad som ingår i varje kontroll.

![Felsöka Update-agenten kontroller](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Nödvändiga kontroller

### <a name="operating-system"></a>Operativsystem

I operativsystemet kontrollerar om Hybrid Runbook Worker kör något av dessa operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Stöder bara uppdatera utvärderingar.         |
|Windows Server 2008 R2 SP1 och senare |.NET framework 4.5.1 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 eller senare krävs. ([Hämta Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet.  ([Hämta Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

.NET Framework kontrollerar att systemet har minst [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) installerad.

### <a name="wmf-51"></a>WMF 5.1

WMF kontrollen kontrollerar att systemet har versionen som krävs av Windows Management Framework (WMF). [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) är den tidigaste versionen som stöds. Vi rekommenderar att du installerar [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) att öka tillförlitligheten för Hybrid Runbook Worker.

### <a name="tls-12"></a>TLS 1.2

Den här kontrollen avgör om du använder TLS 1.2 för att kryptera kommunikationen. TLS 1.0 stöds inte längre av plattformen. Vi rekommenderar att klienter använder TLS 1.2 ska kunna kommunicera med hantering av uppdateringar.

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="registration-endpoint"></a>Registreringsslutpunkt

Den här kontrollen avgör om agenten kan kommunicera med agenttjänsten korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med slutpunkten för registrering. En lista över adresser och portar som ska öppnas finns i [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Operations-slutpunkt

Den här kontrollen avgör om agenten kan kommunicera med tjänsten jobbet Runtime Data korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med tjänsten jobbet Runtime Data. En lista över adresser och portar som ska öppnas finns i [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Hälsokontroller för VM-tjänsten

### <a name="monitoring-agent-service-status"></a>Övervaka agentstatus-tjänsten

Den här kontrollen avgör om `HealthService`, Microsoft Monitoring Agent körs på datorn.

Mer information om felsökning av tjänsten finns [The Microsoft Monitoring Agent körs inte](hybrid-runbook-worker.md#mma-not-running).

Installera Microsoft Monitoring Agent, se [installera och konfigurera Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoring agent-tjänsten-händelser

Den här kontrollen avgör om någon `4502` händelserna som visas i Azure Operations Manager-loggen på datorn under de senaste 24 timmarna.

Läs mer om den här händelsen i den [felsökningsguide för](hybrid-runbook-worker.md#event-4502) för den här händelsen.

## <a name="access-permissions-checks"></a>Åtkomstkontroller för behörigheter

### <a name="machinekeys-folder-access"></a>MachineKeys mappåtkomst

Åtkomstkontrollen krypto mappen bestämmer om det lokala systemkontot har åtkomst till C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren på en Hybrid Runbook Worker offline genom att köra skriptet lokalt. Du kan hämta skriptet [Felsök WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), i PowerShell-galleriet. Utdata från det här skriptet ser ut som i följande exempel:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Nästa steg

Felsökning av problem med mer med Hybrid Runbook Worker Se [felsöka Hybrid Runbook Worker](hybrid-runbook-worker.md).
