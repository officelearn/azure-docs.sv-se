---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som du kan använda för att köra runbooks på Windows-datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e449e6e457c4fa568b5a4de5823014b4dcea82d0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064073"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer. Den här artikeln beskriver hur du installerar Runbook Worker-hybriden på en Windows-dator.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows Hybrid Runbook Worker

Du kan använda två metoder för att installera och konfigurera en Windows-Hybrid Runbook Worker. Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen.

> [!NOTE]
> Du måste lägga till dem som DSC-noder för att hantera konfigurationen för dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC ().

Minimikraven för en Windows-Hybrid Runbook Worker är:

* Windows Server 2012 eller senare.
* Windows PowerShell 4.0 eller senare ([hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Vi rekommenderar att Windows PowerShell 5.1 ([hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) för ökad tillförlitlighet.
* .NET framework 4.6.2 eller senare.
* Två kärnor.
* 4 GB RAM-minne.
* Port 443 (utgående).

För att få mer nätverkskrav för Hybrid Runbook Worker, se [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

Mer information om onboarding-servrar för hantering med DSC finns [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
Om du aktiverar den [uppdatering hanteringslösning](../operations-management-suite/oms-solution-update-management.md), en Windows-dator som är ansluten till Azure logganalys-arbetsytan konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Men är inte det registrerat några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. 

Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

När du har distribuerat en runbook worker kan granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.

### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen:

1. Hämta ny OnPremiseHybridWorker.ps1 skriptet från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) direkt från den dator som kör Hybrid Runbook Worker-rollen eller från en annan dator i din miljö. Kopiera skriptet till arbetaren.

   Ny OnPremiseHybridWorker.ps1 skriptet kräver följande parametrar under körning:

   * *AutomationAccountName* (obligatoriskt): namnet på ditt Automation-konto.
   * *AAResourceGroupName* (obligatoriskt): namnet på resursgruppen som är kopplad till ditt Automation-konto.
   * *OMSResourceGroupName* (valfritt): namnet på resursgruppen för Operations Management Suite-arbetsyta. Om resursgruppen inte anges *AAResourceGroupName* används.
   * *HybridGroupName* (obligatoriskt): namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som har stöd för det här scenariot.
   * *SubscriptionID* (obligatoriskt): Azure prenumerations-ID som ditt Automation-konto.
   * *WorkspaceName* (valfritt): namnet för det logganalys-arbetsytan. Om du inte har en logganalys-arbetsytan skriptet skapar och konfigurerar en.

     > [!NOTE]
     > Endast Automation regioner som stöds för integrering med logganalys är för närvarande **Australien sydost**, **östra USA 2**, **Sydostasien**, och  **Västra Europa**. Om ditt Automation-konto inte är i någon av de regionerna, skriptet skapar en logganalys-arbetsyta men varnar dig om att det går inte att länka dem tillsammans.

1. På datorn, öppna **Windows PowerShell** från den **starta** skärm i administratörsläge.
1. Bläddra till den mapp som innehåller det skript som du hämtade från kommandoraden shell PowerShell. Ändra värdena för parametrarna *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, och *- WorkspaceName*. Sedan kör skriptet.

     > [!NOTE]
     > Du uppmanas att autentisera med Azure när du kör skriptet. Du *måste* logga in med ett konto som är medlem i rollen administratörer för prenumeration och medadministratör för prenumerationen.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Du uppmanas att komma överens om att installera NuGet och du uppmanas att autentisera med dina autentiseringsuppgifter för Azure.

1. När skriptet har slutförts kan den **Hybrid Worker grupper** sidan visas den nya gruppen och antalet medlemmar. Om det är en befintlig grupp stegvis antalet medlemmar. Du kan välja grupp från listan på den **Hybrid Worker grupper** och välja den **hybrider** panelen. På den **hybrider** kan du se varje medlem i gruppen i listan.

### <a name="manual-deployment"></a>Manuell distribution

Utför de två första stegen en gång för Automation-miljö och sedan upprepa stegen för varje worker-dator.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Skapa en logganalys-arbetsyta

Om du inte redan har en logganalys-arbetsyta, skapa en med hjälp av anvisningarna i [hantera din arbetsyta](../log-analytics/log-analytics-manage-access.md). Du kan använda en befintlig arbetsyta om du redan har en.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Lägg till Automation-lösningen logganalys-arbetsytan

Lösningar lägger till funktioner i Log Analytics. Automation-lösningen lägger till funktionalitet för Azure Automation, inklusive stöd för Runbook Worker-Hybrid. När du lägger till lösningen till din arbetsyta skickar automatiskt worker komponenter till agentdatorn som du installerar i nästa steg.

Att lägga till den **Automation** lösning till logganalys-arbetsytan, följer du anvisningarna [att lägga till en lösning med lösningar galleriet](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till logganalys. När du installerar agenten på den lokala datorn och ansluta till arbetsytan, hämtas automatiskt de komponenter som krävs för Runbook Worker-Hybrid.

Om du vill installera agenten på den lokala datorn, följer du anvisningarna [ansluta Windows-datorer till logganalys](../log-analytics/log-analytics-windows-agent.md). Du kan upprepa processen för flera datorer att lägga till flera personer i din miljö.

När agenten har anslutits till logganalys, det visas på den **anslutna källor** för Log Analytics **inställningar** sidan. Du kan kontrollera att agenten har hämtat Automation-lösningen på rätt sätt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft övervakning Agent\Agent. Om du vill bekräfta version av Hybrid Runbook Worker som du kan bläddra till C:\Program Files\Microsoft övervakning Agent\Agent\AzureAutomation\ och anteckna den \\ *version* undermappen.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera runbook-miljön och ansluta till Azure Automation

När du lägger till en agent till logganalys Automation-lösningen push-meddelanden den **HybridRegistration** PowerShell-modulen som innehåller den **Add-HybridRunbookWorker** cmdlet. Du kan använda denna cmdlet för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Öppna ett PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Kör sedan den **Add-HybridRunbookWorker** cmdlet med hjälp av följande syntax:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan hämta den information som krävs för denna cmdlet från den **hantera nycklar** sida i Azure-portalen. Öppna den här sidan genom att välja den **nycklar** alternativet från den **inställningar** sida i ditt Automation-konto.

![”Hantera nycklar” sida](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Gruppnamn** är namnet på Hybrid Runbook Worker-gruppen. Om den här gruppen finns redan i Automation-konto, läggs den aktuella datorn till den. Om den här gruppen inte finns, läggs den.
* **Slutpunkten** är den **URL** transaktionen på den **hantera nycklar** sidan.
* **Token** är den **primära ÅTKOMSTNYCKELN** transaktionen på den **hantera nycklar** sidan.

Använd för att få detaljerad information om installationen av **-Verbose** växel med **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlets som definierats i de moduler som är installerade i Azure Automation-miljö. Dessa moduler distribueras inte automatiskt till lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen som installeras som standard och ger åtkomst till cmdlets för alla Azure-tjänster och aktiviteter för Azure Automation.

Eftersom Huvudsyftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, måste du förmodligen installera moduler som stöder dessa resurser. Information om hur du installerar Windows PowerShell-moduler finns [installerar moduler](http://msdn.microsoft.com/library/dd878350.aspx). 

Moduler som är installerade måste finnas på en plats som refererar till den **PSModulePath** miljövariabeln så att worker-hybriden automatiskt kan importera dem. Mer information finns i [ändra installationssökvägen PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Felsöka

Information om hur du felsöker Hybrid Runbook Worker-arbeten finns [felsökning Windows Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)

Ytterligare anvisningar om hur du felsöker problem med hantering av uppdateringar finns [uppdateringshantering: felsökning](troubleshoot/update-management.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö finns [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).