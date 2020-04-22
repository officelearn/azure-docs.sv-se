---
title: Hantera förskript och efterskript i distributionen av uppdateringshantering i Azure
description: I den här artikeln beskrivs hur du konfigurerar och hanterar förskript och efterskript för uppdateringsdistributioner.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679978"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Hantera förskript och efterskript

Förskript och efterskript är runbooks som ska köras i ditt Azure Automation-konto före (föruppgift) och efter (efter uppgift) en uppdateringsdistribution. Förskript och postskript körs i Azure-kontexten, inte lokalt. Förskript körs i början av uppdateringsdistributionen. Postskript körs i slutet av distributionen och efter alla omstarter som har konfigurerats.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Krav för förskript och efterskriptskript

För att en runbook ska kunna användas som ett förskript eller ett postskript måste du importera den till ditt Automation-konto och [publicera runbooken](manage-runbooks.md#publishing-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametrar för förskript och efterskriptskript

När du konfigurerar förskript och postskript kan du skicka in parametrar precis som att schemalägga en runbook. Parametrar definieras när uppdateringsdistributionen skapas. Preskript och postskript stöder följande typer:

* - Jag vet inte vad du ska ta dig till.
* - Jag vet inte vad du ska ta på dig.
* - Jag vet inte vad du ska gå till.
* - Jag vet inte vad du ska gå.
* [decimal]
* -Jag har inte tid med det här.
* - Jag vet inte vad du ska gå till.
* -Jag har inte tid med det här.
* - Jag vet inte vad du vill kan göra.

Parametrar för runbook före skript och efter skript stöder inte booleska typer, objekt- eller matristyper. Dessa värden gör att runbooks misslyckas. 

Om du behöver en annan objekttyp kan du casta den till en annan typ med din egen logik i runbooken.

Förutom standardkörningsparametrarna `SoftwareUpdateConfigurationRunContext` anges parametern (typ JSON-sträng). Om du definierar parametern i din runbook för skript eller efter skriptet skickas den automatiskt in av uppdateringsdistributionen. Parametern innehåller information om uppdateringsdistributionen, som är en delmängd av information som returneras av [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Avsnitt nedan definierar associerade egenskaper.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Egenskaper för SoftwareUpdateConfigurationRunContext

|Egenskap  |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på programuppdateringskonfigurationen.        |
|SoftwareUpdateConfigurationRunId     | Det unika ID:t för körningen.        |
|SoftwareUpdateConfigurationSettings     | En samling egenskaper som är relaterade till programuppdateringskonfigurationen.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De operativsystem som är avsedda för uppdateringsdistributionen.         |
|SoftwareUpdateConfigurationSettings.duration     | Den maximala varaktigheten för `PT[n]H[n]M[n]S` uppdateringsdistributionen körs enligt ISO8601. kallas även underhållsfönstret.          |
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
> Objektet `SoftwareUpdateConfigurationRunContext` kan innehålla dubblettposter för datorer. Detta kan orsaka att förskript och postskript körs flera gånger på samma dator. För att komma runt `Sort-Object -Unique` det här beteendet använder du för att välja endast unika VM-namn.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Använda ett förskript eller efterskript i en distribution

Om du vill använda ett förskript eller ett postskript i en uppdateringsdistribution börjar du med att skapa en uppdateringsdistribution. Välj **Förskript + Postskript**. Den här åtgärden öppnar sidan **Välj förskript + postskript.**

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Markera det skript du vill använda. I det här exemplet använder vi runbooken **UpdateManagement-TurnOnVms.** När du väljer runbook öppnas sidan **Konfigurera skript.** Välj **Förskript och**välj sedan **OK**.

Upprepa den här processen för skriptet **UpdateManagement-TurnOffVms.** Men när du väljer **skripttyp**väljer du **Post-Script**.

I avsnittet **Markerade objekt** visas nu båda skripten. Den ena är ett förskript och det andra är ett postskript:

![Markerade objekt](./media/pre-post-scripts/selected-items.png)

Slutför konfigurationen av uppdateringsdistributionen.

När uppdateringsdistributionen är klar kan du gå till **Uppdatera distributioner** för att visa resultaten. Som du kan se anges statusen för förskriptet och postskriptet:

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

Genom att välja uppdateringsdistributionskörningen visas ytterligare information om förskript och postskript. En länk till skriptkällan vid körningen tillhandahålls.

![Resultat för distributionskörning](./media/pre-post-scripts/deployment-run.png)

i ditt manus.

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



## <a name="interacting-with-machines"></a>Interagera med maskiner

Förskript och efteruppgifter körs som runbooks i ditt Automation-konto och inte direkt på datorerna i distributionen. Föruppgifter och efteruppgifter körs också i Azure-kontexten och har inte åtkomst till datorer som inte är Azure.Pre-tasks and post-tasks also run in the Azure context and don't have access to non-Azure machines. Följande avsnitt visar hur du kan interagera med datorerna direkt, oavsett om de är virtuella Azure-datorer eller icke-Azure-datorer.

### <a name="interact-with-azure-machines"></a>Interagera med Azure-datorer

Föruppgifter och efteruppgifter körs som runbooks och körs inte internt på dina virtuella Azure-datorer i distributionen. Om du vill interagera med virtuella Azure-datorer måste du ha följande objekt:

* Ett Kör som-konto
* En runbook som du vill köra

Om du vill interagera med Azure-datorer bör du använda cmdleten [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) för att interagera med dina virtuella Azure-datorer. Ett exempel på hur du gör detta finns i runbookexempelet [Uppdateringshantering – kör skript med kommandot Kör](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interagera med datorer som inte är Azure

Föruppgifter och efteruppgifter körs i Azure-kontexten och har inte åtkomst till datorer som inte är Azure.Pre-tasks and post-tasks run in the Azure context and don't have access to non-Azure machines. Om du vill interagera med datorerna som inte är Azure måste du ha följande objekt:

* Ett Kör som-konto
* Hybrid Runbook Worker installerad på datorn
* En runbook som du vill köra lokalt
* En överordnad runbook

Om du vill interagera med datorer som inte är Azure körs en överordnad runbook i Azure-kontexten. Den här runbooken anropar en underordnad runbook med cmdleten [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Du måste `RunOn` ange parametern och ange namnet på Hybrid Runbook Worker för att skriptet ska kunna köras på. Se runbook-exemplet [Update Management – kör skript lokalt](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Avbryta distributionen av korrigeringsfiler

Om förskriptet returnerar ett fel kanske du vill avbryta distributionen. För att göra det måste du [kasta](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i skriptet för någon logik som skulle utgöra ett misslyckande.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Exempel

Exempel på förskript och postskript finns i [Skriptcentergalleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) och [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), eller så kan du importera dem via Azure-portalen. Det gör du i ditt Automation-konto under **ProcessAutomation**och väljer **Runbooks Gallery**. Använd **Uppdateringshantering** för filtret.

![Gallerilista](./media/pre-post-scripts/runbook-gallery.png)

Du kan också söka efter dem med deras skriptnamn, som visas i följande lista:

* Uppdateringshantering - Aktivera virtuella datorer
* Uppdateringshantering - Inaktivera virtuella datorer
* Uppdateringshantering - Kör skript lokalt
* Uppdateringshantering - Mall för skript före/efter
* Uppdateringshantering - Kör skript med kommandot Kör

> [!IMPORTANT]
> När du har importerat runbooks måste du publicera dem innan de kan användas. Det gör du genom att leta reda på runbooken i ditt Automation-konto, välja **Redigera**och välj sedan **Publicera**.

Exemplen baseras alla på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa en egen runbook som ska användas med förskript och postskript. Den nödvändiga logiken för att `SoftwareUpdateConfigurationRunContext` autentisera med Azure och hantera parametern ingår.

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

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> För icke-grafiska `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande självstudiekurs om du vill lära dig hur du hanterar uppdateringar för virtuella Windows-datorer:

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)