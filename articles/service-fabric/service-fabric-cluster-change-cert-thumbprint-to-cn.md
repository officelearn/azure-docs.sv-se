---
title: Uppdatera ett Azure Service Fabric-kluster om du vill använda certifikatets unika namn | Microsoft Docs
description: Lär dig hur du växlar Service Fabric-kluster från att använda certifikattumavtryck till med hjälp av certifikatets unika namn.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/01/2019
ms.author: aljo
ms.openlocfilehash: e7d5e51ea7048a134a5085715dec1797af32da17
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664428"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Ändra kluster från tumavtrycket för certifikatet till unika namn
Inga två certifikat kan ha samma tumavtryck, vilket gör förnya certifikatet för klustret eller management svårt. Flera certifikat kan dock ha samma namn eller ämne.  Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare. Den här artikeln beskriver hur du uppdaterar en Service Fabric-kluster för certifikatets unika namn istället för certifikatets tumavtryck.

>[!NOTE]
> Om du har två tumavtryck deklarerats i mallen kan behöva du utföra två distributioner.  Den första distributionen är klar innan du följer stegen i den här artikeln.  Den första distributionen anger din **tumavtryck** -egenskapen i mallen för att certifikatet som används och tar bort den **thumbprintSecondary** egenskapen.  Följ stegen i den här artikeln för den andra distributionen.
 
## <a name="get-a-certificate"></a>Få ett certifikat
Först hämtar ett certifikat från en [certifikatutfärdare (CA)](https://wikipedia.org/wiki/Certificate_authority).  Namnet på certifikatet ska vara namnet på klustret.  Till exempel ”myclustername.southcentralus.cloudapp.azure.com”.  

Du kan få en CA-signerat certifikat från en kostnadsfri eller öppna certifikatutfärdare i testsyfte.

> [!NOTE]
> Självsignerat certifikat, inklusive de som skapas när du distribuerar Service Fabric-kluster i Azure portal, stöds inte.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera den i skalningsuppsättningen
Service Fabric-kluster har distribuerats på en VM-skalningsuppsättning i Azure.  Överför certifikatet till ett nyckelvalv och sedan installera det på virtuella datorns skalningsuppsättning som klustret körs på.

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
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Scale set hemligheter stöder inte till samma resurs-ID för två separata hemligheter, eftersom varje hemlighet är en version, unik resurs. 

## <a name="download-and-update-the-template-from-the-portal"></a>Ladda ned och uppdatera mallen från portalen
Certifikatet har installerats på underliggande skalningsuppsättningen, men du måste också uppdatera Service Fabric-klustret för att använda certifikatet och dess namn.  Hämta nu mallen för distributionen av klustret.  Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till resursgruppen som är värd för klustret.  I **inställningar**väljer **distributioner**.  Välj den senaste distributionen och klicka på **visa mall**.

![Visa mallar][image1]

Ladda ned mall och parametrar JSON-filerna till den lokala datorn.

Öppna parameterfilen i en textredigerare och Lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Öppna mallfilen i en textredigerare och göra tre uppdateringar för certifikatets unika namn.

1. I den **parametrar** lägger du till en *certificateCommonName* parameter:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Överväg även att ta bort den *certificateThumbprint*, den kan inte längre behövs.

2. I den **Microsoft.Compute/virtualMachineScaleSets** resurs, uppdatera tillägg för virtuell dator om du vill använda det gemensamma namnet i certifikatinställningarna i stället för tumavtrycket för.  I **virtualMachineProfile**->**extensionProfile**->**tillägg**->**egenskaper** -> **inställningar**->**certifikat**, lägga till `"commonNames": ["[parameters('certificateCommonName')]"],` och ta bort `"thumbprint": "[parameters('certificateThumbprint')]",`.
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
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  I den **Microsoft.ServiceFabric/clusters** resurs, Uppdateringsversion API: et till ”2018-02-01”.  Också lägga till en **certificateCommonNames** inställning med en **commonNames** egenskap och ta bort den **certifikat** inställning (med tumavtrycksegenskapen) enligt följande Exempel:
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
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster security](service-fabric-cluster-security.md).
* Lär dig hur du [förnya ett klustercertifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
