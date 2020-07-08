---
title: Distribuera ett Linux-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du installerar en Azure Automation Hybrid Runbook Worker för att köra Runbooks på Linux-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 0fc7951411f1c8956b4816f9031a8b7c26594b42
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856136"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera ett Linux-Hybrid Runbook Worker

Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Linux-Hybrid Runbook Worker Kör Runbooks som en särskild användare som kan utökas för att köra kommandon som behöver förhöjda. Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera angivna datorer. Den här artikeln beskriver hur du installerar Hybrid Runbook Worker på en Linux-dator, hur du tar bort en arbets grupp och hur du tar bort en Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

## <a name="prerequisites"></a>Krav

Kontrol lera att du har följande innan du börjar:

### <a name="a-log-analytics-workspace"></a>En Log Analytics arbets yta

Hybrid Runbook Worker-rollen beror på en Azure Monitor Log Analytics arbets yta för att installera och konfigurera rollen. Du kan skapa den genom [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Om du inte har en Azure Monitor Log Analytics arbets yta granskar du [rikt linjerna för Azure Monitor logg design](../azure-monitor/platform/design-logs-deployment.md) innan du skapar arbets ytan.

Om du har en arbets yta, men inte är länkad till ditt Automation-konto, så lägger en automatiserings funktion till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du aktiverar någon av de Azure Automation funktionerna i Log Analytics-arbetsytan, specifikt [uppdateringshantering](automation-update-management.md) eller [ändringsspårning och inventering](change-tracking.md), flyttas Worker-komponenterna automatiskt till agent datorn.

Om du vill lägga till funktionen Uppdateringshantering i din arbets yta kör du följande PowerShell-cmdlet:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

Om du vill lägga till funktionen Ändringsspårning och inventering i arbets ytan kör du följande PowerShell-cmdlet:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics-agent

Hybrid Runbook Worker-rollen kräver [Log Analytics-agenten](../azure-monitor/platform/log-analytics-agent.md) för det Linux-operativsystem som stöds.

### <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande distributioner:

* Amazon Linux 2012,09 till 2015,09 (x86/x64)
* CentOS Linux 5, 6, och 7 (x86/x64)
* Oracle Linux 5, 6, och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6, och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS och 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

### <a name="minimum-requirements"></a>Minimikrav

Minimi kraven för en Linux-Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1,0 (TLS 1,1 och TLS 1,2 stöds)|
|Curl | webb klient för sväng | 7.15.5|
|Python – ctypes | Python 2. x krävs |
|PAM | Pluggable Authentication Modules|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | För att köra PowerShell-Runbooks måste PowerShell-kärnan installeras. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det. | 6.0.0 |

## <a name="supported-runbook-types"></a>Runbook-typer som stöds

Linux hybrid Runbook Worker stöder en begränsad uppsättning Runbook-typer i Azure Automation, och de beskrivs i följande tabell.

|Typ av Runbook | Stöds |
|-------------|-----------|
|Python 2 |Ja |
|PowerShell |Ja<sup>1</sup> |
|PowerShell-arbetsflöde |No |
|Grafisk |No |
|Grafiskt PowerShell-arbetsflöde |No |

<sup>1</sup> PowerShell-Runbooks kräver att PowerShell Core installeras på Linux-datorn. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installera en Linux-Hybrid Runbook Worker

Utför följande steg för att installera och konfigurera en Linux-Hybrid Runbook Worker.

1. Distribuera Log Analytics-agenten till mål datorn.

    * För virtuella Azure-datorer installerar du Log Analytics agent för Linux med hjälp av [tillägget för virtuella datorer för Linux](../virtual-machines/extensions/oms-linux.md). Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics arbets yta med en Azure Resource Manager mall eller Azure CLI. När agenten har installerats kan den virtuella datorn läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.

    * För virtuella datorer som inte är Azure-datorer installerar du Log Analytics agent för Linux med hjälp av de distributions alternativ som beskrivs i artikeln [Anslut Linux-datorer till Azure Monitor](../azure-monitor/platform/agent-linux.md) . Du kan upprepa den här processen för flera datorer för att lägga till flera arbetare i din miljö. När agenten har installerats kan de virtuella datorerna läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.

    > [!NOTE]
    > Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC) måste du lägga till datorerna som DSC-noder.

    > [!NOTE]
    > [Nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) med motsvarande sudo-behörigheter måste finnas under installationen av Linux-hybrid Worker. Om du försöker installera Worker och kontot inte finns eller inte har rätt behörighet, Miss lyckas installationen.

2. Verifiera att agenten rapporterar till arbets ytan.

    Log Analytics-agenten för Linux ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

    När agenten har anslutit till Log Analytics arbets ytan efter några minuter kan du köra följande fråga för att kontrol lera att den skickar pulsslags data till arbets ytan.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    I Sök resultaten bör du se pulsslags poster för datorn, vilket indikerar att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslags post till den tilldelade arbets ytan.

3. Kör följande kommando för att lägga till datorn i en hybrid Runbook Worker grupp och ange värden för parametrarna `-w` , `-k` , `-g` , och `-e` .

    Du kan hämta den information som krävs för parametrar `-k` och `-e` från sidan **nycklar** i ditt Automation-konto. Välj **nycklar** under avsnittet **konto inställningar** på sidans vänstra sida.

    ![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e`Kopiera värdet för **URL**för parametern.

    * För `-k` parametern kopierar du värdet för **primär åtkomst nyckel**.

    * För `-g` parametern anger du namnet på den hybrid Runbook Worker grupp som den nya Linux hybrid Runbook Work ska ansluta till. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns skapas den med det namnet.

    * För `-w` parametern anger du Log Analytics arbetsyte-ID.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. När kommandot har slutförts visar sidan Hybrid Worker grupper i ditt Automation-konto den nya gruppen och antalet medlemmar. Om det här är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen i listan på sidan Hybrid Worker grupper och sedan välja panelen **hybrid personal** . På sidan hybrid Worker visas varje medlem i gruppen i listan.

    > [!NOTE]
    > Om du använder tillägget Log Analytics virtuell dator för Linux för en virtuell Azure-dator rekommenderar vi att du ställer in `autoUpgradeMinorVersion` till `false` as-versioner för automatisk uppgradering kan orsaka problem med hybrid Runbook Worker. Information om hur du uppgraderar tillägget manuellt finns i [Azure CLI-distribution](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Inaktivera verifiering av signatur

Som standard kräver Linux hybrid Runbook Worker verifiering av signatur. Om du kör en osignerad Runbook mot en arbets grupp visas ett `Signature validation failed` fel meddelande. Kör följande kommando för att inaktivera verifiering av signatur. Ersätt den andra parametern med ditt Log Analytics arbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker från en lokal Linux-dator

Du kan använda kommandot `ls /var/opt/microsoft/omsagent` på Hybrid Runbook Worker för att hämta arbetsyte-ID: t. En mapp skapas med namnet med arbetsyte-ID: t.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Det här skriptet tar inte bort Log Analytics agenten för Linux från datorn. Den tar bara bort funktionaliteten och konfigurationen av Hybrid Runbook Workers rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en Hybrid Runbook Worker grupp Linux-datorer använder du samma steg som för en Windows hybrid Worker-grupp. Se [ta bort en hybrid Worker grupp](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
