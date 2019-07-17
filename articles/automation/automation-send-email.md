---
title: Skicka ett e-postmeddelande från en Azure Automation-runbook
description: Lär dig hur du använder SendGrid för att skicka ett e-postmeddelande från inifrån en runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234985"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Självstudier: Skicka ett e-postmeddelande från en Azure Automation-runbook

Du kan skicka ett e-postmeddelande från en runbook med [SendGrid](https://sendgrid.com/solutions) med hjälp av PowerShell. Den här självstudien visar hur du skapar en återanvändbara runbook som skickar ett e-postmeddelande med en API-nyckel som lagras i [Azure KeyVault](/azure/key-vault/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en Azure KeyVault
> * Store din SendGrid API-nyckel i KeyVault
> * Skapa en runbook som hämtar din API nyckel och skickar ett e-postmeddelande

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att genomföra kursen:

* Azure-prenumeration: Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Skapa ett SendGrid-konto](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-konto](automation-offering-get-started.md) med **Az** moduler och [kör som-anslutning](automation-create-runas-account.md), för att lagra och köra runbook.

## <a name="create-an-azure-keyvault"></a>Skapa en Azure KeyVault

Du kan skapa en Azure KeyVault med hjälp av följande PowerShell-skript. Ersätt variabelvärdena med värden som är specifika för din miljö. Använd inbäddad Azure Cloud Shell via den <kbd>prova</kbd> knapp, finns i det övre högra hörnet om kodblocket. Du kan också kopiera och kör kod lokalt om du har den [Azure PowerShell-modulen](/powershell/azure/install-az-ps) installerad på den lokala datorn.

> [!NOTE]
> Använd stegen i för att hämta din API-nyckel, [hitta din SendGrid API-nyckel](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Andra sätt att skapa en Azure KeyVault och lagra en hemlighet, se [KeyVault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importera moduler som krävs till ditt Automation-konto

Om du vill använda Azure KeyVault inifrån en runbook måste Automation-kontot följande moduler:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Klicka på <kbd>distribuera till Azure Automation</kbd> på fliken Azure Automation under installationsalternativ. Den här åtgärden öppnar Azure Portal. På sidan Importera väljer du ditt Automation-konto och klicka på <kbd>OK</kbd>.

Ytterligare metoder för att lägga till modulerna som krävs finns i [importera moduler](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Skapa runbook för att skicka ett e-postmeddelande

När du har skapat en KeyVault och lagras din SendGrid API-nyckel, är det dags att skapa en runbook som hämtar API-nyckeln och skicka ett e-postmeddelande.

Denna runbook använder AzureRunAsConnection [kör som-konto](automation-create-runas-account.md) att autentisera med Azure för att hämta hemligheten från Azure KeyVault.

Använd det här exemplet för att skapa en runbook med namnet **skicka GridMailMessage**. Du kan ändra PowerShell-skriptet och återanvända den för olika scenarier.

1. Gå till ditt Azure Automation-konto.
2. Under **Processautomatisering**väljer **Runbooks**.
3. Överst i listan med runbooks, Välj **+ skapa en runbook**.
4. På den **Lägg till Runbook** anger **skicka GridMailMessage** för runbook-namn. Typ av runbook, Välj **PowerShell**. Välj sedan **Create** (Skapa).
   ![Skapa Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbooken skapas och sidan **Redigera PowerShell-runbook** öppnas.
   ![Redigera Runbooken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopiera följande PowerShell-exempel i den **redigera** sidan. Se till att den `$VaultName` är det namn du angav när du skapade din KeyVault.

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

7. Välj **publicera** att spara och publicera en runbook.

Kontrollera att runbook körs rätt kan du följa stegen under [testar en runbook](manage-runbooks.md#test-a-runbook) eller [startar en runbook](start-runbooks.md).
Om du inte först ser din test-e-post, kontrollera din **skräppost** och **skräppost** mappar.

## <a name="clean-up"></a>Rensa

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

Ta bort nyckelvalvet med hjälp av den [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) cmdlet.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* Problem med att skapa eller starta din runbook, se [felsöka fel med runbooks](./troubleshoot/runbooks.md).
* Om du vill uppdatera moduler i ditt Automation-konto, se [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md)].
* För att övervaka runbook-körning, se [vidarebefordrar jobbstatus och jobbströmmar från Automation till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).
* För att utlösa en runbook med en avisering, se [använda en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md).
