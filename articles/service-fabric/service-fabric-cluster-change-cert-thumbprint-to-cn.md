---
title: Uppdatera en Azure Service Fabric-klustret för att använda certifikatets unika namn | Microsoft Docs
description: Lär dig mer om att växla en Service Fabric-kluster från att använda certifikattumavtryck med certifikatets unika namn.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: a2b0d49e9fc81837db5c53bc9e52f3a0649b0b00
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Ändra kluster från tumavtrycket för certifikatet till eget namn
Inga två certifikat kan ha samma tumavtryck, vilket gör förnya certifikatet för klustret eller hantering svårt. Flera certifikat kan dock ha samma namn eller ämne.  Växla ett installerat kluster från att använda certifikattumavtryck till att använda certifikat namn gör certifikathantering mycket enklare. Den här artikeln beskriver hur du uppdaterar en Service Fabric-kluster som körs för att använda certifikatets unika namn i stället för tumavtrycket för certifikatet.
 
## <a name="get-a-certificate"></a>Hämta ett certifikat
Först hämta ett certifikat från en [certifikat (certifikatutfärdare)](https://wikipedia.org/wiki/Certificate_authority).  Namnet på certifikatet ska vara namnet på klustret.  Till exempel ”myclustername.southcentralus.cloudapp.azure.com”.  

Du kan få en CA-signerat certifikat från en fri eller öppna certifikatutfärdare i testsyfte kan.

> [!NOTE]
> Självsignerade certifikat, inklusive de som skapas när du distribuerar ett Service Fabric-kluster i Azure portal stöds inte.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera det i skaluppsättning
I Azure har Service Fabric-klustret distribuerats på en skaluppsättning för virtuell dator.  Överför certifikatet till en nyckelvalvet och installera den på virtuella datorns skaluppsättning som körs på klustret på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

## <a name="download-and-update-the-template-from-the-portal"></a>Hämta och uppdatera mall från portalen
Certifikatet har installerats på den underliggande skaluppsättning, men du måste också uppdatera Service Fabric-klustret för att använda certifikatet och dess namn.  Nu kan hämta mall för klusterdistribution.  Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till den resursgrupp som värd för klustret.  I **inställningar**väljer **distributioner**.  Välj den senaste distributionen och klicka på **visa mall**.

![Visa mallar][image1]

Hämta mall och parametrar JSON-filerna till den lokala datorn.

Först öppna filen parametrar i en textredigerare och Lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Öppna mallfilen i en textredigerare och göra tre uppdateringar som stöd för certifikatets unika namn.

1. I den **parametrar** lägger du till en *certificateCommonName* parameter:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Överväg också att ta bort den *certificateThumbprint*, den kan inte längre behövs.

2. I den **Microsoft.Compute/virtualMachineScaleSets** resurs, uppdatera tillägget för virtuell dator om du vill använda det gemensamma namnet i inställningarna i stället för tumavtrycket för certifikatet.  I **virtualMachineProfile**->**extenstionProfile**->**tillägg**->**egenskaper** -> **inställningar**->**certifikat**, lägga till `"commonNames": ["[parameters('certificateCommonName')]"],` och ta bort `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
              "extensionProfile": {
                "extensions": [
                  {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": true,
                      "protectedSettings": {
                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                      },
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[variables('vmNodeType0Name')]",
                        "dataPath": "D:\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "enableParallelJobs": true,
                        "nicPrefixOverride": "[variables('subnet0Prefix')]",
                        "certificate": {
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  I den **Microsoft.ServiceFabric/clusters** resurs, Uppdateringsversion API: et ”2018-02-01”.  Också lägga till en **certificateCommonNames** med en **commonNames** egenskap och ta bort den **certifikat** inställning (med egenskapen tumavtrycket) enligt följande Exempel:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera den uppdaterade mallen när du har gjort ändringarna.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [förnya ett certifikat för kluster](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png