---
title: Konfigurera pre- och efterskript på distributionen update management i Azure
description: I den här artikeln beskrivs hur du konfigurerar och hanterar förskript och efterskript för uppdateringsdistributioner.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417788"
---
# <a name="manage-pre-and-post-scripts"></a>Hantera för- och efterskript

Med förskript och efterskript kan du köra PowerShell-runbooks i ditt Azure Automation-konto före (föruppgift) och efter (efter uppgift) en uppdateringsdistribution. Pre- och postskript körs i Azure-kontexten, inte lokalt. Förskript körs i början av uppdateringsdistributionen. Postskript körs i slutet av distributionen och efter alla omstarter som har konfigurerats.

## <a name="runbook-requirements"></a>Runbook-krav

För att en runbook ska kunna användas som pre- eller efterskript måste runbooken importeras till ditt Automation-konto och publiceras. Mer information om den här processen finns i [Publicera en runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Använda ett förskript eller ett postskript

Om du vill använda ett förskript eller ett postskript i en uppdateringsdistribution börjar du med att skapa en uppdateringsdistribution. Välj **Förskript + Postskript**. Den här åtgärden öppnar sidan **Välj förskript + postskript.**

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Markera det skript du vill använda. I det här exemplet använder vi runbooken **UpdateManagement-TurnOnVms.** När du väljer runbook öppnas sidan **Konfigurera skript.** Välj **Förskript och**välj sedan **OK**.

Upprepa den här processen för skriptet **UpdateManagement-TurnOffVms.** Men när du väljer **skripttyp**väljer du **Post-Script**.

I avsnittet **Markerade objekt** visas nu båda skripten. Den ena är ett förskript och det andra är ett postskript:

![Markerade objekt](./media/pre-post-scripts/selected-items.png)

Slutför konfigurationen av uppdateringsdistributionen.

När uppdateringsdistributionen är klar kan du gå till **Uppdatera distributioner** för att visa resultaten. Som du kan se anges statusen för förskriptet och postskriptet:

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

Genom att välja uppdateringsdistributionskörningen visas ytterligare information om för- och efterskripten. En länk till skriptkällan vid körningen tillhandahålls.

![Resultat för distributionskörning](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passerande parametrar

När du konfigurerar för- och efterskript kan du skicka in parametrar precis som att schemalägga en runbook. Parametrar definieras när uppdateringsdistributionen skapas. Pre- och efterskript stöder följande typer:

* - Jag vet inte vad du ska ta dig till.
* - Jag vet inte vad du ska ta på dig.
* - Jag vet inte vad du ska gå till.
* - Jag vet inte vad du ska gå.
* [decimal]
* -Jag har inte tid med det här.
* - Jag vet inte vad du ska gå till.
* -Jag har inte tid med det här.
* - Jag vet inte vad du vill kan göra.

Om du behöver en annan objekttyp kan du casta den till en annan typ med din egen logik i runbooken.

Förutom dina vanliga runbook-parametrar tillhandahålls en annan parameter: **SoftwareUpdateConfigurationRunContext**

Den här parametern är en JSON-sträng, och om du definierar parametern i för- eller efterskriptet skickas den automatiskt in av uppdateringsdistributionen. Parametern innehåller information om uppdateringsdistributionen, som är en delmängd av information som returneras av [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

I följande tabell visas de egenskaper som finns i variabeln.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Egenskaper för SoftwareUpdateConfigurationRunContext

|Egenskap  |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på programuppdateringskonfigurationen.        |
|SoftwareUpdateConfigurationRunId     | Det unika ID:t för körningen.        |
|SoftwareUpdateConfigurationSettings     | En samling egenskaper som är relaterade till programuppdateringskonfigurationen.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De operativsystem som är avsedda för uppdateringsdistributionen.         |
|SoftwareUpdateConfigurationSettings.duration     | Den maximala varaktigheten för `PT[n]H[n]M[n]S` uppdateringsdistributionen körs enligt ISO8601. kallas även *underhållsfönstret*.          |
|SoftwareUpdateConfigurationSettings.Windows     | En samling egenskaper som är relaterade till Windows-datorer.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | En lista över KBs som är undantagna från uppdateringsdistributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Uppdatera klassificeringar som valts för uppdateringsdistributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Starta om inställningarna för uppdateringsdistributionen.        |
|azureVirtualMachines     | En lista över resourceIds för Virtuella Azure-datorer i uppdateringsdistributionen.        |
|nonAzureComputerNames|En lista över de icke-Azure-datorernas FQDN i uppdateringsdistributionen.|

Följande exempel är en JSON-sträng som skickas in i parametern **SoftwareUpdateConfigurationRunContext:**

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

Ett fullständigt exempel med alla egenskaper finns på: [Hämta programuppdateringskonfiguration efter namn](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Objektet `SoftwareUpdateConfigurationRunContext` kan innehålla dubblettposter för datorer. Detta kan leda till att pre- och efterskript körs flera gånger på samma dator. För att komma runt `Sort-Object -Unique` det här beteendet använder du för att välja endast unika VM-namn i skriptet.


## <a name="stopping-a-deployment"></a>Stoppa en distribution

Om du vill stoppa en distribution baserat på ett förskript måste du [skapa](automation-runbook-execution.md#throw) ett undantag. Om du inte gör det körs distributionen och efterskriptet fortfarande. Följande kodavsnitt visar hur du genererar ett undantag.

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

Exempel för för- och efterskript finns i [Skriptcentergalleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) och [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), eller så kan du importera dem via Azure-portalen. Det gör du i ditt Automation-konto under **ProcessAutomation**och väljer **Runbooks Gallery**. Använd **Uppdateringshantering** för filtret.

![Gallerilista](./media/pre-post-scripts/runbook-gallery.png)

Du kan också söka efter dem med deras skriptnamn, som visas i följande lista:

* Uppdateringshantering - Aktivera virtuella datorer
* Uppdateringshantering - Inaktivera virtuella datorer
* Uppdateringshantering - Kör skript lokalt
* Uppdateringshantering - Mall för skript före/efter
* Uppdateringshantering - Kör skript med kommandot Kör

> [!IMPORTANT]
> När du har importerat runbooks måste du publicera dem innan de kan användas. Det gör du genom att leta reda på runbooken i ditt Automation-konto, välja **Redigera**och välj sedan **Publicera**.

Exemplen baseras alla på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa en egen runbook som du kan använda med för- och efterskript. Den nödvändiga logiken för att `SoftwareUpdateConfigurationRunContext` autentisera med Azure och hantera parametern ingår.

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

## <a name="interacting-with-machines"></a>Interagera med maskiner

Före- och efteruppgifter körs som en runbook i ditt Automation-konto och inte direkt på datorerna i distributionen. Före- och efteruppgifter körs också i Azure-kontexten och har inte åtkomst till datorer som inte är Azure.Pre and post-tasks also run in the Azure context and don't have access to non-Azure machines. Följande avsnitt visar hur du kan interagera med datorerna direkt, oavsett om de är virtuella Azure-datorer eller icke-Azure-datorer.

### <a name="interacting-with-azure-machines"></a>Interagera med Azure-datorer

Före- och efteruppgifter körs som runbooks och körs inte internt på dina virtuella Azure-datorer i distributionen. Om du vill interagera med virtuella Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* En runbook som du vill köra

Om du vill interagera med Azure-datorer bör du använda cmdleten [Invoke-AzureMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) för att interagera med dina virtuella Azure-datorer. Ett exempel på hur du gör detta finns i runbookexempelet [Uppdateringshantering – kör skript med kommandot Kör](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interagera med datorer som inte är Azure

Före- och efteruppgifter körs i Azure-kontexten och har inte åtkomst till datorer som inte är Azure.Pre and post-tasks run in the Azure context and don't have access to non-Azure machines. Om du vill interagera med datorerna som inte är Azure måste du ha följande objekt:

* Ett Kör som-konto
* Hybrid Runbook Worker installerad på datorn
* En runbook som du vill köra lokalt
* En överordnad runbook

Om du vill interagera med datorer som inte är Azure körs en överordnad runbook i Azure-kontexten. Den här runbooken anropar en underordnad runbook med [Cmdleten Start-AzureRmAutomationRunbook.](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Du måste `-RunOn` ange parametern och ange namnet på Hybrid Runbook Worker för att skriptet ska kunna köras på. Mer information finns i runbookexempelet [Uppdateringshantering – kör skript lokalt](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Avbryt korrigeringsdistribution

Om förskriptet returnerar ett fel kanske du vill avbryta distributionen. För att göra det måste du [kasta](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i skriptet för någon logik som skulle utgöra ett misslyckande.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Kända problem

* Du kan inte skicka en boolesk, objekt eller matriser till parametrar när du använder pre- och postskript. Om du gör det misslyckas runbooken. En fullständig lista över typer som stöds finns i [Skicka parametrar](#passing-parameters).

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande självstudiekurs om du vill lära dig hur du hanterar uppdateringar för virtuella Windows-datorer:

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

