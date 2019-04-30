---
title: Azure Automation Windows Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som du kan använda för att köra runbooks på Windows-baserade datorer i ditt lokala datacenter eller moln.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0cc00b4f2075ba77490d310080b9968bedb8dc1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304958"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows Hybrid Runbook Worker

Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera angivna datorer. Den här artikeln beskriver hur du installerar den Hybrid Runbook Worker på en Windows-dator.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows Hybrid Runbook Worker

Du kan använda två metoder för att installera och konfigurera en Windows Hybrid Runbook Worker. Den rekommendera metoden att använda en Automation-runbook att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden följer stegvisa anvisningar för att manuellt installera och konfigurera rollen.

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som har stöd för Hybrid Runbook Worker-roll med Desired State Configuration (DSC), som du behöver lägga till dem som DSC-noder.

Minimikraven för en Windows Hybrid Runbook Worker är:

* Windows Server 2012 eller senare.
* Windows PowerShell 5.1 eller senare ([hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET framework 4.6.2 eller senare.
* Två kärnor.
* 4 GB RAM.
* Port 443 (utgående).

För att få mer nätverkskrav för Hybrid Runbook Worker kan se [konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

Mer information om onboarding-servrar för hantering med DSC finns [konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
Om du aktiverar den [uppdateringshanteringslösningen](../operations-management-suite/oms-solution-update-management.md), konfigureras en Windows-dator som är ansluten till Azure Log Analytics-arbetsytan automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Men är inte det registrerad med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. 

Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto för Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

När du har distribuerat en runbook worker kan granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra moln.

### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installation och konfiguration av Windows Hybrid Worker-roll:

1. Ladda ned skriptet New-OnPremiseHybridWorker.ps1 från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direkt från den dator som kör Hybrid Runbook Worker-rollen eller från en annan dator i din miljö. Kopiera skriptet till arbetaren.

   New-OnPremiseHybridWorker.ps1 skriptet kräver följande parametrar under körning:

   * *AutomationAccountName* (obligatoriskt): Namnet på ditt Automation-konto.
   * *AAResourceGroupName* (obligatoriskt): Namnet på resursgruppen som är associerat med ditt Automation-konto.
   * *OMSResourceGroupName* (valfritt): Namnet på resursgruppen för Log Analytics-arbetsytan. Om den här resursgruppen inte har angetts, *AAResourceGroupName* används.
   * *HybridGroupName* (obligatoriskt): Namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som har stöd för det här scenariot.
   * *SubscriptionID* (obligatoriskt): ID för Azure-prenumeration som Automation-kontot.
   * *WorkspaceName* (valfritt): Namnet på Log Analytics-arbetsytan. Om du inte har en Log Analytics-arbetsyta, skriptet skapar och konfigurerar en.

     > [!NOTE]
     > De enda Automation-regioner som stöds för integrering med Azure Monitor-loggar finns för närvarande **Australien, sydöstra**, **östra USA 2**, **Sydostasien**, och **Västeuropa**. Om ditt Automation-konto inte är i någon av dessa regioner skriptet skapar en Log Analytics-arbetsyta men varnar dig om att det går inte att länka dem tillsammans.

2. På din dator, öppna **Windows PowerShell** från den **starta** skärm i administratörsläge.
3. Bläddra till den mapp som innehåller skriptet som du laddade ned från PowerShell-kommandoradsgränssnittet. Ändra värdena för parametrarna *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, och *- WorkspaceName*. Kör sedan skriptet.

     > [!NOTE]
     > Du uppmanas att autentisera med Azure när du har kört skriptet. Du *måste* logga in med ett konto som är medlem i rollen Prenumerationsadministratörer och medadministratör för prenumerationen.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Du uppmanas att godkänna att installera NuGet och du uppmanas att autentisera med dina autentiseringsuppgifter för Azure.

5. När skriptet har slutförts, den **Hybrid Worker-grupper** sidan visas den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökas antalet medlemmar. Du kan välja grupp i listan på den **Hybrid Worker-grupper** och välj den **Hybrid Worker-arbeten** panelen. På den **Hybrid Worker-arbeten** du se att varje medlem i gruppen i listan.

### <a name="manual-deployment"></a>Manuell distribution

Utför de två första stegen en gång för din automatiseringsmiljö och upprepa återstående steg för varje worker-dator.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Skapa en Log Analytics-arbetsyta

Om du inte redan har en Log Analytics-arbetsyta kan du skapa ett med hjälp av anvisningarna i [hantera din arbetsyta](../azure-monitor/platform/manage-access.md). Du kan använda en befintlig arbetsyta om du redan har ett.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Lägga till Automation-lösningen i Log Analytics-arbetsytan

Automation Azure Monitor logs-lösningen lägger till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du lägger till lösningen till din arbetsyta skickar automatiskt worker komponenter till agentdatorn som du ska installera i nästa steg.

Att lägga till den **Automation** Azure Monitor loggar lösning på arbetsytan och kör följande PowerShell.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till Azure Monitor-loggar. När du installerar agenten på den lokala datorn och ansluter den till arbetsytan, hämtas automatiskt de komponenter som krävs för Hybrid Runbook Worker.

Om du vill installera agenten på den lokala datorn, följer du anvisningarna [ansluta Windows-datorer till Azure Monitor-loggar](../log-analytics/log-analytics-windows-agent.md). Du kan upprepa processen för flera datorer att lägga till flera arbetare i din miljö.

När agenten har lyckats ansluta till Azure Monitor-loggar, visas den på den **anslutna källor** fliken logganalys **inställningar** sidan. Du kan kontrollera att agenten har hämtat Automation-lösningen korrekt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft Monitoring Agent\Agent. Om du vill bekräfta versionen av Hybrid Runbook Worker, som du kan bläddra till C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ och anteckna den \\ *version* undermappen.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera runbook-miljön och Anslut till Azure Automation

När du lägger till en agent till Azure Monitor-loggar, Automation-lösningen skickar den **HybridRegistration** PowerShell-modulen som innehåller den **Add-HybridRunbookWorker** cmdlet. Du kan använda denna cmdlet för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Öppna en PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Kör sedan den **Add-HybridRunbookWorker** cmdlet med hjälp av följande syntax:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan få den information som krävs för denna cmdlet från den **hantera nycklar** sidan på Azure portal. Öppna den här sidan genom att välja den **nycklar** alternativet från den **inställningar** sida i ditt Automation-konto.

![Sidan ”Hantera nycklar”](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Gruppnamn** är namnet på den Hybrid Runbook Worker-gruppen. Om den här gruppen finns redan i Automation-kontot, läggs den aktuella datorn till den. Om den här gruppen inte finns, läggs den.
* **Slutpunkten** är den **URL** posten på den **hantera nycklar** sidan.
* **Token** är den **primära ÅTKOMSTNYCKEL** posten på den **hantera nycklar** sidan.

Använd för att få detaljerad information om installationen av den **-utförlig** växel med **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlet: ar som definierats i de moduler som är installerade i din miljö för Azure Automation. Dessa moduler distribueras inte automatiskt till den lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen som installeras som standard och tillhandahåller åtkomst till cmdletar för alla Azure-tjänster och aktiviteter för Azure Automation.

Eftersom det huvudsakliga syftet med funktionen Hybrid Runbook Worker är att hantera lokala resurser, måste du troligen att installera modulerna som har stöd för dessa resurser. Information om hur du installerar Windows PowerShell-moduler finns i [installerar moduler](/powershell/developer/windows-powershell). 

Moduler som är installerade måste finnas på en plats som refererar till den **PSModulePath** miljövariabeln så att hybrid worker automatiskt kan importera dem. Mer information finns i [ändra installationssökvägen PSModulePath](/powershell/developer/windows-powershell).

## <a name="next-steps"></a>Nästa steg

* Läs hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra moln i [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Instruktioner om hur du tar bort Hybrid Runbook Worker finns [ta bort Azure Automation Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Läs hur du felsöker Hybrid Runbook Worker-arbeten i [felsökning Windows Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)
* Ytterligare anvisningar om hur du felsöker problem med hantering av uppdateringar finns i [uppdateringshantering: felsökning](troubleshoot/update-management.md).
