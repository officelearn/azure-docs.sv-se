---
title: Diagnostisera Windows Hybrid Runbook Worker - Azure Update Management
description: Lär dig hur du felsöker och löser problem med Azure Automation Hybrid Runbook Worker i Windows som stöder uppdateringshantering.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ec35d11eba59ea21947e2c3cd5286bababa4eabb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153862"
---
# <a name="understand-and-resolve-windows-hybrid-runbook-worker-health-in-update-management"></a>Förstå och lösa hälsa för Windows Hybrid Runbook Worker i uppdateringshantering

Det kan finnas många orsaker till att datorn inte visar **Klar** i uppdateringshantering. I Uppdateringshantering kan du kontrollera hälsotillståndet för en Hybrid Runbook Worker-agent för att fastställa det underliggande problemet. I den hÃ¤r artikeln beskrivs hur du kÃ¶r felsökaren för Azure-datorer frÃ¶r Azure-portalen och icke-Azure-datorer i [offlinescenariot](#troubleshoot-offline).

Följande lista är de tre beredskapstillstånd som en maskin kan vara i:

* **Ready** - Hybrid Runbook Worker distribueras och sågs senast för mindre än 1 timme sedan.
* **Frånkopplad** - Hybrid Runbook Worker distribueras och sågs senast för över 1 timme sedan.
* **Inte konfigurerad** – Hybrid Runbook Worker hittades inte eller har inte slutförts.

> [!NOTE]
> Det kan finnas en liten fördröjning mellan vad Azure-portalen visar och datorns aktuella tillstånd.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

För Azure-datorer startar sidan **Felsöka uppdateringsagent** om du klickar på länken **Felsöka** under kolumnen **Beredskap för uppdateringsagenter** i portalen. För datorer som inte är Azure tar länken dig till den här artikeln. Se [offlineinstruktionerna](#troubleshoot-offline) för att felsöka en annan dator än Azure.

![Uppdateringshanteringslista över virtuella datorer](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> För att kontrollera hälsotillståndet för Hybrid Runbook Worker måste den virtuella datorn köras. Om den virtuella datorn inte körs visas knappen **Starta den virtuella datorn.**

På sidan **Felsöka uppdateringsagent** väljer du **Kör checkar** för att starta felsökaren. Felsökaren använder [Kör kommando](../../virtual-machines/windows/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När felsökaren är klar returneras resultatet av checkarna.

![Felsöka sidan Update Agent](../media/update-agent-issues/troubleshoot-page.png)

Resultaten visas på sidan när de är klara. Kontrollavsnitten visar vad som ingår i varje kontroll.

![Felsöka uppdateringsagentkontroller](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kravkontroller

### <a name="operating-system"></a>Operativsystem

Operativsystemets kontroll kontrollerar om Hybrid Runbook Worker kör något av följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2012 och senare |.NET Framework 4.6 eller senare krävs. (Ladda[ner .NET Framework)](/dotnet/framework/install/guide-for-developers)<br/> Windows PowerShell 5.1 krävs.  (Ladda[ner Windows Management Framework 5.1)](https://www.microsoft.com/download/details.aspx?id=54616)        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework-kontrollen verifierar att systemet har minst [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) installerat.

### <a name="wmf-51"></a>WMF 5.1

WMF-kontrollen verifierar att systemet har den version som krävs av WMF (Windows Management Framework) – [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Den här kontrollen avgör om du använder TLS 1.2 för att kryptera din kommunikation. TLS 1.0 stöds inte längre av plattformen. Vi rekommenderar att klienter använder TLS 1.2 för att kommunicera med uppdateringshantering.

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="registration-endpoint"></a>Slutpunkt för registrering

Den här kontrollen avgör om agenten kan kommunicera korrekt med agenttjänsten.

Proxy- och brandväggskonfigurationer måste tillåta Hybrid Runbook Worker-agenten att kommunicera med registreringsslutpunkten. En lista över adresser och portar som ska öppnas finns i [Nätverksplanering för hybridarbetare](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Slutpunkt för operationer

Den här kontrollen avgör om agenten kan kommunicera korrekt med datatjänsten För jobbkörning.

Proxy- och brandväggskonfigurationer måste tillåta hybridkörningsarbetsagenten att kommunicera med datatjänsten Jobbkörning. En lista över adresser och portar som ska öppnas finns i [Nätverksplanering för hybridarbetare](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Hälsokontroller av vm-tjänster

### <a name="monitoring-agent-service-status"></a>Tjänststatus för övervakning av agent

Den här kontrollen `HealthService`avgör om Microsoft Monitoring Agent körs på datorn.

Mer information om felsökning av tjänsten finns i [Microsoft Monitoring Agent körs inte](hybrid-runbook-worker.md#mma-not-running).

Om du vill installera om Microsoft Monitoring Agent finns i [Installera och konfigurera Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Övervaka agenttjänsthändelser

Den här kontrollen `4502` avgör om några händelser visas i Azure Operations Manager-loggen på datorn under de senaste 24 timmarna.

Mer information om den här händelsen finns i [felsökningsguiden](hybrid-runbook-worker.md#event-4502) för den här händelsen.

## <a name="access-permissions-checks"></a>Kontroller av åtkomstbehörigheter

### <a name="machinekeys-folder-access"></a>Åtkomst till mappen MachineKeys

Åtkomstkontrollen för kryptomappen avgör om det lokala systemkontot har åtkomst till C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren på en Hybrid Runbook Worker offline genom att köra skriptet lokalt. Du kan hämta skriptet, [Felsöka-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), i PowerShell-galleriet. Du måste ha WMF 4.0, eller mer, installerat för att kunna köra skriptet. Mer om du vill hämta den senaste versionen av PowerShell finns i [Installera olika versioner av PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Utdata för det här skriptet ser ut som följande exempel:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
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

Information om hur du felsöker fler problem med hybridkörningsarbetare finns i [Felsöka Hybrid Runbook Workers](hybrid-runbook-worker.md).
