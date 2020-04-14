---
title: Skapa ett Azure Key Vault-certifikat | Azure Marketplace
description: Förklarar hur du registrerar en virtuell dator från en Azure-distribuerad virtuell hårddisk.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 09e82b9905104df9b1902b0f64f6cfdf812aabb8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274029"
---
# <a name="create-certificates-for-azure-key-vault"></a>Skapa certifikat för Azure Key Vault

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Azure VM-avbildningscertifiering](https://aks.ms/CertifyVMimage) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du etablerar de självsignerade certifikat som krävs för att upprätta en WinRM-anslutning (Windows Remote Management) till en virtuell virtuell dator som är värd för Azure.This article explains how to provision the self-signed certificates required to establish a Windows Remote Management (WinRM) connectivity to an Azure-hosted virtual machine (VM). Den här processen består av tre steg:

1.    Skapa säkerhetscertifikatet. 
2.    Skapa Azure Key Vault för att lagra det här certifikatet. 
3.    Lagra certifikaten till det här nyckelvalvet. 

Du kan använda antingen en ny eller en befintlig Azure-resursgrupp för det här arbetet.  Det tidigare tillvägagångssättet används i följande förklaring.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Skapa certifikatet

Redigera och kör följande Azure Powershell-skript för att skapa certifikatfilen (.pfx) i en lokal mapp.  Du måste ersätta värdena för följande parametrar:

|  **Parametern**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Lokal mapp för att spara PFX-filen i  |
| `$location`    | En av Azure-standardge geographic locations  |
| `$vmName`      | Namn på den planerade virtuella Azure-datorn   |
| `$certname`    | Intygets namn. måste matcha det fullständigt kvalificerade domännamnet för den planerade virtuella datorn  |
| `$certpassword` | Lösenord för certifikaten, måste matcha lösenordet som används för den planerade virtuella datorn  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Håll samma PowerShell-konsolsession aktiv under dessa steg så att värdena för de olika parametrarna behålls.

> [!WARNING]
> Om du sparar skriptet lagrar du det bara på en säker plats eftersom det innehåller säkerhetsinformation (ett lösenord).


## <a name="create-the-key-vault"></a>Skapa nyckelvalvet

Kopiera innehållet i [distributionsmallen för nyckelvalvet](./cpp-key-vault-deploy-template.md) till en fil på den lokala datorn. (i exempelskriptet nedan är `C:\certLocation\keyvault.json`den här resursen .)  Redigera och kör följande Azure Powershell-skript för att skapa en Azure Key Vault-instans och den associerade resursgruppen.  Du måste ersätta värdena för följande parametrar:

|  **Parametern**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Godtycklig numerisk sträng som läggs till i distributionsidentifierare                     |
| `$rgName`             | Azure-resursgrupp (RG) namn för att skapa                                        |
|  `$location`          | En av Azure-standardge geographic locations                                  |
| `$kvTemplateJson`     | Sökväg till filen (keyvault.json) som innehåller Resource Manager-mall för nyckelvalv |
| `$kvname`             | Namn på det nya nyckelvalvet                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Lagra certifikatet

Du kan nu lagra certifikaten, som finns i PFX-filen, till det nya nyckelvalvet genom att köra följande skript. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Nästa steg

Därefter kommer du att [distribuera en virtuell dator från din användare VM-avbildning](./cpp-deploy-vm-user-image.md).
