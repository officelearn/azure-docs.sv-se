---
title: Konfigurera WinRM-åtkomst för en virtuell Azure-dator
description: Konfigurera WinRM-åtkomst för användning med en virtuell Azure-dator som skapats i Distributionsmodellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: ca52a458104b4de0f7b3ed2aa3f76109a5623c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74067316"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager

Här är de åtgärder du behöver vidta för att konfigurera en virtuell dator med WinRM-anslutning

1. Skapa en Key Vault-lösning
2. Skapa ett självsignerat certifikat
3. Ladda upp ditt självsignerade certifikat till Key Vault
4. Hämta webbadressen för ditt självsignerade certifikat i Key Vault
5. Referera till url:en för självsignerade certifikat när du skapar en virtuell dator

 

## <a name="step-1-create-a-key-vault"></a>Steg 1: Skapa ett nyckelvalv
Du kan använda kommandot nedan för att skapa Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Steg 2: Skapa ett självsignerat certifikat
Du kan skapa ett självsignerat certifikat med det här PowerShell-skriptet

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Steg 3: Ladda upp ditt självsignerade certifikat till Key Vault
Innan du överför certifikatet till Key Vault som skapats i steg 1 måste det konverteras till ett format som Microsoft.Compute-resursleverantören förstår. Nedanstående PowerShell-skript låter dig göra det

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
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Steg 4: Hämta URL:en för ditt självsignerade certifikat i Key Vault
Microsoft.Compute-resursprovidern behöver en URL till hemligheten i Nyckelvalvet när den etablerade virtuella datorn etableras. Detta gör det möjligt för Microsoft.Compute-resursprovidern att hämta hemligheten och skapa motsvarande certifikat på den virtuella datorn.

> [!NOTE]
> Webbadressen till hemligheten måste innehålla versionen också. En exempel-URL ser\/ut som nedan https: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Mallar
Du kan få länken till webbadressen i mallen med hjälp av koden nedan

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Du kan få den här webbadressen med kommandot Nedan för PowerShell

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Steg 5: Referera till url:en för självsignerade certifikat när du skapar en virtuell dator
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
När du skapar en virtuell dator via mallar refereras certifikatet i avsnittet hemligheter och avsnittet winRM enligt nedan:

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

En exempelmall för ovanstående hittar du här på [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Källkod för den här mallen finns på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Steg 6: Ansluta till den virtuella datorn
Innan du kan ansluta till den virtuella datorn måste du se till att datorn är konfigurerad för WinRM-fjärrhantering. Starta PowerShell som administratör och kör kommandot nedan för att kontrollera att du är konfigurerad.

    Enable-PSRemoting -Force

> [!NOTE]
> Du kan behöva se till att WinRM-tjänsten körs om ovanstående inte fungerar. Du kan göra det med`Get-Service WinRM`
> 
> 

När installationen är klar kan du ansluta till den virtuella datorn med kommandot nedan

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
