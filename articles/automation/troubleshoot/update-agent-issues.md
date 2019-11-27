---
title: Förstå kontroll resultatet av Windows-agenten i Azure Uppdateringshantering
description: Lär dig hur du felsöker problem med hantering av agenten.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 72fdfe912a5560ce0c0e3886dd3c56cf9534dc22
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480771"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Förstå kontroll resultatet av Windows-agenten i Uppdateringshantering

Det kan finnas många orsaker till att datorn inte visas som **klar** i uppdateringshantering. Du kan kontrollera hälsotillståndet för en Hybrid Worker-agent för att fastställa det underliggande problemet i hantering av uppdateringar. Den här artikeln beskriver hur du kör fel sökaren för Azure-datorer från Azure Portal och datorer som inte är Azure-datorer i [scenariot offline](#troubleshoot-offline).

Följande lista är de tre beredskaps tillstånd som en dator kan vara i:

* **Klar** – uppdaterings agenten har distribuerats och setts senast under 1 timme sedan.
* **Frånkopplad** – uppdaterings agenten distribueras och lästes senast för 1 timme sedan.
* **Inte konfigurerad** – uppdaterings agenten hittades inte eller har inte kon figurer ATS.

> [!NOTE]
> Det kan uppstå en liten fördröjning mellan det Azure Portal visar och datorns aktuella tillstånd.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

För Azure-datorer öppnar du sidan Felsök **uppdaterings agent** genom att klicka på länken **Felsök** i kolumnen **Uppdatera agent beredskap** i portalen. För datorer som inte är Azure-datorer går länken till den här artikeln. Se [offline-instruktionerna](#troubleshoot-offline) för att felsöka en dator som inte är en Azure-dator.

![Uppdaterings hanterings lista över virtuella datorer](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Om du vill kontrol lera agentens hälso tillstånd måste den virtuella datorn köras. Om den virtuella datorn inte körs visas knappen **starta den virtuella datorn** .

På sidan **Felsök uppdaterings agent** väljer du **Kör kontroller** för att starta fel sökaren. Fel sökaren använder [kommandot kör](../../virtual-machines/windows/run-command.md) för att köra ett skript på datorn för att verifiera agent beroenden. När fel sökaren är färdig returneras resultatet av kontrollerna.

![Felsöka sidan uppdatera agent](../media/update-agent-issues/troubleshoot-page.png)

Resultaten visas på sidan när de är klara. I avsnittet kontroller visas vad som ingår i varje kontroll.

![Felsök uppdateringar av agent kontroller](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Nödvändiga kontroller

### <a name="operating-system"></a>Operativsystem

Operativ system kontrollen verifierar om Hybrid Runbook Worker kör något av följande operativ system:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Stöder endast uppdaterings bedömningar.         |
|Windows Server 2008 R2 SP1 och senare |.NET Framework 4,6 eller senare krävs. ([Ladda ned .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5,1 krävs.  ([Ladda ned Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET-4.6.2

.NET Framework kontrollen verifierar att systemet har minst [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) installerat.

### <a name="wmf-51"></a>WMF 5,1

WMF-kontrollen verifierar att systemet har den version av Windows Management Framework (WMF) som krävs – [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Den här kontrollen avgör om du använder TLS 1,2 för att kryptera din kommunikation. TLS 1,0 stöds inte längre av plattformen. Vi rekommenderar att klienter använder TLS 1,2 för att kommunicera med Uppdateringshantering.

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="registration-endpoint"></a>Registreringsslutpunkt

Den här kontrollen avgör om agenten kan kommunicera korrekt med Agent tjänsten.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med slutpunkten för registrering. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Operations-slutpunkt

Den här kontrollen avgör om agenten kan kommunicera korrekt med jobbets körnings data tjänst.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med tjänsten jobbet Runtime Data. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Hälso kontroller för VM-tjänst

### <a name="monitoring-agent-service-status"></a>Övervaknings agent tjänst status

Den här kontrollen avgör om `HealthService`, Microsoft Monitoring Agent körs på datorn.

Mer information om hur du felsöker tjänsten finns i [Microsoft Monitoring Agent körs inte](hybrid-runbook-worker.md#mma-not-running).

Information om hur du installerar om Microsoft Monitoring Agent finns i [Installera och konfigurera Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Övervaka agent tjänst händelser

Den här kontrollen avgör om `4502` händelser visas i Azure Operations Manager-loggen på datorn under de senaste 24 timmarna.

Mer information om den här händelsen finns i [fel söknings guiden](hybrid-runbook-worker.md#event-4502) för den här händelsen.

## <a name="access-permissions-checks"></a>Åtkomst behörighets kontroller

### <a name="machinekeys-folder-access"></a>Åtkomst till MachineKeys-mapp

Åtkomst kontrollen för krypto avgör om det lokala system kontot har åtkomst till C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda fel sökaren på en Hybrid Runbook Worker offline genom att köra skriptet lokalt. Du kan hämta skriptet, [Felsöka-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), i PowerShell-galleriet. Du måste ha WMF 4,0 eller senare installerat för att kunna köra skriptet. Information om hur du hämtar den senaste versionen av PowerShell finns i [installera olika versioner av PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Utdata från det här skriptet ser ut som i följande exempel:

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

Om du vill felsöka fler problem med dina hybrid Runbook Worker läser du [Felsöka hybrid Runbook Worker](hybrid-runbook-worker.md).

