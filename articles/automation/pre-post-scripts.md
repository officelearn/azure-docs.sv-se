---
title: Konfigurera för-och efter-skript på din Uppdateringshantering distribution i Azure
description: Den här artikeln beskriver hur du konfigurerar och hanterar för-och efter-skript för uppdaterings distributioner
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84cd5db812d995f1160a02917eac5857ee076c7f
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374441"
---
# <a name="manage-pre-and-post-scripts"></a>Hantera för-och efter-skript

Med för-och efter-skript kan du köra PowerShell-Runbooks i ditt Automation-konto innan (före aktivitet) och efter (efter aktivitet) en uppdaterings distribution. För-och efter-skript körs i Azure-kontexten och inte lokalt. För skript körs i början av uppdaterings distributionen. Publicering av skript körs i slutet av distributionen och efter eventuella omstarter som har kon figurer ATS.

## <a name="runbook-requirements"></a>Runbook-krav

För att en Runbook ska kunna användas som ett för-eller-skript måste Runbook importeras till ditt Automation-konto och publiceras. Mer information om den här processen finns i [publicera en Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Använda ett före/efter-skript

Börja med att skapa en uppdaterings distribution om du vill använda ett för-och eller ett-skript i en uppdaterings distribution. Välj **för skript + post skript**. Den här åtgärden öppnar sidan **Välj före skript + efter skript** .

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Välj det skript som du vill använda, i det här exemplet använde du **UpdateManagement-TurnOnVms-** runbooken. När du väljer Runbook-sidan för att **Konfigurera skript** öppnas väljer du **före skript**. Klicka på **OK** när du är färdig.

Upprepa processen för **UpdateManagement-TurnOffVms-** skriptet. Men när du väljer **skript typ**väljer du **efter skript**.

Avsnittet **valda objekt** visar nu båda skripten som valts och på är ett för skript och det andra är ett post-skript.

![Valda objekt](./media/pre-post-scripts/selected-items.png)

Slutför konfigurationen av uppdaterings distributionen.

När distributionen av uppdateringen är klar kan du gå till **uppdaterings distributioner** för att visa resultaten. Som du ser visas statusen för för skript och efter skript.

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

Genom att klicka i körningen av uppdaterings distributionen får du ytterligare information om för-och efter-skript. En länk till skript källan vid körningen tillhandahålls.

![Körnings resultat för distribution](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Parametrar skickas

När du konfigurerar för-och efter-skript kan du skicka parametrar, precis som schemaläggning av en Runbook. Parametrarna definieras vid tidpunkten för skapandet av uppdaterings distributionen. För-och efter-skript stöder följande typer:

* hängande
* stor
* int
* som
* decimal
* samma
* Dubbelklicka
* DateTime
* nollängd

Om du behöver en annan objekt typ kan du omvandla den till en annan typ med din egen logik i runbooken.

Förutom dina standard parametrar för Runbook anges ytterligare en parameter. Den här parametern är **SoftwareUpdateConfigurationRunContext**. Den här parametern är en JSON-sträng och om du definierar parametern i ditt för-eller post skript skickas den automatiskt av uppdaterings distributionen. Parametern innehåller information om uppdaterings distributionen, som är en del av informationen som returneras av [SoftwareUpdateconfigurations-API: et](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) i följande tabell visas de egenskaper som anges i variabeln:


### <a name="softwareupdateconfigurationruncontext-properties"></a>Egenskaper för SoftwareUpdateConfigurationRunContext

|Egenskap  |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på program uppdaterings konfigurationen        |
|SoftwareUpdateConfigurationRunId     | Unikt ID för körningen.        |
|SoftwareUpdateConfigurationSettings     | En samling egenskaper relaterade till program uppdaterings konfigurationen         |
|SoftwareUpdateConfigurationSettings. Opera ting system     | De operativ system som är riktade för uppdaterings distributionen         |
|SoftwareUpdateConfigurationSettings. duration     | Maximal varaktighet för uppdaterings distributionen körs som `PT[n]H[n]M[n]S` enligt per ISO8601, även kallat "underhålls fönster"          |
|SoftwareUpdateConfigurationSettings. Windows     | En samling egenskaper som rör Windows-datorer         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | En lista över KB som är undantagna från uppdaterings distributionen        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | Uppdatera klassificeringar som valts för uppdaterings distributionen        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Starta om inställningarna för uppdaterings distributionen        |
|azureVirtualMachines     | En lista över resourceIds för virtuella Azure-datorer i uppdaterings distributionen        |
|nonAzureComputerNames|En lista över FQDN för icke-Azure-datorer i uppdaterings distributionen|

Följande exempel är en JSON-sträng som skickas till parametern **SoftwareUpdateConfigurationRunContext** :

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Ett fullständigt exempel med alla egenskaper finns på: [program uppdaterings konfiguration – Hämta efter namn](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Objektet `SoftwareUpdateConfigurationRunContext` kan innehålla dubbla poster för datorer. Detta kan orsaka att pre-och post-skript körs flera gånger på samma dator. Använd `Sort-Object -Unique` för att endast välja unika VM-namn i skriptet för att lösa problemet.


## <a name="stopping-a-deployment"></a>Stoppa en distribution

Om du vill stoppa en distribution som baseras på ett för skript, måste du [utlösa](automation-runbook-execution.md#throw) ett undantag. Om du inte utlöser ett undantag körs fortfarande distributions-och publicerings skriptet. Kodfragmentet nedan visar hur du genererar ett undantag.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="samples"></a>Exempel

Exempel för för-och efter-skript finns i [Script Center-galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)eller importeras via Azure Portal. För att importera dem via portalen, i ditt Automation-konto, under **process automatisering**, väljer du **Runbooks-Galleri**. Använd **uppdateringshantering** för filtret.

![Galleri lista](./media/pre-post-scripts/runbook-gallery.png)

Du kan också söka efter dem med skript namnet som visas i följande lista:

* Uppdateringshantering – Aktivera virtuella datorer
* Uppdateringshantering – Stäng av virtuella datorer
* Uppdateringshantering-kör skript lokalt
* Uppdateringshantering-mall för pre/post-skript
* Uppdateringshantering-kör skript med kommandot kör

> [!IMPORTANT]
> När du har importerat Runbooks måste du **publicera** dem innan de kan användas. Om du vill göra detta hittar du Runbook i ditt Automation-konto, väljer **Redigera**och klickar på **publicera**.

Exemplen är alla baserade på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa en egen Runbook som ska användas med för-och efter-skript. Den nödvändiga logiken för autentisering med Azure och hantering av `SoftwareUpdateConfigurationRunContext`-parametern ingår.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management Pre/Post scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Interagera med datorer

För-och efter-aktiviteter körs som en Runbook i ditt Automation-konto och inte direkt på datorerna i distributionen. Pre och post-tasks körs också i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. I följande avsnitt visas hur du kan interagera med datorerna direkt oavsett om de är en virtuell Azure-dator eller en icke-Azure-dator:

### <a name="interacting-with-azure-machines"></a>Interagera med Azure-datorer

Pre och post-tasks körs som Runbooks och körs inte internt på dina virtuella Azure-datorer i distributionen. Om du vill interagera med dina virtuella Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* En Runbook som du vill köra

Om du vill interagera med Azure-datorer ska du använda cmdleten [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) för att interagera med dina virtuella Azure-datorer. Ett exempel på hur du gör detta finns i Runbook [-exemplet uppdateringshantering-kör skript med kommandot kör](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interagera med datorer som inte är Azure-datorer

För-och efter-aktiviteter körs i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. Om du vill interagera med datorer som inte är Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* Hybrid Runbook Worker installerat på datorn
* En Runbook som du vill köra lokalt
* Överordnad Runbook

För att interagera med datorer som inte är Azure-datorer körs en överordnad Runbook i Azure-kontexten. Denna Runbook anropar en underordnad Runbook med cmdleten [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Du måste ange parametern `-RunOn` och ange namnet på den Hybrid Runbook Worker som skriptet ska köras på. Ett exempel finns i Runbook [-exemplet uppdateringshantering-kör skript lokalt](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Avbryt korrigerings distribution

Om skriptet returnerar ett fel kanske du vill avbryta distributionen. Om du vill göra det måste du [utlösa](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i skriptet för vilken logik som skulle utgöra ett fel.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Kända problem

* Det går inte att skicka booleska objekt eller matriser till parametrar när du använder före-och efter-skript. Det gick inte att utföra Runbook-flödet. En fullständig lista över typer som stöds finns i [parametrar](#passing-parameters).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudien för att lära dig hur du hanterar uppdateringar för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

