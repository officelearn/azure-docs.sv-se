---
title: Förstå agenten kontrollera resultaten i Azure-uppdateringshantering
description: Lär dig hur du felsöker problem med hantering av agenten.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 1b258d115e7d9962ecab4b93dbcd98d13f5977c7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956688"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Förstå agenten kontrollera resultaten i hantering av uppdateringar

Det kan finnas flera orsaker som inte är Azure-datorn inte visas **redo** i hantering av uppdateringar. Du kan kontrollera hälsotillståndet för en Hybrid Worker-agent för att fastställa det underliggande problemet i hantering av uppdateringar. Den här artikeln beskrivs hur du kör Felsökaren från Azure-portalen och offline-scenarier.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

Genom att klicka på den **Felsök** länka den **uppdatera Agentberedskap** kolumn i portalen kan du starta den **felsöka Update-agenten** sidan. Den här sidan visar problem med agenten och en länk till den här artikeln för att hjälpa dig att felsöka dina problem.

![sidan virtuell dator](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Kontrollerna kräver den virtuella datorn körs. Om den virtuella datorn inte körs visas en knapp för att **starta den virtuella datorn**.

På den **felsöka Update-agenten** klickar du på **kör kontrollerar**, för att starta felsökningen. Felsökaren använder [Körningskommando](../../virtual-machines/windows/run-command.md) att köra ett skript på datorn för att verifiera de beroenden som agenten har. När felsökaren är klar, returnerar resultatet av kontrollerna.

![Felsöka sidan](../media/update-agent-issues/troubleshoot-page.png)

När du är klar returneras resultaten i fönstret. Den [avsnitten](#pre-requisistes-checks) innehåller information om vad varje kontroll är ute efter.

![Update-agenten kontrollerar sidan](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Nödvändiga kontroller

### <a name="operating-system"></a>Operativsystem

OS-kontroll verifierar om Hybrid Runbook Worker kör något av följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder bara uppdatera utvärderingar.         |
|Windows Server 2008 R2 SP1 och senare     |.NET framework 4.5 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 eller senare krävs. ([Hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet.  ([Hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. 'Yum' kräver klassificeringsbaserad uppdatering för att returnera säkerhetsdata som CentOS inte har direkt.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS och 16.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

### <a name="net-45"></a>.NET 4.5

.NET framework-kontroll verifierar om systemet har minst [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653) finns.

### <a name="wmf-51"></a>WMF 5.1

Kontrollen WMF, kontrollerar om systemet har nödvändig version av Windows Management Framework. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) är den lägsta versionen som stöds. Vi rekommenderar att du installerar [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) för ökad tillförlitlighet för Hybrid Runbook Worker.

### <a name="tls-12"></a>TLS 1.2

Den här kontrollen avgör om du använder TLS 1.2 för att kryptera kommunikationen. TLS 1.0 längre stöds inte av plattformen och det rekommenderas att klienter använder TLS 1.2 ska kunna kommunicera med hantering av uppdateringar.

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="registration-endpoint"></a>Registreringsslutpunkt

Den här kontrollen avgör om agenten kan kommunicera med agenttjänsten korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med slutpunkten för registrering. En lista över adresser och portar som ska öppnas finns [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Operations-slutpunkt

Den här kontrollen avgör om agenten kan kommunicera med tjänsten jobbet Runtime Data korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med tjänsten jobbet Runtime Data. En lista över adresser och portar som ska öppnas finns [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Hälsokontroller för VM-tjänsten

### <a name="monitoring-agent-service-status"></a>Övervaka agentstatus-tjänsten

Den här kontrollen anger om Microsoft Monitoring Agent `HealthService` körs på datorn.

Mer information om felsökning av tjänsten finns [The Microsoft Monitoring Agent körs inte](hybrid-runbook-worker.md#mma-not-running).

Installera Microsoft Monitoring Agent, se [installera och konfigurera Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Monitoring agent-tjänsten-händelser

Den här kontrollen avgör om det har gjorts någon `4502` händelser i Operations Manager logga in på datorn under de senaste 24 timmarna.

Läs mer om den här händelsen i den [felsökningsguide för](hybrid-runbook-worker.md#event-4502) för den här händelsen.

## <a name="access-permissions-checks"></a>Åtkomstkontroller för behörigheter

### <a name="machinekeys-folder-access"></a>MachineKeys mappåtkomst

Crypto mappen åtkomstkontrollen avgör om lokala systemkontot har åtkomst till `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Skriptet [Felsök WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) kan hittas på PowerShell-galleriet. Ett exempel på utdata från det här skriptet visas i följande exempel:

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

Felsökning av ytterligare problem med Hybrid Runbook Worker beskrivs [Felsök - Hybrid Runbook Worker](hybrid-runbook-worker.md)
