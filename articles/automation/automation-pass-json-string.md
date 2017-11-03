---
title: Skicka ett JSON-objekt till en Azure Automation-runbook | Microsoft Docs
description: Hur ska skicka parametrar till en runbook som ett JSON-objekt
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: PowerShell, runbook, json, azure automation
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: e9352ac1a346537d6214590be6dbc9db7ca0f461
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Skicka ett JSON-objekt till en Azure Automation-runbook

Det kan vara praktiskt att lagra data som du vill skicka till en runbook i en JSON-fil.
Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en runbook.
För att göra detta, måste du konvertera JSON till en sträng och sedan konvertera strängen till en PowerShell-objektet innan du skickar dess innehåll till runbook.

I det här exemplet ska vi skapa ett PowerShell-skript som anropar [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) att starta en PowerShell-runbook som överför innehållet i JSON till runbook.
PowerShell-runbook startar en Azure VM, hämtar parametrarna för den virtuella datorn från JSON som skickades.

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller <a href="/pricing/free-account/" target="_blank">[registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.
* Azure Powershell installeras på en lokal dator. Se [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) information om hur du hämtar Azure PowerShell.

## <a name="create-the-json-file"></a>Skapa JSON-fil

Skriv följande test i en textfil och spara den som `test.json` någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Skapa en runbook

Skapa en ny PowerShell-runbook med namnet ”Test-Json” i Azure Automation.
Information om hur du skapar en ny PowerShell-runbook finns [min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).

Om du vill acceptera JSON-data ta runbook ett objekt som parameter.

Runbook kan sedan använda de egenskaper som definierats i JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Spara och publicera denna runbook i Automation-kontot.

## <a name="call-the-runbook-from-powershell"></a>Anropa runbook från PowerShell

Nu kan du anropa runbook från din lokala dator med hjälp av Azure PowerShell.
Kör följande PowerShell-kommandon:

1. Logga in i Azure:
   ```powershell
   Login-AzureRmAccount
   ```
    Du uppmanas att ange dina autentiseringsuppgifter för Azure.
1. Hämta innehållet i JSON-filen och omvandla dem till en sträng:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath`är sökvägen där du sparade JSON-filen.
1. Konvertera strängen innehållet i `$json` till ett PowerShell-objekt:
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
   Observera att du anger värdet för `Parameters` till PowerShell-objektet som innehåller värdena från JSON-filen. 
1. Starta runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Runbook använder värdena från JSON-filen för att starta en virtuell dator.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du redigerar PowerShell och PowerShell-arbetsflöde runbooks med en textrepresentation editor finns [redigera textrepresentation runbooks i Azure Automation](automation-edit-textual-runbook.md) 
* Läs mer om hur du skapar och importerar runbooks i [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)


