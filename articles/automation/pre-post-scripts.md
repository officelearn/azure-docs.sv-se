---
title: Konfigurera före och efter skript på din distribution med hantering av uppdateringar i Azure (förhandsversion)
description: Den här artikeln beskriver hur du konfigurerar och hanterar före och efter-skript för uppdateringsdistributioner
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76cd877380090ccad8b2f7b7dbe79957e0eab5bb
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056685"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Hantera skript före och efter (förhandsversion)

Skript före och efter att du kör PowerShell-runbooks i ditt Automation-konto innan (före uppgift) och efter distributionen för en uppdatering (efter uppgift). Skript före och efter körs i Azure-kontext och inte lokalt. Före skript köras i början av uppdateringsdistributionen. Skicka skript som körs i slutet av distributionen och efter eventuella omstarter som har konfigurerats.

## <a name="runbook-requirements"></a>Krav för Runbook

För en runbook som ska användas som ett skript före eller efter måste runbook importeras till ditt automation-konto och publiceras. Mer information om den här processen finns [publicera en runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Med ett före/efter-skript

Använda en i förväg och eller publicera skript i en Uppdateringsdistribution kan du börja med att skapa en Uppdateringsdistribution. Välj **förskript + Post skript (förhandsversion)**. Den här åtgärden öppnar den **Välj förskript och efterskript** sidan.  

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Väljer du skriptet som du vill använda i det här exemplet, du har använt den **UpdateManagement TurnOnVms** runbook. När du väljer att runbook den **Konfigurera skript** öppnas, ange värden för parametrarna och välj **Förskript**. Klicka på **OK** när du är klar.

![Konfigurera skript](./media/pre-post-scripts/configure-script.png)

Upprepa denna process för den **UpdateManagement TurnOffVms** skript. Men när du väljer den **Skripttyp**, Välj **Efterskript**.

Den **valda objekt** avsnittet visar nu både dina skript som valts och är på en förskript och den andra är en efterskript.

![Markerade objekt](./media/pre-post-scripts/selected-items.png)

Konfigurera din distribution.

När distributionen av uppdateringen är klar går du till **uppdateringsdistributioner** vill visa resultatet. Som du kan se status för förskript och efterskript tillhandahålls.

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

Genom att klicka i uppdateringsdistributionen kör, får du ytterligare information före och efter-skript. Det finns en länk till skriptkälla vid tidpunkten för körningen.

![Distribution Körningsresultat](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Skicka parametrar

När du konfigurerar före och efter skript, du kan skicka parametrar precis som schemaläggning av en runbook. Parametrar definieras vid tidpunkten för uppdatering distributionen skapas. Skript före och efter stöder följande typer:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [enkel]
* [dubbla]
* [DateTime]
* [string]

Om du behöver en annan objekttyp kan skicka du den till en annan typ med egen logik i runbooken.

Utöver standard runbook-parametrarna erbjuds en extra parameter. Den här parametern är **SoftwareUpdateConfigurationRunContext**. Den här parametern är en JSON-sträng, och om du definierar parametern i skriptet före eller efter det skickas automatiskt av uppdateringsdistributionen. Parametern innehåller information om distributionen av uppdateringen, vilket är en delmängd av information som returneras av den [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) i följande tabell visar de egenskaper som anges i variabeln:

## <a name="stopping-a-deployment"></a>Stoppa en distribution

Om du vill stoppa en distribution baserad på ett Pre-skript som du måste [throw](automation-runbook-execution.md#throw) ett undantag. Om du inte utlöser ett undantag, körs distribution och inlägg skriptet fortfarande. Den [exempel-runbooken](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) i galleriet visar hur du kan göra detta. Följande är ett kodfragment från samma runbook.

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

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext properties

|Egenskap   |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på Uppdateringskonfigurationen för programmet        |
|SoftwareUpdateConfigurationRunId     | Unikt id för körningen.        |
|SoftwareUpdateConfigurationSettings     | En uppsättning egenskaper som rör Uppdateringskonfigurationen för programmet         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De operativsystem som mål för distributionen         |
|SoftwareUpdateConfigurationSettings.duration     | Maximal varaktighet för uppdateringsdistributionen kör som- `PT[n]H[n]M[n]S` enligt ISO8601, kallas även ”underhållsfönstret”          |
|SoftwareUpdateConfigurationSettings.Windows     | En uppsättning egenskaper som rör Windows-datorer         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | En lista med KB-artiklar som är exkluderade från distributionen av uppdateringen        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Uppdatera klassificeringar som valts för distributionen av uppdateringen        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Starta om inställningar för distributionen        |
|azureVirtualMachines     | En lista över resourceIds för virtuella Azure-datorer i uppdateringsdistributionen        |
|nonAzureComputerNames|En lista med icke-Azure-datorer FQDN i uppdateringsdistributionen|

I följande exempel är en JSON-sträng som skickas till den **SoftwareUpdateConfigurationRunContext** parameter:

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

Ett fullständigt exempel med alla egenskaper finns på: [Uppdatera konfigurationer - hämta efter namn](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Den `SoftwareUpdateConfigurationRunContext` objekt kan innehålla dubbla poster för datorer. Detta kan orsaka före och efter skript körs flera gånger på samma dator. Att lösa problemet, Använd `Sort-Object -Unique` att välja endast unika namn på virtuella datorer i ditt skript.

## <a name="samples"></a>Exempel

Exempel för skript före och efter finns i den [Galleri för Skriptcenter](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), eller importeras via Azure portal. Att importera dem via portalen, i ditt Automation-konto under **Processautomatisering**väljer **Runbookgalleri**. Använd **uppdateringshantering** för filtret.

![Gallerilista](./media/pre-post-scripts/runbook-gallery.png)

Eller du kan söka efter dem av deras skriptets namn som visas i listan nedan:

* Uppdateringshantering - aktivera virtuella datorer
* Uppdateringshantering - Stäng av virtuella datorer
* Uppdatera hantering – kör skript lokalt
* Uppdatera Management - mall för före/efter-skript
* Uppdatera hantering – kör skriptet med Körningskommando

> [!IMPORTANT]
> När du har importerat runbooks måste du **publicera** dem innan de kan användas. För att göra som hitta runbook i Automation-kontot, Välj **redigera**, och klicka på **publicera**.

Exemplen är alla baserade på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa egna runbooks ska användas med skript före och efter. Den nödvändiga logiken för autentisering med Azure och hantering av den `SoftwareUpdateConfigurationRunContext` parametern ingår.

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

## <a name="interacting-with-non-azure-machines"></a>Interagera med icke-Azure-datorer

Före och efter aktiviteter körs i Azure-kontext och har inte åtkomst till icke-Azure-datorer. För att interagera med icke-Azure-datorer, måste du ha följande objekt:

* Ett kör som-konto
* Hybrid Runbook Worker installerad på datorn
* En runbook som du vill köra lokalt
* Överordnad runbook

För att interagera med icke-Azure-datorer, körs en överordnad runbook i Azure-kontext. Denna runbook anropar en underordnad runbook med den [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet. Du måste ange den `-RunOn` parametern och ange namnet på den Hybrid Runbook Worker för att skriptet ska köras på.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="abort-patch-deployment"></a>Avbryt patch-distribution

Om pre skriptet returnerar ett fel, kan du vill avbryta din distribution. Om du vill göra detta måste du [throw](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i ditt skript för någon logik som skulle kunna utgöra ett fel.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Kända problem

* Du kan inte skicka objekt eller matriser till parametrar när du använder skript före och efter. Runbook misslyckas.

## <a name="next-steps"></a>Nästa steg

Vill du fortsätta till självstudien om hur du hanterar uppdateringar för din Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

