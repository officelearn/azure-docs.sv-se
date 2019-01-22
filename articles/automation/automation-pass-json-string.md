---
title: Skicka ett JSON-objekt till en Azure Automation-runbook
description: Hur du skickar parametrar till en runbook som ett JSON-objekt
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: 65f3bfcebdde50352d5e9e2748036d4522b2a991
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424912"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Skicka ett JSON-objekt till en Azure Automation-runbook

Det kan vara praktiskt att lagra data som du vill skicka till en runbook i en JSON-fil.
Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en runbook.
Detta gör måste du konvertera JSON till en sträng och sedan konvertera strängen till ett PowerShell-objekt innan innehållet i runbooken.

I det här exemplet ska vi skapa ett PowerShell-skript som anropar [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) att starta en PowerShell-runbook, skicka innehållet i JSON till runbook.
PowerShell-runbook startar en Azure-dator, hämtar parametrarna för den virtuella datorn från JSON som skickades in.

## <a name="prerequisites"></a>Förutsättningar
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte har ett konto kan du [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.
* Azure Powershell installerad på en lokal dator. Se [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.1.0) information om hur du hämtar Azure PowerShell.

## <a name="create-the-json-file"></a>Skapa JSON-fil

Skriv följande test i en textfil och spara den som `test.json` någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Skapa runbook

Skapa en ny PowerShell-runbook med namnet ”Test-Json” i Azure Automation.
Läs hur du skapar en ny PowerShell-runbook i [min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).

Om du vill acceptera JSON-data, ta runbook ett objekt som indataparameter.

Runbook kan sedan använda egenskaperna som definierats i JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Spara och publicera denna runbook i Automation-kontot.

## <a name="call-the-runbook-from-powershell"></a>Anropa en runbook från PowerShell

Nu kan du anropa runbooken från din lokala dator med hjälp av Azure PowerShell.
Kör följande PowerShell-kommandon:

1. Logga in på Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Du uppmanas att ange dina autentiseringsuppgifter för Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

1. Hämta innehållet i JSON-filen och konvertera den till en sträng:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` är sökvägen där du sparade JSON-filen.
1. Konvertera sträng innehållet i `$json` till ett PowerShell-objekt:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Skapa en hash-tabell för parametrarna för `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Observera att du anger värdet för `Parameters` till PowerShell-objekt som innehåller värdena från JSON-fil. 
1. Starta runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Runbook använder värdena från JSON-filen för att starta en virtuell dator.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du redigerar PowerShell och PowerShell Workflow-runbooks med en textredigeraren finns [redigera runbooks med text i Azure Automation](automation-edit-textual-runbook.md) 
* Läs mer om att skapa och importera runbooks i [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)



