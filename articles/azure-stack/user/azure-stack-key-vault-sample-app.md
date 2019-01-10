---
title: Tillåta program att hämta Azure Stack Key Vault-hemligheter | Microsoft Docs
description: Använda en exempelapp för att arbeta med Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: 09a31103b2a29bc335653dd92e6b11a5f8294eda
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192477"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Ett exempelprogram som använder nycklar och hemligheter som lagras i key vault

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följ stegen i den här artikeln om du vill köra ett exempelprogram med namnet **HelloKeyVault** som hämtar nycklar och hemligheter från en nyckel valv i Azure Stack.

## <a name="prerequisites"></a>Förutsättningar

Du kan installera följande från Azure Stack [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad externa klient om du är [är anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).
* Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Skapa och få nyckelvalvet och programinställningar

Förbereda för exempelprogrammet:

* Skapa ett nyckelvalv i Azure Stack.
* Registrera ett program i Azure Active Directory (AD Azure).

Du kan använda Azure portal eller PowerShell förbereder för exempelprogrammet. Den här artikeln visar hur du skapar ett nyckelvalv och registrera ett program med hjälp av PowerShell.

>[!NOTE]
>Som standard skapar ett nytt program i PowerShell-skriptet i Active Directory. Dock kan du registrera en av dina befintliga program.

Innan du kör följande skript, ser till att ange värden för den `aadTenantName` och `applicationPassword` variabler. Om du inte anger ett värde för `applicationPassword`, det här skriptet genererar ett slumpmässigt lösenord.

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
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

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

Följande bild visar utdata från skriptet som används för att skapa key vault:

![Nyckelvalv med åtkomstnycklar](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Anteckna den **VaultUrl**, **AuthClientId**, och **AuthClientSecret** värden som returneras av föregående skript. Du kan använda dessa värden för att köra programmet HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Ladda ned och konfigurerar du exempelprogrammet

Ladda ned exemplet nyckelvalv från Azure [Key Vault-klienten exempel](https://www.microsoft.com/download/details.aspx?id=45343) sidan. Extrahera innehållet i ZIP-filen på utvecklingsdatorn. Det finns två program i mappen samples. Den här artikeln använder **HelloKeyVault**.

Att läsa in den **HelloKeyVault** exemplet:

* Bläddra till den **Microsoft.Azure.KeyVault.Samples** > **exempel** > **HelloKeyVault** mapp.
* Öppna den **HelloKeyVault** program i Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

I Visual Studio:

* Öppna filen HelloKeyVault\App.config och hitta den &lt; **appSettings** &gt; element.
* Uppdatera den **VaultUrl**, **AuthClientId**, och **AuthClientSecret** nycklar med värden som returneras av de som används för att skapa nyckelvalvet. Filen App.config har som standard en platshållare för `AuthCertThumbprint`. Ersätt platshållaren med `AuthClientSecret`.

  ![Appinställningar](media/azure-stack-key-vault-sample-app/appconfig.png)

* Bygg lösningen.

## <a name="run-the-application"></a>Köra programmet

När du kör HelloKeyVault programmet loggar in på Azure AD och använder sedan AuthClientSecret token för att autentisera till nyckelvalv i Azure Stack.

Du kan använda HelloKeyVault-exemplet för att:

* Utföra grundläggande åtgärder som att skapa, kryptera, omsluta, och ta bort med nycklar och hemligheter.
* Skicka parametrar som `encrypt` och `decrypt` till HelloKeyVault, och tillämpa de angivna ändringarna till ett nyckelvalv.

## <a name="next-steps"></a>Nästa steg

- [Distribuera en VM med ett Key Vault-lösenord](azure-stack-key-vault-deploy-vm-with-secret.md)
- [Distribuera en virtuell dator med ett Key Vault-certifikat](azure-stack-key-vault-push-secret-into-vm.md)
