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
ms.openlocfilehash: d37dbb85dc85ee8bae0447f18f771dc658de18e3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060246"
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
|cURL | cURL Webbklient | 7.15.5|
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

## <a name="troubleshoot"></a>Felsöka

Information om hur du felsöker Hybrid Runbook Worker-arbeten finns [felsökning Linux Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#linux)

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö finns [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
