---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som du kan använda för att köra Runbooks på Windows-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649967"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera angivna datorer. I den här artikeln beskrivs hur du distribuerar Hybrid Runbook Worker på en Windows-dator.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installation och konfiguration av Windows Hybrid Runbook Worker

Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker kan du använda någon av följande metoder.

* För virtuella Azure-datorer installerar du Log Analytics agent för Windows med hjälp av [tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md). Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics arbets yta med en Azure Resource Manager-mall eller med PowerShell. När agenten har installerats kan den virtuella datorn läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto enligt **steg 4** under avsnittet [manuell distribution](#manual-deployment) nedan.

* Använd en Automation-Runbook för att helt automatisera processen med att konfigurera en Windows-dator. Detta är den rekommenderade metoden för datorer i ditt data Center eller i en annan moln miljö.

* Följ en steg-för-steg-procedur för att manuellt installera och konfigurera Hybrid Runbook Worker-rollen på den virtuella datorn som inte är en Azure-dator.

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som har stöd för Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC), måste du lägga till dem som DSC-noder.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimi krav för Windows-Hybrid Runbook Worker

Minimi kraven för en Windows-Hybrid Runbook Worker är:

* Windows Server 2012 eller senare
* Windows PowerShell 5,1 eller senare ([Hämta WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 eller senare
* Två kärnor
* 4 GB RAM-minne
* Port 443 (utgående)

### <a name="network-configuration"></a>Nätverkskonfiguration

Information om hur du får fler nätverks krav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Server onboarding för hantering med Automation DSC

Information om onboarding-servrar för hantering med DSC finns i [onboarding Machines for Management by Azure Automation DSC](automation-dsc-onboarding.md).

Om du aktiverar [uppdateringshantering-lösningen](../operations-management-suite/oms-solution-update-management.md)konfigureras en Windows-dator som är ansluten till din Log Analytics-arbetsyta automatiskt som en hybrid Runbook Worker för att stödja Runbooks som ingår i den här lösningen. Den har dock inte registrerats med några Hybrid Worker grupper som redan har definierats i ditt Automation-konto.

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Lägga till datorn i en Hybrid Runbook Worker grupp

Datorn kan läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto så att den stöder Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg på mål datorn för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen.

### <a name="1-download-the-powershell-script"></a>1. Hämta PowerShell-skriptet

Hämta skriptet New-OnPremiseHybridWorker. ps1 från [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direkt från datorn som kör hybrid Runbook Worker-rollen eller från en annan dator i din miljö. Kopiera skriptet till arbetaren. Skriptet New-OnPremiseHybridWorker. ps1 kräver följande parametrar under körningen:

* *AAResourceGroupName* (obligatorisk): namnet på den resurs grupp som är kopplad till ditt Automation-konto.
* *OMSResourceGroupName* (valfritt): namnet på resurs gruppen för Log Analytics arbets ytan. Om den här resurs gruppen inte anges används *AAResourceGroupName* .
* *SubscriptionID* (obligatoriskt): ID för Azure-prenumerationen som ditt Automation-konto finns i.
* *TenantID* (valfritt): identifieraren för den klient organisation som är kopplad till ditt Automation-konto.
* *WorkspaceName* (valfritt): namnet på den Log Analytics arbets ytan. Om du inte har en Log Analytics arbets yta, skapar skriptet och konfigurerar ett.
* *AutomationAccountName* (obligatorisk): namnet på ditt Automation-konto.
* *HybridGroupName* (obligatorisk): namnet på en hybrid Runbook Worker grupp som du anger som mål för de Runbooks som har stöd för det här scenariot.
* *Autentiseringsuppgift* (valfritt): de autentiseringsuppgifter som ska användas när du loggar in i Azure-miljön.
  
> [!NOTE]
> När du aktiverar lösningar stöds endast vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Öppna kommando rads gränssnittet för Windows PowerShell

Öppna **Windows PowerShell** från **Start** skärmen i administratörs läge.

### <a name="3-run-the-powershell-script"></a>3. kör PowerShell-skriptet

Från kommando rads gränssnittet för PowerShell bläddrar du till den mapp som innehåller skriptet som du laddade ned. Ändra värdena för parametrarna *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*och *-WorkspaceName*. Kör sedan skriptet.

Du uppmanas att autentisera med Azure när du har kört skriptet. Du måste logga in med ett konto som är medlem i rollen prenumerations administratörer och som är medadministratör för prenumerationen.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. Installera NuGet

Du uppmanas att godkänna att installera NuGet och att autentisera med dina Azure-autentiseringsuppgifter. Om du inte har den senaste versionen av NuGet kan du hämta den från [tillgängliga NuGet-distributions versioner](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. kontrol lera distributionen

När skriptet är klart visar sidan **hybrid Worker grupper** den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen i listan på sidan **hybrid Worker grupper** och sedan välja panelen **hybrid personal** . På sidan **hybrid Worker** visas varje medlem i gruppen i listan.

## <a name="manual-deployment"></a>Manuell distribution

Utför de två första stegen en gång för din Automation-miljö på mål datorn. Utför sedan de återstående stegen för varje arbets dator.

### <a name="1-create-a-log-analytics-workspace"></a>1. skapa en Log Analytics arbets yta

Om du inte redan har en Log Analytics arbets yta måste du först läsa igenom [rikt linjerna för Azure Monitor loggs design](../azure-monitor/platform/design-logs-deployment.md) innan du skapar en arbets yta.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Lägg till Automation-lösningen i arbets ytan Log Analytics

Automation-lösningen lägger till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du lägger till lösningen i Log Analytics-arbetsytan pushas automatiskt Worker-komponenter till den agent dator som du ska installera i nästa steg.

Kör följande PowerShell-cmdlet för att lägga till **Automation** -lösningen i din arbets yta.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Installera Log Analytics agent för Windows

Log Analytics-agenten för Windows ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

Installera agenten på datorn genom att följa anvisningarna på [Anslut Windows-datorer till Azure Monitor loggar](../log-analytics/log-analytics-windows-agent.md). Du kan upprepa den här processen för flera datorer för att lägga till flera arbetare i din miljö.

När agenten har anslutit till Log Analytics arbets ytan efter några minuter kan du köra följande fråga för att kontrol lera att den skickar pulsslags data till arbets ytan:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

I Sök resultaten bör du se pulsslags poster för datorn, vilket indikerar att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslags post till den tilldelade arbets ytan. Du kan kontrol lera att agenten har laddat ned Automation-lösningen korrekt när den har en mapp med namnet AzureAutomationFiles i C:\Program Files\Microsoft Monitoring Agent\Agent. Om du vill bekräfta versionen av Hybrid Runbook Worker bläddrar du till C:\Program\Microsoft Monitoring Agent\Agent\AzureAutomation\ och noterar undermappen \\*version* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera Runbook-miljön och Anslut till Azure Automation

När du konfigurerar en agent att rapportera till en Log Analytics arbets yta, push-överför lösningen i HybridRegistration PowerShell-modulen, som innehåller cmdleten **Add-HybridRunbookWorker** . Använd den här cmdleten för att installera Runbook-miljön på datorn och registrera den med Azure Automation.

Öppna en PowerShell-session i administratörs läge och kör följande kommandon för att importera modulen.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Kör sedan cmdleten **Add-HybridRunbookWorker** med följande syntax.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan hämta den information som krävs för denna cmdlet från sidan "Hantera nycklar" i Azure Portal. Öppna den här sidan genom att välja alternativet **nycklar** på sidan **Inställningar** i ditt Automation-konto.

![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* För parametern *GroupName* använder du namnet på Hybrid Runbook Workers gruppen. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.
* För parametern *Endpoint* använder du **URL** -posten på sidan Hantera nycklar.
* För parametern *token* använder du posten **primär åtkomst nyckel** på sidan Hantera nycklar.

Om du vill ha detaljerad information om installationen använder du växeln *-verbose* med **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda alla aktiviteter och cmdletar som definierats i de moduler som är installerade i din Azure Automation-miljö. Dessa moduler distribueras inte automatiskt till lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen. Den här modulen installeras som standard och ger åtkomst till cmdletar för alla Azure-tjänster och-aktiviteter för Azure Automation.

Eftersom det primära syftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, behöver du förmodligen installera modulerna som stöder dessa resurser, i synnerhet PowerShellGet-modulen. Information om hur du installerar Windows PowerShell-moduler finns i [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Moduler som är installerade måste finnas på en plats som refereras av PSModulePath-miljövariabeln så att hybrid Worker kan importera dem automatiskt. Mer information finns [i installera moduler i PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner för hur du tar bort hybrid Runbook Worker finns i [ta bort Azure Automation hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Information om hur du felsöker dina hybrid Runbook Worker finns i [Felsöka Windows hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)
* Ytterligare anvisningar om hur du felsöker problem med Uppdateringshantering finns i [uppdateringshantering: fel sökning](troubleshoot/update-management.md).
