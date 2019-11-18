---
title: Azure Automation Linux Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker så att du kan köra Runbooks på Linux-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8e497d18e39a199f34ff76b11b0e6c2c213f35fb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129851"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera ett Linux-Hybrid Runbook Worker

Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Linux-Hybrid Runbook Worker Kör Runbooks som en särskild användare som kan utökas för att köra kommandon som behöver förhöjda. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera angivna datorer.

Den här artikeln beskriver hur du installerar Hybrid Runbook Worker på en Linux-dator.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande distributioner:

* Amazon Linux 2012,09 till 2015,09 (x86/x64)
* CentOS Linux 5, 6, och 7 (x86/x64)
* Oracle Linux 5, 6, och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6, och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS och 16,04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Installera en Linux-Hybrid Runbook Worker

Om du vill installera och konfigurera en Hybrid Runbook Worker på Linux-datorn följer du en enkel process för att manuellt installera och konfigurera rollen. Det kräver att du aktiverar **Automation hybrid Worker** -lösningen i Azure Log Analytics-arbetsytan och sedan kör en uppsättning kommandon för att registrera datorn som en anställd och lägga till den i en grupp.

Minimi kraven för en Linux-Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM-minne
* Port 443 (utgående)

### <a name="package-requirements"></a>Paket krav

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1,0 (TLS 1,1 och TLS 1,2 stöds|
|Klammerparentes | webb klient för sväng | 7.15.5|
|Python – ctypes | Python 2. x krävs |
|PAM | Pluggable Authentication Modules|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | För att köra PowerShell-Runbooks måste PowerShell installeras, se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det.  | 6.0.0 |

### <a name="installation"></a>Installation

Notera Log Analytics arbets ytan som ditt Automation-konto är länkat till innan du fortsätter. Observera också den primära nyckeln för ditt Automation-konto. Du kan hitta båda från Azure Portal genom att välja ditt Automation-konto, välja **arbets yta** för arbetsyte-ID och välja **nycklar** för den primära nyckeln. Information om portar och adresser som du behöver för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

1. Aktivera **Automation hybrid Worker** lösning i Azure med någon av följande metoder:

   * Lägg till **Automation hybrid Worker** -lösningen i din prenumeration genom att följa anvisningarna i [lägga till Azure Monitor loggar lösningar på din arbets yta](../log-analytics/log-analytics-add-solutions.md).
   * Kör följande cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installera Log Analytics agent för Linux genom att köra följande kommando. Ersätt \<WorkspaceID\> och \<WorkspaceKey\> med lämpliga värden från din arbets yta.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Kör följande kommando och ändra värdena för parametrarna *-w*, *-k*, *-g*och *-e*. För parametern *-g* ersätter du värdet med namnet på den hybrid Runbook Worker grupp som den nya Linux-hybrid Runbook Worker ska ansluta till. Om namnet inte finns i ditt Automation-konto görs en ny Hybrid Runbook Worker grupp med det namnet.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. När kommandot har slutförts visar sidan **hybrid Worker grupper** i Azure Portal den nya gruppen och antalet medlemmar. Om det här är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen i listan på sidan **hybrid Worker grupper** och sedan välja panelen **hybrid personal** . På sidan **hybrid Worker** visas varje medlem i gruppen i listan.

> [!NOTE]
> Om du använder tillägget Azure Monitor virtuell dator för Linux för en virtuell Azure-dator rekommenderar vi att du anger `autoUpgradeMinorVersion` till falskt eftersom automatiska uppgraderings versioner kan orsaka problem med Hybrid Runbook Worker. Information om hur du uppgraderar tillägget manuellt finns i [Azure CLI-distribution ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Inaktiverar verifiering av signatur

Som standard kräver Linux hybrid Runbook Worker verifiering av signatur. Om du kör en osignerad Runbook mot en arbets grupp visas ett fel meddelande om att det inte gick att validera signaturen. Kör följande kommando för att inaktivera verifiering av signatur. Ersätt den andra parametern med Log Analytics-arbetsyte-ID: t.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Runbook-typer som stöds

Linux hybrid Runbook Worker stöder inte den fullständiga uppsättningen av Run Book typer i Azure Automation.

Följande Runbook-typer fungerar på en Linux-Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell-Runbooks kräver att PowerShell Core installeras på Linux-datorn. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det.

Följande Runbook-typer fungerar inte på en Linux-Hybrid Worker:

* PowerShell-arbetsflöde
* Bild
* Grafiskt PowerShell-arbetsflöde

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner för hur du tar bort hybrid Runbook Worker finns i [ta bort Azure Automation hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Information om hur du felsöker dina hybrid Runbook Worker finns i [Felsöka Linux hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#linux)
