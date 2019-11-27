---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som du kan använda för att köra Runbooks på Windows-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd599fcfe403d64483e6b4db869b93b26f5db754
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480813"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera angivna datorer. I den här artikeln beskrivs hur du installerar Hybrid Runbook Worker på en Windows-dator.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows-Hybrid Runbook Worker

Du kan använda två metoder för att installera och konfigurera en Windows-Hybrid Runbook Worker. Den rekommenderade metoden använder en Automation-Runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden följer en steg-för-steg-procedur för att manuellt installera och konfigurera rollen.

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som har stöd för Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC), måste du lägga till dem som DSC-noder.

Minimi kraven för en Windows-Hybrid Runbook Worker är:

* Windows Server 2012 eller senare.
* Windows PowerShell 5,1 eller senare ([Hämta WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 eller senare.
* Två kärnor.
* 4 GB RAM-minne.
* Port 443 (utgående).

Information om hur du får fler nätverks krav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

Mer information om onboarding-servrar för hantering med DSC finns i [onboarding Machines for Management by Azure Automation DSC](automation-dsc-onboarding.md).
Om du aktiverar [uppdateringshantering-lösningen](../operations-management-suite/oms-solution-update-management.md)konfigureras en Windows-dator som är ansluten till din Azure Log Analytics-arbetsyta automatiskt som en hybrid Runbook Worker för att stödja Runbooks som ingår i den här lösningen. Den har dock inte registrerats med några Hybrid Worker grupper som redan har definierats i ditt Automation-konto. 

Datorn kan läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto så att den stöder Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen:

1. Hämta skriptet New-OnPremiseHybridWorker. ps1 från [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direkt från datorn som kör hybrid Runbook Worker-rollen eller från en annan dator i din miljö. Kopiera skriptet till arbetaren.

   Skriptet New-OnPremiseHybridWorker. ps1 kräver följande parametrar under körningen:

   * *AutomationAccountName* (obligatorisk): namnet på ditt Automation-konto.
   * *AAResourceGroupName* (obligatorisk): namnet på den resurs grupp som är kopplad till ditt Automation-konto.
   * *OMSResourceGroupName* (valfritt): namnet på resurs gruppen för Log Analytics arbets ytan. Om den här resurs gruppen inte anges används *AAResourceGroupName* .
   * *HybridGroupName* (obligatorisk): namnet på en hybrid Runbook Worker grupp som du anger som mål för de Runbooks som har stöd för det här scenariot.
   * *SubscriptionID* (obligatoriskt): ID för Azure-prenumerationen som ditt Automation-konto finns i.
   * *WorkspaceName* (valfritt): namnet på den Log Analytics arbets ytan. Om du inte har en Log Analytics arbets yta, skapar skriptet och konfigurerar ett.

   > [!NOTE]
   > När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
   >
   > En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

2. Öppna **Windows PowerShell** från **Start** skärmen i administratörs läge på datorn.
3. Från PowerShell-kommandoraden, bläddra till mappen som innehåller skriptet som du laddade ned. Ändra värdena för parametrarna *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*och *-WorkspaceName*. Kör sedan skriptet.

     > [!NOTE]
     > Du uppmanas att autentisera med Azure när du har kört skriptet. Du *måste* logga in med ett konto som är medlem i rollen prenumerations administratörer och som är medadministratör för prenumerationen.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Du uppmanas att godkänna att installera NuGet och du uppmanas att autentisera dig med dina Azure-autentiseringsuppgifter.

5. När skriptet är klart visar sidan **hybrid Worker grupper** den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen i listan på sidan **hybrid Worker grupper** och sedan välja panelen **hybrid personal** . På sidan **hybrid Worker** visas varje medlem i gruppen i listan.

### <a name="manual-deployment"></a>Manuell distribution

Utför de första två stegen en gång för din Automation-miljö och upprepa sedan de återstående stegen för varje arbets dator.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. skapa en Log Analytics arbets yta

Om du inte redan har en Log Analytics arbets yta skapar du en genom att följa anvisningarna i [Hantera din arbets yta](../azure-monitor/platform/manage-access.md). Du kan använda en befintlig arbets yta om du redan har en.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Lägg till Automation-lösningen i arbets ytan Log Analytics

Lösningen för automatiserings Azure Monitor loggar lägger till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du lägger till lösningen i din arbets yta push-överför den automatiskt Worker-komponenter till den agent dator som du ska installera i nästa steg.

Kör följande PowerShell om du vill lägga till lösningen för **Automation** Azure Monitor-loggar på din arbets yta.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till Azure Monitor loggar. När du installerar agenten på din lokala dator och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

Installera agenten på den lokala datorn genom att följa anvisningarna på [Anslut Windows-datorer till Azure Monitor loggar](../log-analytics/log-analytics-windows-agent.md). Du kan upprepa den här processen för flera datorer för att lägga till flera arbetare i din miljö.

När agenten har anslutit till Azure Monitor loggar visas den på fliken **anslutna källor** på sidan logg analys **Inställningar** . Du kan kontrol lera att agenten har laddat ned Automation-lösningen korrekt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft Monitoring Agent\Agent. Om du vill bekräfta versionen av Hybrid Runbook Worker kan du bläddra till C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ och anteckna undermappen \\*version* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera Runbook-miljön och Anslut till Azure Automation

När du lägger till en agent i Azure Monitor loggar, pushar Automation-lösningen ned **HybridRegistration** PowerShell-modulen, som innehåller cmdleten **Add-HybridRunbookWorker** . Du använder den här cmdleten för att installera Runbook-miljön på datorn och registrera den med Azure Automation.

Öppna en PowerShell-session i administratörs läge och kör följande kommandon för att importera modulen:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Kör sedan cmdleten **Add-HybridRunbookWorker** med hjälp av följande syntax:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan hämta den information som krävs för den här cmdleten från sidan **Hantera nycklar** i Azure Portal. Öppna den här sidan genom att välja alternativet **nycklar** på sidan **Inställningar** i ditt Automation-konto.

![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** är namnet på Hybrid Runbook Worker gruppen. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.
* **Slut punkten** är **URL** -posten på sidan **Hantera nycklar** .
* **Token** är posten för **primär åtkomst nyckel** på sidan **Hantera nycklar** .

Om du vill ha detaljerad information om installationen använder du växeln **-verbose** med **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda alla aktiviteter och cmdletar som definierats i de moduler som är installerade i din Azure Automation-miljö. Dessa moduler distribueras inte automatiskt till lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen som installeras som standard och ger åtkomst till cmdletar för alla Azure-tjänster och-aktiviteter för Azure Automation.

Eftersom det primära syftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, behöver du förmodligen installera modulerna som stöder dessa resurser. Information om hur du installerar Windows PowerShell-moduler finns i [installera moduler](/powershell/scripting/developer/windows-powershell). 

Moduler som är installerade måste finnas på en plats som refereras av **PSModulePath** -miljövariabeln så att hybrid Worker kan importera dem automatiskt. Mer information finns i [ändra installations Sök vägen för PSModulePath](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner för hur du tar bort hybrid Runbook Worker finns i [ta bort Azure Automation hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Information om hur du felsöker dina hybrid Runbook Worker finns i [Felsöka Windows hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)
* Ytterligare anvisningar om hur du felsöker problem med Uppdateringshantering finns i [uppdateringshantering: fel sökning](troubleshoot/update-management.md).
