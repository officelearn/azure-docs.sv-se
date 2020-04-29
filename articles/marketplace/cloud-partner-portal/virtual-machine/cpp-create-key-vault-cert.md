---
title: Skapa ett Azure Key Vault-certifikat | Azure Marketplace
description: Förklarar hur du registrerar en virtuell dator från en Azure-distribuerad virtuell hård disk.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148247"
---
# <a name="create-certificates-for-azure-key-vault"></a>Skapa certifikat för Azure Key Vault

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Azures avbildnings certifiering för virtuella datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) för att hantera dina migrerade erbjudanden.

Den här artikeln beskriver hur du etablerar de självsignerade certifikat som krävs för att upprätta en WinRM-anslutning (Windows Remote Management) till en virtuell Azure-värd dator (VM). Den här processen består av tre steg:

1.    Skapa säkerhetscertifikatet. 
2.    Skapa Azure Key Vault för att lagra certifikatet. 
3.    Lagra certifikaten i det här nyckel valvet. 

Du kan använda antingen en ny eller en befintlig Azure-resurs grupp för detta arbete.  Den tidigare metoden används i följande förklaring.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Skapa certifikatet

Redigera och kör följande Azure PowerShell-skript för att skapa certifikat filen (. pfx) i en lokal mapp.  Du måste ersätta värdena för följande parametrar:

|  **ProfileServiceApplicationProxy**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Lokal mapp för att spara. pfx-filen till  |
| `$location`    | En av de geografiska standard platserna för Azure  |
| `$vmName`      | Namnet på den planerade virtuella Azure-datorn   |
| `$certname`    | Certifikatets namn. måste matcha det fullständigt kvalificerade domän namnet för den planerade virtuella datorn  |
| `$certpassword` | Lösen ordet för certifikaten måste matcha det lösen ord som används för den planerade virtuella datorn  |
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
> Se till att PowerShell-konsolsessionen är aktiv under dessa steg så att värdena för de olika parametrarna bevaras.

> [!WARNING]
> Om du sparar det här skriptet lagrar du det bara på en säker plats eftersom det innehåller säkerhets information (ett lösen ord).


## <a name="create-the-key-vault"></a>Skapa nyckel valvet

Kopiera innehållet i [distributions mal len Key Vault](./cpp-key-vault-deploy-template.md) till en fil på den lokala datorn. (i exempel skriptet nedan är `C:\certLocation\keyvault.json`den här resursen.)  Redigera och kör följande Azure PowerShell-skript för att skapa en Azure Key Vault-instans och den tillhör ande resurs gruppen.  Du måste ersätta värdena för följande parametrar:

|  **ProfileServiceApplicationProxy**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Godtycklig numerisk sträng som läggs till i distributions identifierare                     |
| `$rgName`             | Namn på Azure Resource Group (RG) som ska skapas                                        |
|  `$location`          | En av de geografiska standard platserna för Azure                                  |
| `$kvTemplateJson`     | Sökväg till filen (Key Vault. JSON) som innehåller Resource Manager-mall för nyckel valv |
| `$kvname`             | Namnet på det nya nyckel valvet                                                       |
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

Nu kan du lagra certifikaten, som finns i. pfx-filen, i det nya nyckel valvet genom att köra följande skript. 

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

Härnäst ska du [distribuera en virtuell dator från den virtuella användar avbildningen](./cpp-deploy-vm-user-image.md).
