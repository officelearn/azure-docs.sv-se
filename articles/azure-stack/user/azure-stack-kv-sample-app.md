---
title: Tillåter att program att hämta Azure Stack Key Vault hemligheter | Microsoft Docs
description: Använda en exempelapp för att arbeta med Azure-stacken Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Ett exempelprogram som använder nycklar och hemligheter som lagras i ett nyckelvalv

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Följ stegen i den här artikeln för att köra exempelprogrammet (HelloKeyVault) som hämtar nycklar och hemligheter från nyckelvalvet i Azure-stacken.

## <a name="prerequisites"></a>Förutsättningar

Du kan installera följande krav från Azure-stacken [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Skapa och få nyckelvalvet och programinställningar

Förbereda för exempelprogrammet:

* Skapa en nyckelvalvet i Azure-stacken.
* Registrera ett program i Azure Active Directory (AD Azure).

Du kan använda Azure-portalen eller PowerShell för att förbereda för exempelprogrammet. Den här artikeln visar hur du skapar en nyckelvalvet och registrera ett program med hjälp av PowerShell.

>[!NOTE]
>Som standard skapar ett nytt program i PowerShell-skriptet i Active Directory. Dock kan du registrera en av dina befintliga program.

 Innan du kör följande skript, kontrollera att du anger värden för den `aadTenantName` och `applicationPassword` variabler. Om du inte anger ett värde för `applicationPassword`, genererar ett slumpmässigt lösenord för det här skriptet.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

```

Nästa skärmdump visar utdata från skriptet som används för att skapa nyckelvalvet:

![Nyckeln valvet med snabbtangenter](media/azure-stack-kv-sample-app/settingsoutput.png)

Anteckna den **VaultUrl**, **AuthClientId**, och **AuthClientSecret** värden som returneras av föregående skript. Du kan använda dessa värden för att köra programmet HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Hämta och konfigurera exempelprogrammet

Hämta exemplet som nyckelvalv från Azure [Key Vault klienten prover](https://www.microsoft.com/en-us/download/details.aspx?id=45343) sidan. Extrahera innehållet i ZIP-filen på utvecklingsdatorn. Det finns två program i mappen samples, den här artikeln använder HelloKeyVault.

Att läsa in HelloKeyVault exemplet:

* Bläddra till den **Microsoft.Azure.KeyVault.Samples** > **exempel** > **HelloKeyVault** mapp.
* Öppna programmet HelloKeyVault i Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

I Visual Studio:

* Öppna filen HelloKeyVault\App.config och hitta Bläddra till den &lt; **appSettings** &gt; element.
* Uppdatering av **VaultUrl**, **AuthClientId**, och **AuthClientSecret** nycklar med de värden som används för att skapa nyckelvalvet. (Filen App.config har som standard en platshållare för *AuthCertThumbprint*. Ersätta platshållaren med *AuthClientSecret*.)

  ![Appinställningar](media/azure-stack-kv-sample-app/appconfig.png)

* Bygg lösningen.

## <a name="run-the-application"></a>Köra programmet

När du kör HelloKeyVault programmet loggar in på Azure AD och använder sedan AuthClientSecret token för att autentisera till nyckelvalvet i Azure-stacken.

Du kan använda HelloKeyVault-exempel för att:

* Utföra grundläggande åtgärder som att skapa, kryptera, omsluter, och ta bort på nycklar och hemligheter.
* Skicka parametrar som *kryptera* och *dekryptera* till HelloKeyVault, och använda de angivna ändringarna på ett nyckelvalv.

## <a name="next-steps"></a>Nästa steg

[Distribuera en VM med ett Key Vault-lösenord](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuera en virtuell dator med en Key Vault-certifikat](azure-stack-kv-push-secret-into-vm.md)
