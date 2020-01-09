---
title: Konfigurera för-och efter-skript på din Uppdateringshantering distribution i Azure
description: Den här artikeln beskriver hur du konfigurerar och hanterar för-skript och post-skript för uppdaterings distributioner.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417788"
---
# <a name="manage-pre-and-post-scripts"></a>Hantera för-och efter-skript

För skript och post-skript kan du köra PowerShell-Runbooks i ditt Azure Automation konto före (före aktivitet) och efter (efter aktivitet) en uppdaterings distribution. För-och efter-skript körs i Azure-kontexten, inte lokalt. För skript körs i början av uppdaterings distributionen. Efter att skripten körs i slutet av distributionen och efter omstarter som har kon figurer ATS.

## <a name="runbook-requirements"></a>Runbook-krav

För att en Runbook ska kunna användas som ett före eller efter skript, måste runbooken importeras till ditt Automation-konto och publiceras. Mer information om den här processen finns i [publicera en Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Använda ett för skript eller efter skript

Om du vill använda ett för skript eller efter skript i en uppdaterings distribution börjar du med att skapa en uppdaterings distribution. Välj **för skript + efter skript**. Den här åtgärden öppnar sidan **Välj före skript + efter skript** .

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Välj det skript som du vill använda. I det här exemplet använder vi **UpdateManagement-TurnOnVms-** runbooken. När du väljer Runbook öppnas sidan **Konfigurera skript** . Välj **för skript**och välj sedan **OK**.

Upprepa processen för **UpdateManagement-TurnOffVms-** skriptet. Men när du väljer **skript typen**väljer du **efter skript**.

Avsnittet **valda objekt** visar nu båda skripten som valts. En är ett för skript och det andra är ett efter skript:

![Markerade objekt](./media/pre-post-scripts/selected-items.png)

Slutför konfigurationen av uppdaterings distributionen.

När distributionen av uppdateringen är klar kan du gå till **uppdaterings distributioner** för att visa resultaten. Som du kan se anges statusen för för-skriptet och efter skriptet:

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

Genom att välja körningen av uppdaterings distributionen visas ytterligare information om för-och efter-skripten. En länk till skript källan vid körningen tillhandahålls.

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

Förutom dina standard parametrar för Runbook anges en annan parameter: **SoftwareUpdateConfigurationRunContext**

Den här parametern är en JSON-sträng och om du definierar parametern i ditt för-eller-skript skickas den automatiskt av uppdaterings distributionen. Parametern innehåller information om uppdaterings distributionen, som är en del av informationen som returneras av [SoftwareUpdateconfigurations-API: et](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

I följande tabell visas de egenskaper som anges i variabeln.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext properties

|Egenskap  |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på program uppdaterings konfigurationen.        |
|SoftwareUpdateConfigurationRunId     | Unikt ID för körningen.        |
|SoftwareUpdateConfigurationSettings     | En samling egenskaper relaterade till program uppdaterings konfigurationen.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De operativ system som är avsedda för uppdaterings distributionen.         |
|SoftwareUpdateConfigurationSettings.duration     | Maximal varaktighet för uppdaterings distributionen som körs som `PT[n]H[n]M[n]S` per ISO8601; kallas även *underhålls period*.          |
|SoftwareUpdateConfigurationSettings.Windows     | En samling egenskaper som är relaterade till Windows-datorer.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | En lista över KB som är undantagna från uppdaterings distributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Uppdaterings klassificeringar som valts för uppdaterings distributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Starta om inställningarna för uppdaterings distributionen.        |
|azureVirtualMachines     | En lista över resourceIds för virtuella Azure-datorer i uppdaterings distributionen.        |
|nonAzureComputerNames|En lista över FQDN för icke-Azure-datorer i uppdaterings distributionen.|

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

Du hittar ett fullständigt exempel med alla egenskaper på: [Hämta program uppdaterings konfiguration efter namn](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext`-objektet kan innehålla dubbla poster för datorer. Detta kan orsaka att för-och efter-skript körs flera gånger på samma dator. Undvik det här problemet genom att använda `Sort-Object -Unique` för att välja enbart unika VM-namn i skriptet.


## <a name="stopping-a-deployment"></a>Stoppa en distribution

Om du vill stoppa en distribution som baseras på ett för skript, måste du [utlösa](automation-runbook-execution.md#throw) ett undantag. Om du inte gör det kommer distribution och post script fortfarande att köras. Följande kodfragment visar hur du genererar ett undantag.

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

Exempel för för-och efter-skript finns i [Script Center-galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) och i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), eller så kan du importera dem via Azure Portal. Om du vill göra det går du till ditt Automation-konto, under **process automatisering**, väljer **Runbooks-Galleri**. Använd **uppdateringshantering** för filtret.

![Galleri lista](./media/pre-post-scripts/runbook-gallery.png)

Du kan också söka efter dem med deras skript namn, som du ser i följande lista:

* Uppdateringshantering – Aktivera virtuella datorer
* Uppdateringshantering – Stäng av virtuella datorer
* Uppdateringshantering-kör skript lokalt
* Uppdateringshantering-mall för pre/post-skript
* Uppdateringshantering-kör skript med kommandot kör

> [!IMPORTANT]
> När du har importerat Runbooks måste du publicera dem innan de kan användas. Det gör du genom att leta upp Runbook i ditt Automation-konto, välja **Redigera**och sedan **publicera**.

Exemplen är alla baserade på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa en egen Runbook som ska användas med för-och efter-skript. Den nödvändiga logiken för autentisering med Azure och hantering av `SoftwareUpdateConfigurationRunContext`-parametern ingår.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
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

För-och efter-aktiviteter körs som en Runbook i ditt Automation-konto och inte direkt på datorerna i distributionen. Pre och post-tasks körs också i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. I följande avsnitt visas hur du kan interagera med datorerna direkt, oavsett om de är virtuella Azure-datorer eller datorer som inte är Azure-datorer.

### <a name="interacting-with-azure-machines"></a>Interagera med Azure-datorer

Pre och post-tasks körs som Runbooks och körs inte internt på dina virtuella Azure-datorer i distributionen. Om du vill interagera med dina virtuella Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* En Runbook som du vill köra

Om du vill interagera med Azure-datorer ska du använda cmdleten [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) för att interagera med dina virtuella Azure-datorer. Ett exempel på hur du gör detta finns i Runbook-exemplet [uppdateringshantering – kör skript med kommandot kör](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interagera med datorer som inte är Azure-datorer

För-och efter-aktiviteter körs i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. Om du vill interagera med datorer som inte är Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* Hybrid Runbook Worker installerat på datorn
* En Runbook som du vill köra lokalt
* En överordnad Runbook

För att interagera med datorer som inte är Azure-datorer körs en överordnad Runbook i Azure-kontexten. Denna Runbook anropar en underordnad Runbook med cmdleten [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Du måste ange parametern `-RunOn` och ange namnet på den Hybrid Runbook Worker som skriptet ska köras på. Mer information finns i Runbook-exemplet [uppdateringshantering – kör skript lokalt](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Avbryt korrigerings distribution

Om ditt för skript returnerar ett fel kanske du vill avbryta distributionen. Om du vill göra det måste du [utlösa](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i skriptet för vilken logik som skulle utgöra ett fel.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Kända problem

* Det går inte att skicka booleska objekt eller matriser till parametrar när du använder för-och-skript. Om du gör det Miss lyckas runbooken. En fullständig lista över typer som stöds finns i [Skicka parametrar](#passing-parameters).

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande självstudie och lär dig hur du hanterar uppdateringar för virtuella Windows-datorer:

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

