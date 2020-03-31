---
title: Azure Automation Linux Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker så att du kan köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2579748d9c68512e51fe46ec70084c30d06953bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278771"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera en Linux Hybrid Runbook Worker

Du kan använda hybridkörningsarbetsfunktionen i Azure Automation för att köra runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Linux Hybrid Runbook Worker kör runbooks som en speciell användare som kan höjas för att köra kommandon som behöver höjd. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera angivna datorer.

I den här artikeln beskrivs hur du installerar Hybrid Runbook Worker på en Linux-dator.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande distributioner:

* Amazon Linux 2012.09 till 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)
* Oracle Linux 5, 6 och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS och 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Installera en Linux Hybrid Runbook Worker

Om du vill installera och konfigurera en Hybrid Runbook Worker på din Linux-dator följer du en enkel process för att manuellt installera och konfigurera rollen. Det kräver att aktivera **Automation Hybrid Worker-lösningen** i din Azure Log Analytics-arbetsyta och sedan köra en uppsättning kommandon för att registrera datorn som arbetare och lägga till den i en grupp.

Minimikraven för en Linux Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="package-requirements"></a>Paketkrav

| **Paketet krävs** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1.0 (TLS 1.1 och TLS 1.2 stöds|
|Curl | cURL-webbklient | 7.15.5|
|Python-ctypes | Python 2.x krävs |
|PAM | Pluggable Authentication Modules|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | Om du vill köra PowerShell-runbooks måste PowerShell installeras, se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar den.  | 6.0.0 |

### <a name="installation"></a>Installation

Innan du fortsätter bör du notera arbetsytan Log Analytics som ditt Automation-konto är länkat till. Observera också den primära nyckeln för ditt Automation-konto. Du kan hitta både från Azure-portalen genom att välja ditt Automation-konto, välja **Arbetsyta** för arbetsytan ID och välja **Nycklar** för primärnyckeln. Information om portar och adresser som du behöver för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

1. Aktivera **Automation Hybrid Worker-lösningen** i Azure med någon av följande metoder:

   * Lägg till **Automation Hybrid Worker-lösningen** i din prenumeration med hjälp av proceduren på [Lägg till Azure Monitor-logglösningar på din arbetsyta](../log-analytics/log-analytics-add-solutions.md).
   * Kör följande cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installera Log Analytics-agenten för Linux genom att köra följande kommando. Ersätt \<\> WorkspaceID \<och\> WorkspaceKey med lämpliga värden från arbetsytan.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Kör följande kommando och ändra värdena för parametrarna *-w*, *-k*, *-g*och *-e*. För parametern *-g* ersätter du värdet med namnet på gruppen Hybrid Runbook Worker som den nya Linux Hybrid Runbook Worker ska ansluta till. Om namnet inte finns i ditt Automation-konto görs en ny Hybrid Runbook Worker-grupp med det namnet.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. När kommandot har slutförts visar sidan **Hybrid worker Groups** i Azure-portalen den nya gruppen och antalet medlemmar. Om detta är en befintlig grupp ökas antalet medlemmar. Du kan välja gruppen i listan på sidan **Hybridarbetaregrupper** och välja panelen **Hybridarbetare.** På sidan **Hybridarbetare** visas varje medlem i gruppen i listan.

> [!NOTE]
> Om du använder Azure Monitor-tillägget för virtuella datorer för `autoUpgradeMinorVersion` Linux för en Virtuell Azure rekommenderar vi att du ställer in för false som automatisk uppgradering av versioner kan orsaka problem som Hybrid Runbook Worker. Mer information om hur du uppgraderar tillägget manuellt finns i [Azure CLI-distributionen ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Inaktivera signaturvalidering

Som standard kräver Linux Hybrid Runbook Workers signaturverifiering. Om du kör en osignerad runbook mot en arbetare visas ett felmeddelande med "Signaturvalidering misslyckades". Om du vill inaktivera signaturverifiering kör du följande kommando. Ersätt den andra parametern med ditt logganalysarbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Runbook-typer som stöds

Linux Hybrid Runbook Workers stöder inte hela uppsättningen runbook-typer i Azure Automation.

Följande runbook-typer fungerar på en Linux Hybrid Worker:

* Python 2 (Svenska)
* PowerShell

  > [!NOTE]
  > PowerShell-runbooks kräver att PowerShell Core installeras på Linux-datorn. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar den.

Följande runbook-typer fungerar inte på en Linux Hybrid Worker:

* PowerShell-arbetsflöde
* Grafisk
* Grafiskt PowerShell-arbetsflöde

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar runbooks för att automatisera processer i ditt lokala datacenter eller annan molnmiljö finns i [Kör runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner om hur du tar bort Hybrid Runbook Workers finns i [Ta bort Azure Automation Hybrid Runbook Workers](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Mer information om hur du felsöker dina Hybrid Runbook-arbetare finns i [Felsöka Linux Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#linux)
