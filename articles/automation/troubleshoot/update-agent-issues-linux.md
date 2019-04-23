---
title: Förstå Linux-agenten kontrollera resultaten i Azure uppdateringshantering
description: Lär dig hur du felsöker problem med hantering av agenten.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150123"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Förstå Linux-agenten kontrollera resultaten i hantering av uppdateringar

Det kan finnas många orsaker till din dator inte visas **redo** i hantering av uppdateringar. Du kan kontrollera hälsotillståndet för en Hybrid Worker-agent för att fastställa det underliggande problemet i hantering av uppdateringar. Den här artikeln beskrivs hur du kör Felsökaren för datorer i Azure från Azure-portalen och icke-Azure-datorer i den [offlinescenario](#troubleshoot-offline).

I följande lista är en dator kan vara i för tre beredskapstillstånd:

* **Redo** -update-agenten har distribuerats och sågs senast mindre än 1 timme sedan.
* **Frånkopplad** -update-agenten har distribuerats och sågs senast över 1 timme sedan.
* **Inte konfigurerad** -update-agenten inte hittas eller har inte slutförts onboarding.

> [!NOTE]
> Det kan finnas en fördröjning mellan Azure-portalen visar och det aktuella tillståndet för datorn.

## <a name="start-the-troubleshooter"></a>Starta felsökaren

För datorer i Azure, klicka på den **Felsök** länka den **uppdatera Agentberedskap** kolumn i portalen startar den **felsöka Update-agenten** sidan. För icke-Azure-datorer ger länken du till den här artikeln. Se offline anvisningarna för att felsöka en icke-Azure-dator.

![sidan virtuell dator](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrollerna kräver den virtuella datorn körs. Om den virtuella datorn inte körs visas en knapp för att **starta den virtuella datorn**.

På den **felsöka Update-agenten** klickar du på **kör kontrollerar**, för att starta felsökningen. Felsökaren använder [Körningskommando](../../virtual-machines/linux/run-command.md) att köra ett skript på datorn för att verifiera de beroenden som agenten har. När felsökaren är klar, returnerar resultatet av kontrollerna.

![Felsöka sidan](../media/update-agent-issues-linux/troubleshoot-page.png)

När du är klar returneras resultaten i fönstret. Kontrollera-avsnitt innehåller information om vad varje kontroll är ute efter.

![Update-agenten kontrollerar sidan](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Nödvändiga kontroller

### <a name="operating-system"></a>Operativsystem

OS-kontroll verifierar om Hybrid Runbook Worker kör något av följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. 'Yum' kräver klassificeringsbaserad uppdatering för att returnera säkerhetsdata som CentOS inte har direkt.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

## <a name="monitoring-agent-service-health-checks"></a>Hälsokontroller för övervakning agent-tjänsten

### <a name="oms-agent"></a>OMS-agenten

Den här kontrollen säkerställer att OMS-agenten för Linux är installerad. Anvisningar för hur du installerar den finns i [installera agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Status för OMS-Agent

Den här kontrollen säkerställer att OMS-agenten för Linux körs. Om agenten inte körs måste köra du följande kommando för att försöka starta om den. Läs mer om hur du felsöker agenten [Linux Hybrid Runbook worker felsökning](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Den här kontrollen avgör om agenten rapporterar till flera arbetsytor. Flera värdar stöds inte av hantering av uppdateringar.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Det här kontrollerar om OMS-agenten för Linux har Hybrid Runbook Worker-paketet. Det här paketet måste anges för hantering av uppdateringar ska fungera.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker-status

Den här kontrollen ser till att Hybrid Runbook Worker körs på datorn. Följande processer ska vara tillgänglig om Hybrid Runbook Worker körs korrekt. Mer information finns i [felsökning Log Analytics-agenten för Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Anslutningskontroller

### <a name="general-internet-connectivity"></a>Allmän Internetanslutning

Den här kontrollen ser till att datorn har åtkomst till internet.

### <a name="registration-endpoint"></a>Registreringsslutpunkt

Den här kontrollen avgör om agenten kan kommunicera med agenttjänsten korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med slutpunkten för registrering. En lista över adresser och portar som ska öppnas finns [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Operations-slutpunkt

Den här kontrollen avgör om agenten kan kommunicera med tjänsten jobbet Runtime Data korrekt.

Proxy och brandvägg konfigurationer måste tillåta Hybrid Runbook Worker-agenten kan kommunicera med tjänsten jobbet Runtime Data. En lista över adresser och portar som ska öppnas finns [Network planera för Hybrid Worker-arbeten](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics slutpunkt 1

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-2"></a>Log Analytics slutpunkt 2

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-slutpunkten 3

Det här kontrollerar att datorn har åtkomst till de slutpunkter som krävs av Log Analytics-agenten.

## <a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda felsökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Python-skriptet [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) finns i Script Center. Ett exempel på utdata från det här skriptet visas i följande exempel:

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

Felsökning av ytterligare problem med Hybrid Runbook Worker beskrivs [Felsök - Hybrid Runbook Worker](hybrid-runbook-worker.md)

