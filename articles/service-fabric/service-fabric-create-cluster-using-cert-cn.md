---
title: Skapa ett Azure Service Fabric-kluster med certifikatets unika namn | Microsoft Docs
description: Lär dig hur du skapar ett Service Fabric-kluster med certifikatets unika namn från en mall.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211073"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Distribuera ett Service Fabric-kluster som använder certifikatets unika namn i stället för tumavtrycket
Inga två certifikat kan ha samma tumavtryck, vilket gör förnya certifikatet för klustret eller hantering svårt. Flera certifikat kan dock ha samma namn eller ämne.  Ett kluster med certifikatet nätverksnamnen gör det mycket enklare att certifikathantering. Den här artikeln beskriver hur du distribuerar ett Service Fabric-kluster om du vill använda certifikatets unika namn i stället för tumavtrycket för certifikatet.
 
## <a name="get-a-certificate"></a>Hämta ett certifikat
Först hämta ett certifikat från en [certifikat (certifikatutfärdare)](https://wikipedia.org/wiki/Certificate_authority).  Namnet på certifikatet ska vara namnet på klustret.  Till exempel ”myclustername.southcentralus.cloudapp.azure.com”.  

Du kan få en CA-signerat certifikat från en fri eller öppna certifikatutfärdare i testsyfte kan.

> [!NOTE]
> Självsignerade certifikat, inklusive de som skapas när du distribuerar ett Service Fabric-kluster i Azure portal stöds inte.

## <a name="upload-the-certificate-to-a-key-vault"></a>Överför certifikatet till en nyckelvalv
I Azure har Service Fabric-klustret distribuerats på en skaluppsättning för virtuell dator.  Överför certifikatet till en nyckelvalvet.  När klustret distribuerar installeras certifikatet på virtuella datorns skaluppsättning som körs på klustret på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

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
```

## <a name="download-and-update-a-sample-template"></a>Hämta och uppdatera en exempelmall
Den här artikeln används den [säker kluster med 5 exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) mall och mallparametrar. Hämta den *azuredeploy.json* och *azuredeploy.parameters.json* filer till din dator.

### <a name="update-parameters-file"></a>Uppdatera parameterfilen
Öppna först den *azuredeploy.parameters.json* i en textredigerare och Lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Därefter anger du den *certificateCommonName*, *sourceVaultValue*, och *certificateUrlValue* parametervärden som returneras av skriptet:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Uppdatera mallfilen
Därefter öppnar den *azuredeploy.json* filen i en textredigerare och göra tre uppdateringar som stöd för certifikatets unika namn.

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

2. Ange värdet för den *sfrpApiVersion* variabeln ”2018-02-01”:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. I den **Microsoft.Compute/virtualMachineScaleSets** resurs, uppdatera tillägget för virtuell dator om du vill använda det gemensamma namnet i inställningarna i stället för tumavtrycket för certifikatet.  I **virtualMachineProfile**->**extenstionProfile**->**tillägg**->**egenskaper** -> **inställningar**->**certifikat**, lägga till `"commonNames": ["[parameters('certificateCommonName')]"],` och ta bort `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

4.  I den **Microsoft.ServiceFabric/clusters** resurs, Uppdateringsversion API: et ”2018-02-01”.  Också lägga till en **certificateCommonNames** med en **commonNames** egenskap och ta bort den **certifikat** inställning (med egenskapen tumavtrycket) enligt följande Exempel:
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
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [förnya ett certifikat för kluster](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png