---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som du kan använda för att köra runbooks på Windows-baserade datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2bd9b4f46e28a28f99045319d8ac606cdcee7216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536793"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows Hybrid Runbook Worker

Du kan använda hybridkörningsarbetsfunktionen i Azure Automation för att köra runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Azure Automation lagrar och hanterar runbooks och levererar dem sedan till en eller flera angivna datorer. I den här artikeln beskrivs hur du distribuerar en Hybrid Runbook Worker på en Windows-dator.

När du har distribuerat en runbook-arbetare granskar du [Run runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller annan molnmiljö.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installation och konfiguration av Windows Hybrid Runbook Worker

Om du vill installera och konfigurera en Windows Hybrid Runbook Worker kan du använda någon av följande metoder.

* För virtuella Azure-datorer installerar du Log Analytics-agenten för Windows med hjälp av [tillägget för den virtuella datorn för Windows](../virtual-machines/extensions/oms-windows.md). Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar virtuella datorer till en befintlig Log Analytics-arbetsyta med hjälp av en Azure Resource Manager-mall eller PowerShell. När agenten har installerats kan den virtuella datorn läggas till i en Hybrid Runbook Worker-grupp i ditt Automation-konto. Se steg 3 och 4 i avsnittet [Manuell distribution.](#manual-deployment)

* Använd en Automation-runbook för att helt automatisera processen med att konfigurera en Windows-dator. Detta är den rekommenderade metoden för datorer i ditt datacenter eller en annan molnmiljö.

* Följ en steg-för-steg-procedur för att manuellt installera och konfigurera hybridkörningsarbetsrollen på din icke-Azure-virtuella dator.

> [!NOTE]
> Om du vill hantera konfigurationen av servrar som stöder hybridkörningsarbetsrollen med DSC (Desired State Configuration) måste du lägga till servrarna som DSC-noder.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimikrav för Windows Hybrid Runbook Worker

Minimikraven för en Windows Hybrid Runbook Worker är:

* Windows Server 2012 eller senare
* Windows PowerShell 5.1 eller senare[(hämta WMF 5.1)](https://www.microsoft.com/download/details.aspx?id=54616)
* .NET Framework 4.6.2 eller senare
* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="network-configuration"></a>Nätverkskonfiguration

Mer information om nätverkskrav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Serverinklädnad för hantering med Automation DSC

Information om introduktionsservrar för hantering med DSC finns i [Onboarding-datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).

Om du aktiverar [lösningen update management](../operations-management-suite/oms-solution-update-management.md) konfigureras alla Windows-datorer som är anslutna till log analytics-arbetsytan automatiskt som en Hybrid Runbook Worker för att stödja runbooks som ingår i lösningen. Den här arbetaren är dock inte registrerad med några Hybrid Runbook Worker-grupper som redan har definierats i ditt Automation-konto.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Tillägg av datorn till en hybridkörningsarbetsgrupp

Du kan lägga till arbetsdatorn i en hybridkörningsarbetsgrupp i ditt Automation-konto. Observera att du måste stödja Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Automatiserad distribution

På måldatorn utför du följande steg för att automatisera installationen och konfigurationen av windows hybridarbetsbetarrollen.

### <a name="step-1---download-the-powershell-script"></a>Steg 1 - Hämta PowerShell-skriptet

Ladda ner skriptet **New-OnPremiseHybridWorker.ps1** från [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Hämtningen ska vara direkt från datorn som kör rollen Hybrid Runbook Worker eller från en annan dator i din miljö. När du har hämtat skriptet kopierar du det till arbetaren. Skriptet **New-OnPremiseHybridWorker.ps1** använder de parametrar som beskrivs nedan under körningen.

| Parameter | Status | Beskrivning |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obligatorisk | Namnet på den resursgrupp som är kopplad till ditt Automation-konto. |
| `AutomationAccountName` | Obligatorisk | Namnet på ditt Automation-konto.
| `Credential` | Valfri | Autentiseringsuppgifterna som ska användas när du loggar in i Azure-miljön. |
| `HybridGroupName` | Obligatorisk | Namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som stöder det här scenariot. |
| `OMSResourceGroupName` | Valfri | Namnet på resursgruppen för log analytics-arbetsytan. Om den här resursgruppen inte har `AAResourceGroupName` angetts används värdet för. |
| `SubscriptionID` | Obligatorisk | Identifieraren för Azure-prenumerationen som är kopplad till ditt Automation-konto. |
| `TenantID` | Valfri | Identifieraren för klientorganisationen som är kopplad till ditt Automation-konto. |
| `WorkspaceName` | Valfri | Namnet på Logganalysarbetsytan. Om du inte har en Log Analytics-arbetsyta skapar och konfigurerar skriptet en. |

> [!NOTE]
> När du aktiverar lösningar stöder Azure Automation bara vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. En lista över mappningspar som stöds finns i [Regionmappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Steg 2 - Öppna kommandoradsgränssnittet i Windows PowerShell

Öppna **Windows PowerShell** från **Startskärmen** i administratörsläge.

### <a name="step-3---run-the-powershell-script"></a>Steg 3 - Kör PowerShell-skriptet

Bläddra till mappen som innehåller skriptet som du har hämtat i powershellkommandoraden. Ändra värdena för `AutomationAccountName` `AAResourceGroupName`parametrarna , `OMSResourceGroupName`, , `HybridGroupName`, `SubscriptionID`och `WorkspaceName`. Kör sedan skriptet.

Du uppmanas att autentisera med Azure när du har kört skriptet. Du måste logga in med ett konto som är medlem i rollen Prenumerationsadministratörer och medadministratör för prenumerationen.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Steg 4 - Installera NuGet

Du uppmanas att godkänna att installera NuGet och autentisera med dina Azure-autentiseringsuppgifter. Om du inte har den senaste NuGet-versionen kan du hämta den från [Tillgängliga NuGet-distributionsversioner](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Steg 5 – Verifiera distributionen

När skriptet är klart visar sidan Hybridarbetaregrupper den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökas antalet medlemmar. Du kan välja gruppen i listan på sidan Hybridarbetaregrupper och välja panelen **Hybridarbetare.** På sidan Hybridarbetare kan du se varje medlem i gruppen i listan.

## <a name="manual-deployment"></a>Manuell distribution

På målmaskinen utför du de två första stegen en gång för din Automation-miljö. Utför sedan de återstående stegen för varje arbetsdator.

### <a name="step-1---create-a-log-analytics-workspace"></a>Steg 1 - Skapa en log analytics-arbetsyta

Om du inte redan har en Log Analytics-arbetsyta läser du [designvägledningen](../azure-monitor/platform/design-logs-deployment.md) för Azure Monitor Log innan du skapar arbetsytan.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Steg 2 - Lägg till Automation-lösningen på log analytics-arbetsytan

Automation-lösningen lägger till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du lägger till lösningen på logganalysarbetsytan flyttas den automatiskt till agentdatorn de arbetskomponenter som du installerar enligt beskrivningen i nästa steg.

Om du vill lägga till Automation-lösningen på arbetsytan kör du följande PowerShell-cmdlet.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Steg 3 – Installera Log Analytics-agenten för Windows

Log Analytics-agenten för Windows ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till arbetsytan hämtas automatiskt de komponenter som krävs för Hybrid Runbook Worker.

Om du vill installera agenten på datorn följer du instruktionerna på [Anslut Windows-datorer till Azure Monitor-loggar](../log-analytics/log-analytics-windows-agent.md). Du kan upprepa den här processen för flera datorer för att lägga till flera arbetare i din miljö.

När agenten har anslutit till logganalysarbetsytan efter några minuter kan du köra följande fråga för att kontrollera att pulsslagsdata skickas till arbetsytan.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

I sökresultaten bör du se pulsslagsposter för datorn, vilket anger att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslagspost till den tilldelade arbetsytan. 

Följ följande steg för att slutföra agentinstallationen och installationen.

1. Aktivera lösningen för att ombord på agentmaskinen. Se [Inbyggda datorer på arbetsytan](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Kontrollera att agenten har laddat ned Automation-lösningen korrekt. Den bör ha en mapp som heter **AzureAutomationFiles** i **C:\Program Files\Microsoft Monitoring Agent\Agent**. 
3. Om du vill bekräfta versionen av Hybrid Runbook Worker bläddrar du till **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** och noterar versionsundermappen. **version**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Steg 4 – Installera runbook-miljön och anslut till Azure Automation

När du konfigurerar en agent för att rapportera till en `HybridRegistration` Log Analytics-arbetsyta trycker `Add-HybridRunbookWorker` Automation-lösningen ned PowerShell-modulen, som innehåller cmdleten. Använd den här cmdleten för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Öppna en PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Kör nu `Add-HybridRunbookWorker` cmdlet med hjälp av följande syntax.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan få den information som krävs för den här cmdleten från sidan Hantera nycklar i Azure-portalen. Öppna den här sidan genom att välja **Nycklar** på sidan Inställningar i ditt Automation-konto.

![Sidan Hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Använd `GroupName` namnet på gruppen Hybrid Runbook Worker för parametern. Om den här gruppen redan finns i Automation-kontot läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.
* För `EndPoint` parametern använder du **URL-posten** på sidan Hantera nycklar.
* För `Token` parametern använder du posten **PRIMÄR ÅTKOMSTNYCKEL** på sidan Hantera nycklar.
* Om det behövs `Verbose` anger du parametern för att få information om installationen.

### <a name="step-5----install-powershell-modules"></a>Steg 5 - Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlets som definierats i modulerna som är installerade i din Azure Automation-miljö. Eftersom dessa moduler inte distribueras automatiskt till lokala datorer måste du installera dem manuellt. Undantaget är Azure-modulen. Den här modulen installeras som standard och ger åtkomst till cmdlets för alla Azure-tjänster och -aktiviteter för Azure Automation.

Eftersom det primära syftet med hybridkörningsarbetsfunktionen är att hantera lokala resurser, måste du troligen `PowerShellGet` installera modulerna som stöder dessa resurser, särskilt modulen. Information om hur du installerar Windows PowerShell-moduler finns i [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Moduler som installeras måste finnas på en `PSModulePath` plats som refereras av miljövariabeln så att hybridarbetaren automatiskt kan importera dem. Mer information finns [i Installera moduler i PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar runbooks för att automatisera processer i ditt lokala datacenter eller annan molnmiljö finns i [Kör runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner om hur du tar bort Hybrid Runbook Workers finns i [Ta bort Azure Automation Hybrid Runbook Workers](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Mer information om hur du felsöker hybridkörningsarbetare finns i [Felsöka Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows).
* Ytterligare steg för felsökning av problem med uppdateringshantering finns i [Uppdatera hantering: felsökning](troubleshoot/update-management.md).
