---
title: Azure Automation Linux Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker så att du kan köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268124"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera en Linux Hybrid Runbook Worker

Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Linux Hybrid Runbook Worker kör runbooks som en särskild användare som kan utökas för att köra kommandon som behöver höjning. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.

Den här artikeln beskriver hur du installerar Runbook Worker-hybriden på en Linux-dator.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Hybrid Runbook Worker-funktionen stöder följande distributioner:

* Amazon Linux 2012.09 till 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)
* Oracle Linux 5, 6 och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS och 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Installera en Linux Hybrid Runbook Worker

Om du vill installera och konfigurera en Hybrid Runbook Worker på Linux-dator, kan du följa en enkel process för att manuellt installera och konfigurera rollen. Det krävs att aktivera den **Automation Hybrid Worker** lösning i Azure logganalys-arbetsytan och sedan köra en uppsättning kommandon för att registrera datorn som en arbetare och lägga till den i en grupp.

Minimikraven för en Linux Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="package-requirements"></a>Krav för paketet

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 0.9.8e eller 1.0|
|CURL | cURL Webbklient | 7.15.5|
|Python-ctypes | |
|PAM | PAM-moduler|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | Om du vill köra PowerShell-runbooks PowerShell måste vara installerad finns [installera PowerShell Core på Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) information om hur du installerar den.  | 6.0.0 |

### <a name="installation"></a>Installation

Observera logganalys-arbetsytan som ditt Automation-konto är kopplat till innan du fortsätter. Tänk också på den primära nyckeln för ditt Automation-konto. Du kan hitta båda från Azure-portalen genom att välja ditt Automation-konto genom att välja **arbetsytan** för arbetsyte-ID och välja **nycklar** för den primära nyckeln. Mer information om portar och adresser som du behöver för Runbook Worker-hybriden finns [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

1. Aktivera den **Automation Hybrid Worker** lösning i Azure genom att använda någon av följande metoder:

   * Lägg till den **Automation Hybrid Worker** lösningen till din prenumeration med hjälp av proceduren på [lägga till logganalys hanteringslösningar till din arbetsyta](../log-analytics/log-analytics-add-solutions.md).
   * Kör följande cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installera OMS-Agent för Linux genom att köra följande kommando. Ersätt \<WorkspaceID\> och \<WorkspaceKey\> med lämpliga värden från arbetsytan.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Kör följande kommando, ändra värdena för parametrarna *-w*, *-k*, *-g*, och *-e*. För den *-g* parameter, Ersätt värdet med namnet på Hybrid Runbook Worker-grupp som ska ansluta till den nya Linux Hybrid Runbook Worker. Om namnet inte finns i ditt Automation-konto, görs en ny Hybrid Runbook Worker-grupp med det namnet.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. När kommandot har slutförts i **Hybrid Worker grupper** sidan i Azure portal visas den nya gruppen och antalet medlemmar. Om det här är en befintlig grupp stegvis antalet medlemmar. Du kan välja grupp från listan på den **Hybrid Worker grupper** och välja den **hybrider** panelen. På den **hybrider** kan du se varje medlem i gruppen i listan.

## <a name="turning-off-signature-validation"></a>Om du inaktiverar signaturverifiering

Som standard kräver Linux Hybrid Runbook Worker signaturverifiering. Om du kör en osignerad runbook mot en arbetare, visas ett felmeddelande som säger ”signaturverifieringen misslyckades”. Kör följande kommando om du vill inaktivera signaturverifiering. Ersätt den andra parametern med ditt logganalys arbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Stöds runbook-typer

Linux Hybrid Runbook Worker stöder inte den fullständiga uppsättningen av runbook-typer i Azure Automation.

Följande typer av runbook fungerar på en Linux Worker-Hybrid:

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell-runbooks kräver PowerShell Core installeras på Linux-dator. Se [installera PowerShell Core på Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) information om hur du installerar den.

Följande typer av runbook fungerar inte på en Linux Worker-Hybrid:

* PowerShell-arbetsflöde
* Grafisk
* Grafisk PowerShell-arbetsflöde

## <a name="troubleshooting"></a>Felsökning

Linux Hybrid Runbook Worker beror på OMS-Agent för Linux för att kommunicera med ditt Automation-konto för registrering worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, kan vissa möjliga orsaker till felet.

### <a name="the-oms-agent-for-linux-isnt-running"></a>OMS-Agent för Linux körs inte

Om OMS-Agent för Linux inte kör kan inte Linux Hybrid Runbook Worker kommunicera med Azure Automation. Kontrollera att agenten är igång genom att ange kommandot `ps -ef | grep python`. 

Du bör se utdata som liknar följande (Python bearbetar med den **nxautomation** användarkonto). Om uppdateringshantering eller Azure Automation-lösningen inte är aktiverad, körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Följande processer startas för en Linux Hybrid Runbook Worker. De är placerade i den `/var/opt/microsoft/omsagent/state/automationworker/` directory.

* **OMS.conf**: Detta är manager arbetsprocessen. Den startas direkt från önskad tillstånd Configuration DSC ().

* **Worker.conf**: det här är automatiskt registrerad Hybrid Worker-process. Den har startats av hanteraren för arbetaren. Den här processen används av hantering av uppdateringar och är transparent för användaren. Den här processen finns endast om uppdateringshantering lösningen är aktiverat på datorn.

* **diy/Worker.conf**: Detta är själv hybrid arbetsprocessen. Arbetsprocessen själv hybrid används för att köra runbooks för användaren på Hybrid Runbook Worker. Den skiljer sig från processen för att automatiskt registrera Hybrid Worker endast att den använder en annan konfiguration. Den här processen finns bara om Azure Automation-lösningen är aktiverad och själv Linux Hybrid Worker registreras.

Om OMS-Agent för Linux inte körs, kör följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>Den angivna klassen finns inte

Om du ser felet ”den angivna klassen finns inte” i `/var/opt/microsoft/omsconfig/omsconfig.log`, OMS-Agent för Linux måste uppdateras. Kör följande kommando för att installera om OMS-agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Ytterligare anvisningar om hur du felsöker problem med hantering av uppdateringar finns [uppdateringshantering: felsökning](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö finns [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
