---
title: Azure Automation Linux Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som gör det möjligt att köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e95f5d585fa97a62b709e73b6ed6eacafe69a2b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Så här distribuerar du en Linux Hybrid Runbook Worker

Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Linux Hybrid Runbook Worker kör runbooks som en särskild användare som kan utökas för att köra kommandon som behöver höjning. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer. Den här artikeln beskrivs hur du installerar Runbook Worker-hybriden på en Linux-dator.

## <a name="supported-linux-operating-systems"></a>Stöds Linux-operativsystem

Följande är en lista med Linux-distributioner som stöds:

* Amazon Linux 2012.09--> 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)
* Oracle Linux 5, 6 och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux in Server 11 och 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Installera Linux Hybrid Runbook Worker

Om du vill installera och konfigurera en Hybrid Runbook Worker på Linux-dator, kan du följa en direkt framåt process för att manuellt installera och konfigurera rollen. Det krävs att aktivera den **Automation Hybrid Worker** lösning i logganalys-arbetsytan och sedan köra en uppsättning kommandon för att registrera datorn som en arbetare och lägga till den i en ny eller befintlig grupp.

Följande är minimikraven för en Linux Hybrid Runbook Worker:

* Minst två kärnor
* Minst 4 GB RAM-minne
* Port 443 (utgående)

### <a name="package-requirements"></a>Krav för paketet

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 0.9.8e eller 1.0|
|CURL | cURL Webbklient | 7.15.5|
|Python-ctypes | |
|PAM | PAM-moduler|

Innan du fortsätter måste du Observera logganalys-arbetsytan Automation-kontot är länkat till och den primära nyckeln för ditt Automation-konto. Du kan hitta båda från portalen genom att välja Automation-kontot och markera **arbetsytan** för arbetsyte-ID och välja **nycklar** för den primära nyckeln. Mer information om portar och adresser som behövs för Runbook Worker-hybriden finns [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

1. Aktivera lösningen ”Automation Hybrid Worker” i Azure. Detta kan göras genom att antingen:

   1. Lägg till den **Automation Hybrid Worker** lösningen till din prenumeration med hjälp av proceduren på [lägga till logganalys hanteringslösningar till din arbetsyta](../log-analytics/log-analytics-add-solutions.md).
   1. Kör följande cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installera OMS-agent för Linux genom att köra följande kommando ersätter \<WorkspaceID\> och \<WorkspaceKey\> med lämpliga värden från arbetsytan.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Kör följande kommando, ändra värdena för parametrarna *-w*, *-k*, *-g*, och *-e*. För den *-g* parameter, Ersätt värdet med namnet på Hybrid Runbook Worker-grupp som ska ansluta till den nya Linux Hybrid Runbook Worker. Om namnet inte redan finns i ditt Automation-konto, görs en ny Hybrid Runbook Worker-grupp med det namnet.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. När kommandot har slutförts, sidan Hybrid Worker-grupper i Azure portal visas den nya gruppen och antal medlemmar eller om en befintlig grupp, ökar antalet medlemmar. Du kan välja grupp från listan på den **Hybrid Worker grupper** och välja den **hybrider** panelen. På den **hybrider** kan du se varje medlem i gruppen i listan.

## <a name="turning-off-signature-validation"></a>Om du inaktiverar signaturverifiering

Som standard kräver Linux Hybrid Runbook Worker signaturverifiering. Om du kör en osignerad runbook mot en arbetare finns ett fel som innehåller ”signaturverifieringen misslyckades”. Om du vill inaktivera signaturverifiering, kör du följande kommando och ersätter den andra parametern med din logganalys arbetsyte-ID:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Stöds runbook-typer

Linux Hybrid Runbook Worker stöder inte den fullständiga uppsättningen av runbook-typer som finns i Azure automation.

Följande typer av runbook fungerar på en Linux Worker-Hybrid:

* Python 2
* PowerShell

Följande typer av runbook fungerar inte på en Linux Worker-Hybrid:

* PowerShell-arbetsflöde
* Grafisk
* Grafisk PowerShell-arbetsflöde

## <a name="troubleshooting"></a>Felsökning

Linux Hybrid Runbook Worker beror på OMS-Agent för Linux för att kommunicera med ditt Automation-konto för registrering worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:

### <a name="the-oms-agent-for-linux-is-not-running"></a>OMS-Agent för Linux körs inte

Om OMS-Agent för Linux inte körs, förhindrar detta Linux Hybrid Runbook Worker från att kommunicera med Azure Automation. Kontrollera att agenten körs genom att ange följande kommando: `ps -ef | grep python`. Du bör se utdata som liknar följande, python-processer med **nxautomation** användarkonto. Om hantering av uppdateringar eller Azure Automation-lösningar inte är aktiverade, körs inget av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux Hybrid Runbook Worker. De befinner sig i den `/var/opt/microsoft/omsagent/state/automationworker/` directory.

* **OMS.conf** -detta är manager arbetsprocessen, detta är direkt från DSC.

* **Worker.conf** -den här processen är automatiskt registrerad Hybrid arbetsprocessen, den har startats av hanteraren för arbetaren. Den här processen används av hantering av uppdateringar och är transparent för användaren. Den här processen är inte tillgänglig om uppdateringshantering lösningen inte är aktiverat på datorn.

* **diy/Worker.conf** -den här processen är själv hybrid arbetsprocessen. Arbetsprocessen själv hybrid används för att köra runbooks för användaren på Hybrid Runbook Worker. Den bara skiljer sig från automatisk registrerade Hybrid arbetsprocessen i de viktiga detaljer som använder en annan konfiguration. Den här processen är inte närvarande om Azure-Automation lösning inte har aktiverats och själv Linux Hybrid Worker inte registreras.

Om OMS-Agent för Linux inte körs, kör följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>Den angivna klassen finns inte

Om du ser felet **finns inte den angivna klassen...** i den `/var/opt/microsoft/omsconfig/omsconfig.log` sedan OMS-Agent för Linux måste uppdateras. Kör följande kommando för att installera om OMS-agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Ytterligare anvisningar om hur du felsöker problem med hantering av uppdateringar finns [uppdateringshantering - felsökning](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Nästa steg

* Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
