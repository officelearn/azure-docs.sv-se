---
title: Diagnostisera Linux Hybrid Runbook Worker – Azure Uppdateringshantering
description: Lär dig hur du felsöker och löser problem med Azure Automation Hybrid Runbook Worker på Linux som stöder Uppdateringshantering.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373088"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>Förstå och lösa Linux Hybrid Runbook Worker Health för Uppdateringshantering

Det kan finnas många orsaker till att datorn inte visas som **klar** i uppdateringshantering. I Uppdateringshantering kan du kontrol lera hälso tillståndet för en Hybrid Runbook Worker agent för att fastställa det underliggande problemet. Den här artikeln beskriver hur du kör fel sökaren för Azure-datorer från Azure Portal och datorer som inte är Azure-datorer i [scenariot offline](#troubleshoot-offline).

Följande lista är de tre beredskaps tillstånd som en dator kan vara i:

* **Redo** – hybrid Runbook Worker distribueras och granskades senast för 1 timme sedan.
* **Frånkopplad** – hybrid Runbook Worker distribueras och lästes senast för 1 timme sedan.
* **Inte konfigurerad** – hybrid Runbook Worker hittas inte eller har inte registrerats.

> [!NOTE]
> Det kan uppstå en liten fördröjning mellan det Azure Portal visar och datorns aktuella tillstånd.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

För Azure-datorer öppnar du sidan Felsök **uppdaterings agent** genom att klicka på länken **Felsök** i kolumnen **Uppdatera agent beredskap** i portalen. För datorer som inte är Azure-datorer går länken till den här artikeln. Se offline-instruktionerna för att felsöka en dator som inte är en Azure-dator.

![sidan virtuell dator](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrollerna kräver den virtuella datorn körs. Om den virtuella datorn inte körs visas en knapp för att **starta den virtuella datorn**.

På sidan **Felsök uppdaterings agent** klickar du på **Kör kontroller**för att starta fel sökaren. Fel sökaren använder [Kör-kommandot](../../virtual-machines/linux/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När felsökaren är klar, returnerar resultatet av kontrollerna.

![Felsöka sidan](../media/update-agent-issues-linux/troubleshoot-page.png)

När du är klar returneras resultaten i fönstret. Kryss avsnitten innehåller information om vad varje kontroll söker efter.

![Update-agenten kontrollerar sidan](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Nödvändiga kontroller

### <a name="operating-system"></a>Operativsystem

Operativ system kontrollen verifierar om Hybrid Runbook Worker kör något av följande operativ system:

|Operativsystem  |Anteckningar  |
|---------|---------|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificerings baserad uppdatering kräver ' yum ' för att returnera säkerhets data som CentOS inte har gjort i rutan.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

## <a name="monitoring-agent-service-health-checks"></a>Hälsokontroller för övervakning agent-tjänsten

### <a name="log-analytics-agent"></a>Log Analytics-agent

Den här kontrollen säkerställer att Log Analytics-agenten för Linux är installerad. Anvisningar om hur du installerar den finns i [Installera agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Log Analytics agent status

Den här kontrollen säkerställer att Log Analytics-agenten för Linux körs. Om agenten inte körs kan du köra följande kommando för att försöka starta om den. Mer information om hur du felsöker agenten finns i [Linux hybrid Runbook Worker Troubleshooting](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Den här kontrollen avgör om agenten rapporterar till flera arbetsytor. Flera värdar stöds inte av hantering av uppdateringar.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den här kontrollen kontrollerar om Log Analytics-agenten för Linux har Hybrid Runbook Worker-paketet. Det här paketet måste anges för hantering av uppdateringar ska fungera.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker-status

Den här kontrollen ser till att Hybrid Runbook Worker körs på datorn. Följande processer ska vara tillgänglig om Hybrid Runbook Worker körs korrekt. Mer information finns i [felsöka Log Analytics agent för Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="general-internet-connectivity"></a>Allmän Internetanslutning

Den här kontrollen ser till att datorn har åtkomst till internet.

### <a name="registration-endpoint"></a>Registreringsslutpunkt

Den här kontrollen avgör om Hybrid Runbook Worker kan kommunicera korrekt med Azure Automation arbets ytan Log Analytics.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med slutpunkten för registrering. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Operations-slutpunkt

Den här kontrollen avgör om agenten kan kommunicera med tjänsten jobbet Runtime Data korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med tjänsten jobbet Runtime Data. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics slutpunkt 1

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-2"></a>Log Analytics slutpunkt 2

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-slutpunkten 3

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

## <a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Python-skriptet [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) finns i Script Center. Ett exempel på utdata från det här skriptet visas i följande exempel:

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

Information om hur du felsöker ytterligare problem med dina hybrid Runbook Worker finns i [Felsöka-hybrid Runbook Worker](hybrid-runbook-worker.md).
