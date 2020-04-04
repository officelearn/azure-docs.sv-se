---
title: Skicka ett e-postmeddelande från en Azure Automation-runbook
description: Läs om hur du använder SendGrid för att skicka ett e-postmeddelande från en runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 332d7e4cae3b65d794d48d208b54db3809b78a8f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632372"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Självstudiekurs: Skicka ett e-postmeddelande från en Azure Automation-runbook

Du kan skicka ett e-postmeddelande från en runbook med [SendGrid](https://sendgrid.com/solutions) med PowerShell. Den här självstudien visar hur du skapar en återanvändbar runbook som skickar ett e-postmeddelande med hjälp av en API-nyckel som lagras i [Azure KeyVault](/azure/key-vault/).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en Azure KeyVault
> * Lagra din SendGrid API-nyckel i KeyVault
> * Skapa en runbook som hämtar din API-nyckel och skickar ett e-postmeddelande

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* Azure-prenumeration: Om du inte har en ännu kan du [aktivera dina msdn-prenumerationsförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Skapa ett SendGrid-konto](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-konto](automation-offering-get-started.md) med **Az-moduler** och [Kör som-anslutning](automation-create-runas-account.md)för att lagra och köra runbooken.

## <a name="create-an-azure-keyvault"></a>Skapa en Azure KeyVault

Du kan skapa en Azure KeyVault med följande PowerShell-skript. Ersätt variabelvärdena med värden som är specifika för din miljö. Använd det inbäddade Azure Cloud Shell via <kbd>knappen Prova,</kbd> som finns längst upp till höger i kodblocket. Du kan också kopiera och köra koden lokalt om du har [Azure PowerShell-modulen](/powershell/azure/install-az-ps) installerad på din lokala dator.

> [!NOTE]
> Om du vill hämta DIN API-nyckel använder du stegen i [Hitta din SendGrid API-nyckel](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Andra sätt att skapa en Azure KeyVault och lagra en hemlighet finns i [KeyVault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importera nödvändiga moduler till ditt Automation-konto

Om du vill använda Azure KeyVault i en runbook behöver ditt Automation-konto följande moduler:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Klicka på <kbd>Distribuera till Azure Automation</kbd> på fliken Azure Automation under Installationsalternativ. Den här åtgärden öppnar Azure-portalen. På sidan Importera väljer du ditt Automation-konto och klickar på <kbd>OK</kbd>.

Mer information om hur du lägger till de moduler som krävs finns i [Importera moduler](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Skapa runbooken för att skicka ett e-postmeddelande

När du har skapat en KeyVault och lagrat din SendGrid API-nyckel är det dags att skapa runbooken som hämtar API-nyckeln och skickar ett e-postmeddelande.

Den här runbooken använder AzureRunAsConnection [Run As-kontot](automation-create-runas-account.md) för att autentisera med Azure för att hämta hemligheten från Azure KeyVault.

Använd det här exemplet om du vill skapa en runbook som heter **Send-GridMailMessage**. Du kan ändra PowerShell-skriptet och återanvända det för olika scenarier.

1. Gå till ditt Azure Automation-konto.
2. Under **Processautomatisering**väljer du **Runbooks**.
3. Högst upp i listan över runbooks väljer du **+ Skapa en runbook**.
4. På sidan **Lägg till runbook** anger du **Send-GridMailMessage** för runbook-namnet. För runbook-typen väljer du **PowerShell**. Välj sedan **Create** (Skapa).
   ![Skapa runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbooken skapas och sidan **Redigera PowerShell-runbook** öppnas.
   ![Redigera runbooken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopiera följande PowerShell-exempel till sidan **Redigera.** Kontrollera att `$VaultName` det är det namn du angav när du skapade keyvault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Välj **Publicera** om du vill spara och publicera runbooken.

Om du vill kontrollera att runbooken körs kan du följa stegen under [Testa en runbook](manage-runbooks.md#testing-a-runbook) eller [Starta en runbook](start-runbooks.md).
Om du inte först ser din test-e-post kontrollerar du **dina** **skräppostmappar.**

## <a name="clean-up"></a>Rensa

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

Ta bort nyckelvalvet med hjälp av cmdleten [Remove-AzureRMKeyVault.](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* Problem med att skapa eller starta runbooken finns i [Felsöka fel med runbooks](./troubleshoot/runbooks.md).
* Information om hur du uppdaterar moduler i ditt Automation-konto finns i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md)].
* Information om hur du övervakar körning av runbook finns i [Vidarebefordra jobbstatus och jobbströmmar från Automatisering till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).
* Information om hur du utlöser en runbook med hjälp av en avisering finns i [Använda en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md).
