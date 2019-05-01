---
title: Skapa ett Azure Key Vault-certifikat | Azure Marketplace
description: Beskriver hur du registrerar en virtuell dator från en Azure-distribuerade virtuella Hårddisken.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 5163aa0a9195aa712fa333667b3f7ccf227469be
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938390"
---
# <a name="create-certificates-for-azure-key-vault"></a>Skapa certifikat för Azure Key Vault

Den här artikeln beskrivs hur du etablerar de självsignerade certifikat som krävs för att upprätta en anslutning för Windows Remote Management (WinRM) till en Azure-baserad virtuell dator (VM). Den här processen består av tre steg:

1.  Skapa säkerhetscertifikatet. 
2.  Skapa Azure Key Vault för att lagra det här certifikatet. 
3.  Store certifikat till det här nyckelvalvet. 

Du kan använda en ny eller en befintlig Azure-resursgrupp för arbetet.  Den tidigare metoden används i följande förklaring.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Skapa certifikatet

Redigera och kör följande Azure Powershell-skript för att skapa certifikatfilen (.pfx) i en lokal mapp.  Du måste ersätta värdena för följande parametrar:

|  **Parametern**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Lokal mapp för att spara den .pfx-fil till  |
| `$location`    | En av Azure standard geografiska platser  |
| `$vmName`      | Namnet på den planerade Azure-datorn   |
| `$certname`    | Namnet på certifikatet. måste matcha det fullständigt kvalificerade domännamnet för den planerade virtuella datorn  |
| `$certpassword` | Lösenord för certifikat, måste matcha det lösenord som används för den planerade virtuella datorn  |
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
> Behåll samma PowerShell-konsolsessionen aktiva under de här stegen så att värdena för olika parametrar som ska behållas.

> [!WARNING]
> Om du sparar det här skriptet kan du lagra den bara på en säker plats eftersom den innehåller säkerhetsinformation (ett lösenord).


## <a name="create-the-key-vault"></a>Skapa key vault

Kopiera innehållet i den [nyckelvalv Distributionsmall](./cpp-key-vault-deploy-template.md) till en fil på den lokala datorn. (i exemplet skriptet nedan för den här resursen är `C:\certLocation\keyvault.json`.)  Redigera och kör följande Azure Powershell-skript för att skapa en Azure Key Vault-instans och den associerade resursgruppen.  Du måste ersätta värdena för följande parametrar:

|  **Parametern**        |   **Beskrivning**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Valfri numerisk sträng som läggs till distribution identifierare                     |
| `$rgName`             | Azure-resurs (RG) gruppnamn för att skapa                                        |
|  `$location`          | En av Azure standard geografiska platser                                  |
| `$kvTemplateJson`     | Sökvägen till filen (keyvault.json) som innehåller Resource Manager-mall för key vault |
| `$kvname`             | Namnet på nytt nyckelvalv                                                       |
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

## <a name="store-the-certificate"></a>Store certifikatet

Nu kan du lagra certifikat, som finns i PFX-filen till det nya nyckelvalvet genom att köra följande skript. 

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Nästa steg

Sedan kommer du att [distribuera en virtuell dator från din användaravbildning för VM](./cpp-deploy-vm-user-image.md).
