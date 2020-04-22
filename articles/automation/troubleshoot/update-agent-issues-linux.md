---
title: Felsöka problem med Linux-uppdateringsagenten i Azure Automation Update Management
description: Lär dig hur du felsöker och löser problem med Linux Windows Update-agenten med hjälp av lösningen för uppdateringshantering.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679064"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Felsöka problem med Linux-uppdateringsagenten

Det kan finnas många orsaker till att din dator inte visas som redo (felfri) i Uppdateringshantering. I Uppdateringshantering kan du kontrollera hälsotillståndet för en Hybrid Runbook Worker-agent för att fastställa det underliggande problemet. I den hÃ¤r artikeln beskrivs hur du kÃ¶r felsökaren för Azure-datorer frÃ¶r Azure-portalen och icke-Azure-datorer i [offlinescenariot](#troubleshoot-offline). 

Följande lista är de tre beredskapstillstånd som en maskin kan vara i:

* Ready - Hybrid Runbook Worker distribueras och sågs senast för mindre än 1 timme sedan.
* Frånkopplad - Hybrid Runbook Worker distribueras och sågs senast för över 1 timme sedan.
* Inte konfigurerad – Hybrid Runbook Worker hittades inte eller har inte slutförts.

> [!NOTE]
> Det kan finnas en liten fördröjning mellan vad Azure-portalen visar och det aktuella tillståndet för en dator.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

För Azure-datorer startar sidan **Felsöka** uppdateringsagent om du klickar på länken Felsöka under kolumnen **Beredskap för uppdateringsagenter** i portalen. För datorer som inte är Azure tar länken dig till den här artikeln. Se offlineinstruktionerna för att felsöka en annan dator än Azure.

![vm-listsida](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrollerna kräver att den virtuella datorn körs. Om den virtuella datorn inte körs visas knappen **Starta den virtuella datorn.**

På sidan Felsöka uppdateringsagent klickar du på **Kör checkar**för att starta felsökaren. Felsökaren använder [kommandot Kör](../../virtual-machines/linux/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När felsökaren är klar returneras resultatet av checkarna.

![Felsöka sida](../media/update-agent-issues-linux/troubleshoot-page.png)

När det är klart returneras resultaten i fönstret. Kontrollavsnitten ger information om vad varje kontroll letar efter.

![Sidan Uppdatera agentkontroller](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kravkontroller

### <a name="operating-system"></a>Operativsystem

Operativsystemets kontroll verifierar om Hybrid Runbook Worker kör något av följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificeringsbaserad korrigering kräver "yum" för att returnera säkerhetsdata som CentOS inte har ur lådan.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

## <a name="monitoring-agent-service-health-checks"></a>Hälsokontroller av övervakningsagenttjänsten

### <a name="log-analytics-agent"></a>Log Analytics-agent

Den här kontrollen säkerställer att Log Analytics-agenten för Linux är installerad. Instruktioner om hur du installerar det finns i [Installera agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status för Logganalysagent

Den här kontrollen säkerställer att Log Analytics-agenten för Linux körs. Om agenten inte körs kan du köra följande kommando för att försöka starta om den. Mer information om felsökning av agenten finns i [felsökning av Linux Hybrid Runbook-arbetare](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming (olika)

Den här kontrollen avgör om agenten rapporterar till flera arbetsytor. Multi-homing stöds inte av Uppdateringshantering.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den här kontrollen verifierar om Log Analytics-agenten för Linux har Hybrid Runbook Worker-paketet. Det här paketet krävs för att uppdateringshantering ska fungera.

### <a name="hybrid-runbook-worker-status"></a>Hybrid runbook-arbetarstatus

Den här kontrollen kontrollerar att Hybrid Runbook Worker körs på datorn. Följande processer bör finnas om Hybrid Runbook Worker körs korrekt. Mer information finns [i felsöka Log Analytics Agent för Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="general-internet-connectivity"></a>Allmän internetanslutning

Denna kontroll ser till att maskinen har tillgång till Internet.

### <a name="registration-endpoint"></a>Slutpunkt för registrering

Den här kontrollen avgör om Hybrid Runbook Worker kan kommunicera korrekt med Azure Automation arbetsytan Log Analytics.

Proxy- och brandväggskonfigurationer måste tillåta Hybrid Runbook Worker-agenten att kommunicera med registreringsslutpunkten. En lista över adresser och portar som ska öppnas finns i [Nätverksplanering för hybridarbetare](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Slutpunkt för operationer

Den här kontrollen avgör om agenten kan kommunicera korrekt med datatjänsten För jobbkörning.

Proxy- och brandväggskonfigurationer måste tillåta hybridkörningsarbetsagenten att kommunicera med datatjänsten Jobbkörning. En lista över adresser och portar som ska öppnas finns i [Nätverksplanering för hybridarbetare](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Slutpunkt för Logganalys 1

Den här kontrollen verifierar att datorn har åtkomst till de slutpunkter som behövs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-2"></a>Slutpunkt för Logganalys 2

Den här kontrollen verifierar att datorn har åtkomst till de slutpunkter som behövs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-3"></a>Slutpunkt för Logganalys 3

Den här kontrollen verifierar att datorn har åtkomst till de slutpunkter som behövs av Log Analytics-agenten.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Python-skriptet, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) finns i Skriptcenter. Ett exempel på utdata från det här skriptet visas i följande exempel:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker ytterligare problem med hybridkörningsarbetare finns i [Felsöka - Hybrid Runbook Workers](hybrid-runbook-worker.md).
