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
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: c32667f240c9e825a82b1e9623c672b00ac999ed
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Exempelprogram som använder nycklar och hemligheter som lagras i ett nyckelvalv

I den här artikeln hur vi du kör ett exempelprogram (HelloKeyVault) som hämtar nycklar och hemligheter från nyckelvalvet i Azure-stacken.

## <a name="prerequisites"></a>Förutsättningar 

Kör följande förutsättningar antingen från den [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Skapa och få nyckelvalvet och programinställningar

Du bör först skapa ett nyckelvalv i Azure-stacken och registrera ett program i Azure Active Directory (AD Azure). Du kan skapa och registrera nyckelvalv med hjälp av Azure-portalen eller PowerShell. Den här artikeln visar PowerShell-sätt att göra. Som standard skapar detta PowerShell-skript ett nytt program i Active Directory. Men kan du också använda en av dina befintliga program. Se till att ange ett värde för den `aadTenantName` och `applicationPassword` variabler. Om du inte anger ett värde för den `applicationPassword` variabel, skriptet genererar ett slumpmässigt lösenord. 

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
Connect-AzureRmAccount `
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

# Create a new resource group and a key vault within that resource group.
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

Följande skärmbild visar utdata från skriptet tidigare:

![Programkonfiguration](media/azure-stack-kv-sample-app/settingsoutput.png)

Anteckna den **VaultUrl**, **AuthClientId**, och **AuthClientSecret** värden som returneras av föregående skript. Du kan använda dessa värden för att köra programmet HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Hämta och kör exempelprogrammet

Hämta exemplet som nyckelvalv från Azure [Key Vault klienten prover](https://www.microsoft.com/en-us/download/details.aspx?id=45343) sidan. Extrahera innehållet i ZIP-filen på utvecklingsdatorn. Det finns två samplingarna i mappen samples. Vi använder HellpKeyVault exemplet i den här artikeln. Bläddra till den **Microsoft.Azure.KeyVault.Samples** > **exempel** > **HelloKeyVault** mapp och öppna programmet HelloKeyVault i Visual Studio. 

Öppna filen HelloKeyVault\App.config och Ersätt värdena i den <appSettings> element med den **VaultUrl**, **AuthClientId**, och **AuthClientSecret** värden returnerade det föregående skriptet. Observera att App.config som standard innehåller en platshållare för *AuthCertThumbprint*, men använda *AuthClientSecret* i stället. När du ersätter inställningarna bygg lösningen och starta programmet.

![Appinställningar](media/azure-stack-kv-sample-app/appconfig.png)
 
Programmet loggar in på Azure AD och använder sedan den token för att autentisera till nyckelvalvet i Azure-stacken. Programmet utför åtgärder som att skapa, kryptera, omsluter och ta bort på nycklar och hemligheter i nyckelvalvet. Du kan också skicka specifika parametrar som *kryptera* och *dekryptera* till programmet, vilket säkerställer att programmet körs bara dessa åtgärder mot valvet. 


## <a name="next-steps"></a>Nästa steg
[Distribuera en VM med ett Key Vault-lösenord](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuera en virtuell dator med en Key Vault-certifikat](azure-stack-kv-push-secret-into-vm.md)



