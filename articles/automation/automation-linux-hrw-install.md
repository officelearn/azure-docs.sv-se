---
title: Azure Automation Linux Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker så att du kan köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller moln.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cc07aa9c1b2c540c33949a8c591bd98f91b04666
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738867"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera en Linux Hybrid Runbook Worker

Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Linux Hybrid Runbook Worker kör runbooks som en särskild användare som kan utökas för att köra kommandon som behöver höjning. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera angivna datorer.

Den här artikeln beskriver hur du installerar den Hybrid Runbook Worker på en Linux-dator.

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

Om du vill installera och konfigurera en Hybrid Runbook Worker på Linux-datorn, kan du följa en enkel process för att manuellt installera och konfigurera rollen. Det kräver att aktivera den **Automation Hybrid Worker** lösning i Azure Log Analytics-arbetsytan och sedan köra en uppsättning kommandon för att registrera datorn som en arbetare och lägga till den i en grupp.

Minimikraven för en Linux Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="package-requirements"></a>Krav för paketet

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1.0 (TLS 1.1 och TLS 1.2 stöds|
|Curl | cURL webbklienten | 7.15.5|
|Python-ctypes | |
|PAM | Pluggable Authentication Modules|
| **Valfria paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | Om du vill köra PowerShell-runbooks PowerShell måste vara installerad, se [installera PowerShell Core på Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) att lära dig hur du installerar den.  | 6.0.0 |

### <a name="installation"></a>Installation

Observera Log Analytics-arbetsytan och Automation-kontot är länkat till innan du fortsätter. Tänk också på den primära nyckeln för ditt Automation-konto. Du kan hitta båda från Azure-portalen genom att välja ditt Automation-konto, välja **arbetsytan** för arbetsyte-ID och välja **nycklar** för den primära nyckeln. Information om portar och adresser som ska användas för att Hybrid Runbook Worker finns [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

1. Aktivera den **Automation Hybrid Worker** lösning i Azure genom att använda någon av följande metoder:

   * Lägg till den **Automation Hybrid Worker** lösningen till din prenumeration med hjälp av proceduren på [lägga till Azure Monitor loggar lösningar på din arbetsyta](../log-analytics/log-analytics-add-solutions.md).
   * Kör följande cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installera Log Analytics-agenten för Linux genom att köra följande kommando. Ersätt \<WorkspaceID\> och \<WorkspaceKey\> med lämpliga värden från din arbetsyta.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Kör följande kommando, ändring av värdena för parametrarna *-w*, *-k*, *-g*, och *-e*. För den *-g* parametern och Ersätt värdet med namnet på den Hybrid Runbook Worker-grupp som ska ansluta till den nya Linux Hybrid Runbook Worker. Om namnet inte finns i ditt Automation-konto, görs en ny Hybrid Runbook Worker-grupp med det namnet.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. När kommandot har slutförts, den **Hybrid Worker-grupper** visar den nya gruppen och antalet medlemmar i Azure-portalen. Om det här är en befintlig grupp ökas antalet medlemmar. Du kan välja grupp i listan på den **Hybrid Worker-grupper** och välj den **Hybrid Worker-arbeten** panelen. På den **Hybrid Worker-arbeten** du se att varje medlem i gruppen i listan.

> [!NOTE]
> Om du använder Azure Monitor-tillägget för virtuell dator för Linux för en Azure virtuell dator rekommenderar vi att ställa `autoUpgradeMinorVersion` till FALSKT som automatiskt uppgradera versioner kan orsaka problem med Hybrid Runbook Worker. Läs hur du uppgraderar tillägget manuellt i [distribution av Azure CLI ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Om du inaktiverar signaturverifiering

Som standard kräver Linux Hybrid Runbook Worker verifiera signaturen. Om du kör ett osignerat runbook mot en arbetsroll kan se du ett meddelande om att ”signaturverifiering misslyckades”. Kör följande kommando om du vill inaktivera verifiera signaturen. Ersätt den andra parametern med din log analytics arbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Stöds runbook-typer

Linux Hybrid Runbook Worker stöder inte den fullständiga uppsättningen runbook-typer i Azure Automation.

Följande typer av runbooks fungerar på en Linux Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell-runbooks kräver PowerShell Core installeras på Linux-dator. Se [installera PowerShell Core på Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) att lära dig hur du installerar den.

Följande typer av runbooks fungerar inte på en Linux Hybrid Worker:

* PowerShell-arbetsflöde
* Grafiska
* Grafiskt PowerShell-arbetsflöde

## <a name="next-steps"></a>Nästa steg

* Läs hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra moln i [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner om hur du tar bort Hybrid Runbook Worker finns [ta bort Azure Automation Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Läs hur du felsöker Hybrid Runbook Worker-arbeten i [felsökning Linux Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#linux)