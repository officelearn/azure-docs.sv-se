---
title: Felsöka problem med Linux-uppdaterings agenten i Azure Automation Uppdateringshantering
description: Lär dig hur du felsöker och löser problem med Linux Windows Update-agenten med hjälp av Uppdateringshantering-lösningen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 1f9c8d449fb060d5b1a5f810f9e387057eac3252
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927980"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Felsök problem med Linux-uppdaterings agent

Det kan finnas många orsaker till att datorn inte visas som klar (felfri) i Uppdateringshantering. Du kan kontrol lera hälso tillståndet för en Linux Hybrid Runbook Worker-agent för att fastställa det underliggande problemet. Följande är tre beredskaps tillstånd för en dator:

* Klart: Hybrid Runbook Worker har distribuerats och setts senast för en timme sedan.
* Frånkopplad: Hybrid Runbook Worker distribueras och lästes senast en timme sedan.
* Inte konfigurerad: det Hybrid Runbook Worker inte att hitta eller slutföra registreringen.

> [!NOTE]
> Det kan finnas en liten fördröjning mellan det Azure Portal visar och datorns aktuella tillstånd.

Den här artikeln beskriver hur du kör fel sökaren för Azure-datorer från Azure Portal och datorer som inte är Azure-datorer i [scenariot offline](#troubleshoot-offline). 

> [!NOTE]
> Fel söknings skriptet dirigerar för närvarande inte trafik via en proxyserver om en sådan har kon figurer ATS.

## <a name="start-the-troubleshooter"></a>Starta fel sökaren

För Azure-datorer väljer du **fel söknings** länken under kolumnen **Uppdatera agent beredskap** i portalen för att öppna sidan Felsök uppdaterings agent. För datorer som inte är Azure-datorer går länken till den här artikeln. Information om hur du felsöker en icke-Azure-dator finns i anvisningarna i avsnittet "Felsöka offline".

![Sidan VM-lista](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrollerna kräver att den virtuella datorn körs. Om den virtuella datorn inte körs visas **starta den virtuella datorn** .

På sidan Felsök uppdaterings agent väljer du **Kör kontroller** för att starta fel sökaren. Fel sökaren använder [Kör-kommandot](../../virtual-machines/linux/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När fel sökaren är färdig returneras resultatet av kontrollerna.

![Fel söknings sida](../media/update-agent-issues-linux/troubleshoot-page.png)

När kontrollerna är klara returneras resultatet i fönstret. Kryss avsnitten innehåller information om vad varje kontroll söker efter.

![Uppdatera agent kontroller Sidan](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kravkontroller

### <a name="operating-system"></a>Operativsystem

Operativ system kontrollen verifierar om Hybrid Runbook Worker kör något av följande operativ system.

|Operativsystem  |Anteckningar  |
|---------|---------|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificerings baserad uppdatering kräver "yum" för att returnera säkerhets data, vilket CentOS inte har gjort.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

## <a name="monitoring-agent-service-health-checks"></a>Övervaka Agent tjänstens hälso kontroller

### <a name="log-analytics-agent"></a>Log Analytics-agent

Den här kontrollen säkerställer att Log Analytics-agenten för Linux är installerad. Anvisningar om hur du installerar den finns i [Installera agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Log Analytics agent status

Den här kontrollen säkerställer att Log Analytics-agenten för Linux körs. Om agenten inte körs kan du köra följande kommando för att försöka starta om den. Mer information om hur du felsöker agenten finns i [Linux hybrid Runbook Worker fel sökning](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Den här kontrollen avgör om agenten rapporterar till flera arbets ytor. Multihoming stöds inte av Uppdateringshantering.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den här kontrollen kontrollerar om Log Analytics-agenten för Linux har Hybrid Runbook Worker-paketet. Det här paketet krävs för att Uppdateringshantering ska fungera.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker status

Den här kontrollen säkerställer att Hybrid Runbook Worker körs på datorn. Följande processer bör finnas om Hybrid Runbook Worker körs på rätt sätt. Mer information finns i [felsöka Log Analytics agent för Linux](hybrid-runbook-worker.md#oms-agent-not-running).

> [!NOTE]
> Om Hybrid Runbook Worker inte körs och drift slut punkten har misslyckats kan uppdateringen Miss lyckas. Uppdateringshantering laddar ned hybrid Worker-paketen från slut punkten för åtgärder.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Anslutnings kontroller

### <a name="general-internet-connectivity"></a>Allmän Internet anslutning

Den här kontrollen ser till att datorn har åtkomst till Internet.

### <a name="registration-endpoint"></a>Registrerings slut punkt

Den här kontrollen avgör om Hybrid Runbook Worker kan kommunicera korrekt med Azure Automation i arbets ytan Log Analytics.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med registrerings slut punkten. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Åtgärds slut punkt

Den här kontrollen avgör om agenten kan kommunicera korrekt med jobb körnings data tjänsten.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med jobb körnings data tjänsten. En lista över adresser och portar som ska öppnas finns i [nätverks planering för Hybrid arbetare](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics slut punkt 1

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

### <a name="log-analytics-endpoint-2"></a>Log Analytics slut punkt 2

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

### <a name="log-analytics-endpoint-3"></a>Log Analytics slut punkt 3

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda fel sökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Python-skriptet [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)finns i Script Center. Ett exempel på utdata från det här skriptet visas i följande exempel:

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

Information om hur du felsöker ytterligare problem med dina hybrid Runbook Worker finns i [Felsöka hybrid Runbook Worker](hybrid-runbook-worker.md).
