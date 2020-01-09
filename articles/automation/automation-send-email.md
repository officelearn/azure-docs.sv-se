---
title: Skicka ett e-postmeddelande från en Azure Automation Runbook
description: Lär dig hur du använder SendGrid för att skicka ett e-postmeddelande inifrån en Runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 6acb68b7bbaa54db2e4143a42e43aede2caed35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420697"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Självstudie: Skicka ett e-postmeddelande från en Azure Automation Runbook

Du kan skicka ett e-postmeddelande från en Runbook med [SendGrid](https://sendgrid.com/solutions) med hjälp av PowerShell. I den här kursen får du lära dig hur du skapar en återanvändbar Runbook som skickar ett e-postmeddelande med hjälp av en API-nyckel som lagras i [Azure-valv](/azure/key-vault/).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
>
> * Skapa ett Azure-valv
> * Lagra din SendGrid API-nyckel i nyckel valvet
> * Skapa en Runbook som hämtar din API-nyckel och skickar ett e-postmeddelande

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* Azure-prenumeration: om du inte har någon än kan du [aktivera din MSDN-prenumerations förmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Skapa ett SendGrid-konto](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-konto](automation-offering-get-started.md) med **AZ** -moduler och [Kör som-anslutning](automation-create-runas-account.md)för att lagra och köra runbooken.

## <a name="create-an-azure-keyvault"></a>Skapa ett Azure-valv

Du kan skapa ett Azure-nyckel valv med hjälp av följande PowerShell-skript. Ersätt variabel värden med värden som är speciella för din miljö. Använd den inbäddade Azure Cloud Shell via knappen <kbd>prova</kbd> , som finns i det övre högra hörnet av kod blocket. Du kan också kopiera och köra koden lokalt om du har installerat [Azure PowerShell-modulen](/powershell/azure/install-az-ps) på den lokala datorn.

> [!NOTE]
> Hämta din API-nyckel genom att använda stegen i [hitta din SENDGRID API-nyckel](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

För andra sätt att skapa ett Azure-valv och lagra en hemlighet, se [snabb starter](/azure/key-vault/)för nyckel valv.

## <a name="import-required-modules-to-your-automation-account"></a>Importera obligatoriska moduler till ditt Automation-konto

För att du ska kunna använda Azure-valv i en Runbook behöver ditt Automation-konto följande moduler:

* [AZ. Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [AZ.](https://www.powershellgallery.com/packages/Az.KeyVault)nyckel-valv.

Klicka på <kbd>distribuera för att Azure Automation</kbd> på fliken Azure Automation under installations alternativ. Den här åtgärden öppnar Azure Portal. På sidan Importera väljer du ditt Automation-konto och klickar på <kbd>OK</kbd>.

Ytterligare metoder för att lägga till nödvändiga moduler finns i [importera moduler](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Skapa en Runbook för att skicka ett e-postmeddelande

När du har skapat ett nyckel valv och sparat din SendGrid API-nyckel är det dags att skapa en Runbook som hämtar API-nyckeln och skickar ett e-postmeddelande.

Denna Runbook använder [Kör som-kontot](automation-create-runas-account.md) i AzureRunAsConnection för att autentisera med Azure för att hämta hemligheten från Azure-.

Använd det här exemplet för att skapa en Runbook med namnet **send-GridMailMessage**. Du kan ändra PowerShell-skriptet och återanvända det för olika scenarier.

1. Gå till ditt Azure Automation-konto.
2. Under **process automatisering**väljer du **Runbooks**.
3. Längst upp i listan över Runbooks väljer du **+ skapa en Runbook**.
4. På sidan **Lägg till Runbook** anger du **send-GridMailMessage** som Runbook-namn. För Runbook-typen väljer du **PowerShell**. Välj sedan **Create** (Skapa).
   ![skapa Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbooken skapas och sidan **Redigera PowerShell-runbook** öppnas.
   ![redigera Runbook-](./media/automation-send-email/automation-send-email-edit.png)
6. Kopiera följande PowerShell-exempel till sidan **Redigera** . Se till att `$VaultName` är det namn du angav när du skapade ditt nyckel valv.

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

7. Välj **publicera** för att spara och publicera runbooken.

För att kontrol lera att runbooken körs korrekt kan du följa stegen under [testa en Runbook](manage-runbooks.md#test-a-runbook) eller [starta en Runbook](start-runbooks.md).
Om du inte först ser ditt test-e-postmeddelande kontrollerar du mapparna för **skräp post** och **skräp** post.

## <a name="clean-up"></a>Rensa

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

Ta bort nyckel valvet med cmdleten [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* Information om problem med att skapa eller starta din Runbook finns i [Felsöka fel med Runbooks](./troubleshoot/runbooks.md).
* Information om hur du uppdaterar moduler i ditt Automation-konto finns i [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md)].
* Om du vill övervaka Runbook-körningen, se [vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).
* Information om hur du utlöser en Runbook med en avisering finns i [använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md).
