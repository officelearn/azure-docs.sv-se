---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som gör det möjligt att köra runbooks på Windows-datorer i ditt lokala datacenter eller molnmiljö.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e63e4afb5c60f193d46e30ab884d72912a6a5054
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Så här distribuerar du en Windows-Hybrid Runbook Worker

Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer. Den här artikeln beskrivs hur du installerar Runbook Worker-hybriden på en Windows-dator.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows Hybrid Runbook Worker

Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker, finns det två metoder. Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen som krävs för att konfigurera en Windows-dator. Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen.

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC () som du behöver lägga till dem som DSC-noder.

Följande är minimikraven för en Windows-Hybrid Runbook Worker.

* Windows Server 2012 eller senare.
* Windows PowerShell 4.0 eller senare krävs ([hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Windows PowerShell 5.1 ([hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) rekommenderas för ökad tillförlitlighet.
* .NET framework 4.6.2 eller senare.
* Minst två kärnor.
* Minst 4 GB RAM.
* Port 443 (utgående)

Ytterligare nätverkskrav för Runbook Worker-hybriden finns [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

Mer information om onboarding dem för hantering med DSC, se [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
Om du aktiverar den [uppdatering hanteringslösning](../operations-management-suite/oms-solution-update-management.md), en Windows-dator som är ansluten till logganalys-arbetsytan konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Det har inte registrerats med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösning och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

När en runbook worker har distribuerats, granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.

### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen.

1. Hämta den *ny OnPremiseHybridWorker.ps1* skript från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) direkt från den dator som kör Hybrid Runbook Worker-rollen eller från en annan dator i din miljö och kopierar den till arbetaren.

   Den *ny OnPremiseHybridWorker.ps1* skriptet kräver följande parametrar under körning:

   * *AutomationAccountName* (obligatoriskt) - namnet på ditt Automation-konto.
   * *AAResourceGroupName* (obligatoriskt) - namnet på resursgruppen som är associerade med ditt Automation-konto
   * *OMSResourceGroupName* (valfritt) – namnet på resursgruppen för OMS-arbetsyta. Om inget anges används AAResourceGroupName.
   * *HybridGroupName* (obligatoriskt) - namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som stöder det här scenariot.
   * *SubscriptionID* (obligatoriskt) - det Azure-prenumerationens ID som ditt Automation-konto.
   * *WorkspaceName* (valfritt) - namnet för det logganalys-arbetsytan. Om du inte har en logganalys-arbetsytan skriptet skapar och konfigurerar en.

     > [!NOTE]
     > Det finns för närvarande endast Automation regioner som stöds för integrering med logganalys - **Australien sydost**, **östra USA 2**, **Sydostasien**, och  **Västra Europa**. Om ditt Automation-konto inte är i någon av de regionerna, skriptet skapar en logganalys-arbetsyta men den varnar dig om att det går inte att länka dem tillsammans.

1. På datorn, startar **Windows PowerShell** från den **starta** skärm i administratörsläge.
1. Från PowerShell-kommandoradsgränssnitt, navigera till mappen som innehåller skriptet som du hämtade och köra den ändra värdena för parametrarna *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, och *- WorkspaceName*.

     > [!NOTE]
     > Du uppmanas att autentisera med Azure när du kör skriptet. Du **måste** logga in med ett konto som är medlem i rollen administratörer för prenumeration och medadministratör för prenumerationen.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Du uppmanas att komma överens om att installera **NuGet** och du uppmanas att autentisera med dina autentiseringsuppgifter för Azure.

1. När skriptet har slutförts, sidan Hybrid Worker-grupper visas den nya gruppen och antal medlemmar eller om en befintlig grupp, ökar antalet medlemmar. Du kan välja grupp från listan på den **Hybrid Worker grupper** och välja den **hybrider** panelen. På den **hybrider** kan du se varje medlem i gruppen i listan.

### <a name="manual-deployment"></a>Manuell distribution

Utför de två första stegen en gång för Automation-miljö och upprepa återstående steg för varje worker-dator.

#### <a name="1-create-log-analytics-workspace"></a>1. Skapa Log Analytics-arbetsyta

Om du inte redan har en logganalys-arbetsytan, skapar du en med hjälp av anvisningarna i [hantera din arbetsyta](../log-analytics/log-analytics-manage-access.md). Du kan använda en befintlig arbetsyta om du redan har en.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Lägg till Automation-lösningen i logganalys-arbetsytan

Lösningar lägger till funktioner i Log Analytics. Automation-lösningen lägger till funktionalitet för Azure Automation, inklusive stöd för Runbook Worker-Hybrid. När du lägger till lösningen till din arbetsyta skickar den automatiskt ned worker komponenter till agentdatorn som du installerar i nästa steg.

Följ anvisningarna på [att lägga till en lösning med lösningar galleriet](../log-analytics/log-analytics-add-solutions.md) att lägga till den **Automation** lösning till logganalys-arbetsytan.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till logganalys. När du installerar agenten på den lokala datorn och ansluta till arbetsytan, hämtas automatiskt de komponenter som krävs för Runbook Worker-Hybrid.

Följ anvisningarna på [ansluta Windows-datorer till logganalys](../log-analytics/log-analytics-windows-agent.md) att installera agenten på den lokala datorn. Du kan upprepa processen för flera datorer att lägga till flera personer i din miljö.

När agenten har anslutits till logganalys, det visas på den **anslutna källor** för Log Analytics **inställningar** sidan. Du kan kontrollera att agenten har hämtat Automation-lösningen på rätt sätt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft övervakning Agent\Agent. För att bekräfta versionen av Hybrid Runbook Worker, kan du gå till C:\Program Files\Microsoft övervakning Agent\Agent\AzureAutomation\ och anteckna den \\ *version* undermappen.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera runbook-miljön och ansluta till Azure Automation

När du lägger till en agent till logganalys Automation-lösningen push-meddelanden den **HybridRegistration** PowerShell-modulen som innehåller den **Add-HybridRunbookWorker** cmdlet. Du kan använda denna cmdlet för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Öppna ett PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Kör sedan den **Add-HybridRunbookWorker** cmdlet med följande syntax:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan hämta den information som krävs för denna cmdlet från den **hantera nycklar** sida i Azure-portalen. Öppna den här sidan genom att välja den **nycklar** alternativet från den **inställningar** sida i ditt Automation-konto.

![Hybrid Runbook Worker-översikt](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Gruppnamn** är namnet på Hybrid Runbook Worker-gruppen. Om den här gruppen finns redan i automation-konto, läggs den aktuella datorn till den. Om den inte redan finns, sedan läggs den.
* **Slutpunkten** är den **URL** i den **hantera nycklar** sidan.
* **Token** är den **primära åtkomstnyckeln** i den **hantera nycklar** sidan.

Använd den **-Verbose** växel med **Add-HybridRunbookWorker** få detaljerad information om installationen.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlets som definierats i moduler som installerats i din Azure Automation-miljö. Dessa moduler distribueras automatiskt inte till lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen som installeras som standard ger tillgång till cmdlets för alla Azure-tjänster och aktiviteter för Azure Automation.

Eftersom Huvudsyftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, behöver du troligen installera moduler som stöder dessa resurser. Du kan referera till [installerar moduler](http://msdn.microsoft.com/library/dd878350.aspx) information om hur du installerar Windows PowerShell-moduler. Moduler som är installerade måste vara på en plats som refereras av PSModulePath miljövariabeln så att de importeras automatiskt av worker-hybriden. Mer information finns i [ändra installationssökvägen PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="next-steps"></a>Nästa steg

* Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)