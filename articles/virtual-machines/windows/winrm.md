---
title: Konfigurera WinRM-åtkomst för en virtuell dator i Azure | Microsoft Docs
description: Konfigurera WinRM-åtkomst för användning med en Azure-dator som skapats i Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: 5fa82dd4a85ff2e62848df0fdc6006922005a84b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM i Azure Service Management vs Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* En översikt över Azure Resource Manager finns [artikel](../../azure-resource-manager/resource-group-overview.md)
* Skillnader mellan Azure Service Management och Azure Resource Manager finns det [artikel](../../resource-manager-deployment-model.md)

Den viktigaste skillnaden i konfigurationen av WinRM-konfigurationen mellan två platser är hur certifikatet installeras på den virtuella datorn. I Azure Resource Manager-stacken modelleras certifikaten som resurser som hanteras av Key Vault Resource Provider. Därför kan användaren måste ange sina egna certifikat och överföra den till ett Nyckelvalv innan den används i en virtuell dator.

Här är de steg som du måste utföra för att konfigurera en virtuell dator med WinRM-anslutningen

1. Skapa en Key Vault-lösning
2. Skapa ett självsignerat certifikat
3. Överför ditt självsignerade certifikat till Key Vault
4. Hämta URL för ditt självsignerade certifikat i Nyckelvalvet
5. Referera till URL: en självsignerade certifikat när du skapar en virtuell dator

## <a name="step-1-create-a-key-vault"></a>Steg 1: Skapa en Key Vault
Du kan använda den under kommando för att skapa Nyckelvalvet

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Steg 2: Skapa ett självsignerat certifikat
Du kan skapa ett självsignerat certifikat med hjälp av PowerShell-skript

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Steg 3: Överför din självsignerade certifikatet i nyckelvalvet
Den måste konverteras till ett format som Microsoft.Compute-resursprovidern kan förstå innan du laddar upp certifikatet i nyckelvalvet som skapades i steg 1. Den nedan PowerShell skript kan du göra det

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Steg 4: Hämta URL för ditt självsignerade certifikat i Nyckelvalvet
Microsoft.Compute-resursprovidern måste en URL till hemligheten i Nyckelvalvet vid etablering av den virtuella datorn. Detta gör att Microsoft.Compute-resursprovidern att hämta hemligheten och skapa motsvarande certifikat på den virtuella datorn.

> [!NOTE]
> URL: en för hemlighet måste innehålla versionen också. En exempel-URL som ser ut som nedan https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Mallar
Du kan hämta en länk till en URL i en mall med hjälp av den under

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Du kan hämta den här URL: en med hjälp av den under PowerShell-kommando

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Steg 5: Referera självsignerade certifikat-URL när du skapar en virtuell dator
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
När du skapar en virtuell dator via mallar, hämtar certifikatet refereras i avsnittet hemligheter och avsnittet winRM enligt nedan:

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

En exempelmall för ovanstående finns här på [201 vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Källkoden för den här mallen finns på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Steg 6: Ansluta till den virtuella datorn
Innan du kan ansluta till den virtuella datorn måste du kontrollera att är datorn konfigurerad för fjärrhantering med WinRM. Starta PowerShell som administratör och kör den nedan kommando för att kontrollera att du har konfigurerat.

    Enable-PSRemoting -Force

> [!NOTE]
> Du kan behöva kontrollera att WinRM-tjänsten körs om detta inte fungerar. Du kan göra det med `Get-Service WinRM`
> 
> 

När installationen är klar kan du ansluta till den virtuella datorn med hjälp av den nedan kommando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
