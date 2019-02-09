---
title: Konfigurera WinRM-åtkomst för en Azure-dator | Microsoft Docs
description: Konfigurera WinRM-åtkomst för användning med en Azure virtuell dator som skapats i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 22a522fcde2b79d89e6084cdcfcbf64e4e5bd5ce
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977974"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Hur du konfigurerar WinRM-åtkomst för virtuella datorer i Azure Resource Manager

Här är de steg som du behöver utföra för att konfigurera en virtuell dator med WinRM-anslutningen

1. Skapa en Key Vault-lösning
2. Skapa ett självsignerat certifikat
3. Ladda upp ett självsignerat certifikat till Key Vault
4. Hämta URL för ditt självsignerade certifikat i Key Vault
5. Använda självsignerade certifikat-URL när du skapar en virtuell dator

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-create-a-key-vault"></a>Steg 1: Skapa en Key Vault-lösning
Du kan använda den nedan kommando för att skapa Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Steg 2: Skapa ett självsignerat certifikat
Du kan skapa ett självsignerat certifikat med det här PowerShell-skript

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Steg 3: Ladda upp ett självsignerat certifikat till Key Vault
Innan du laddar upp certifikatet i nyckelvalvet som du skapade i steg 1, måste den konverteras till ett format som kan förstå Microsoft.Compute-resursprovidern. Den nedan PowerShell skriptet kan du göra det

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Steg 4: Hämta URL för ditt självsignerade certifikat i Key Vault
Microsoft.Compute-resursprovidern måste en hemlighet i Key Vault-URL när du etablerar den virtuella datorn. På så sätt kan Microsoft.Compute-resursprovidern att hämta hemligheten och skapa motsvarande certifikat på den virtuella datorn.

> [!NOTE]
> URL: en för hemlighet måste innehålla versionen också. En exempel-URL som ser ut som nedan https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Mallar
Du kan hämta en länk till URL: en i mallen med hjälp av den under

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Du kan hämta den här URL: en med de PowerShell-kommandot nedan

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Steg 5: Använda självsignerade certifikat-URL när du skapar en virtuell dator
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
När du skapar en virtuell dator via mallar, hämtar certifikatet refereras till i den hemligheter och winRM-avsnitt enligt nedan:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Ett exempel på mall för ovanstående finns här på [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Källkoden för den här mallen finns på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Steg 6: Ansluta till den virtuella datorn
Innan du kan ansluta till den virtuella datorn måste du kontrollera att har din dator konfigurerats för fjärrhantering med WinRM. Starta PowerShell som administratör och kör den nedanstående kommando för att kontrollera att du har konfigurerat.

    Enable-PSRemoting -Force

> [!NOTE]
> Du kan behöva kontrollera att WinRM-tjänsten körs om ovanstående inte fungerar. Du kan göra det med `Get-Service WinRM`
> 
> 

När installationen är klar kan du kan ansluta till en virtuell dator med hjälp av den nedanstående kommando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
