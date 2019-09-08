---
title: Uppdatera ett Azure Service Fabric-kluster för att använda certifikatets egna namn | Microsoft Docs
description: Lär dig hur du växlar ett Service Fabric kluster från att använda certifikat tumavtrycken till att använda certifikatets egna namn.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: 3618339349d618b371a40d3b37ebc30192c067ca
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764824"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Ändra klustret från certifikatets tumavtryck till eget namn
Inga två certifikat kan ha samma tumavtryck, vilket gör det svårt att förnya kluster certifikat eller hantering. Flera certifikat kan dock ha samma egna namn eller ämne.  Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare. I den här artikeln beskrivs hur du uppdaterar ett kör Service Fabric-kluster för att använda certifikatets egna namn i stället för certifikatets tumavtryck.

>[!NOTE]
> Om du har två tumavtryck som har deklarerats i din mall måste du utföra två distributioner.  Den första distributionen görs innan du följer stegen i den här artikeln.  Den första distributionen ställer in din **tumavtryck** -egenskap i mallen till certifikatet som används och tar bort egenskapen **thumbprintSecondary** .  För den andra distributionen följer du stegen i den här artikeln.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Hämta ett certifikat
Börja med att hämta ett certifikat från en [certifikat utfärdare (ca)](https://wikipedia.org/wiki/Certificate_authority).  Certifikatets nätverks namn ska vara värd namnet för klustret.  Till exempel "myclustername.southcentralus.cloudapp.azure.com".  

I test syfte kan du få ett CA-signerat certifikat från en kostnads fri eller öppen certifikat utfärdare.

> [!NOTE]
> Självsignerade certifikat, inklusive de som genereras när du distribuerar ett Service Fabric kluster i Azure Portal, stöds inte.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera det i skalnings uppsättningen
I Azure distribueras ett Service Fabric-kluster på en skal uppsättning för virtuella datorer.  Ladda upp certifikatet till ett nyckel valv och installera det sedan på den skalnings uppsättning för virtuella datorer som klustret körs på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Skalnings uppsättnings hemligheter har inte stöd för samma resurs-ID för två separata hemligheter, eftersom varje hemlighet är en version av en unik resurs. 

## <a name="download-and-update-the-template-from-the-portal"></a>Hämta och uppdatera mallen från portalen
Certifikatet har installerats på den underliggande skalnings uppsättningen, men du måste också uppdatera Service Fabric-klustret för att använda certifikatet och dess eget namn.  Nu kan du ladda ned mallen för kluster distributionen.  Logga in på [Azure Portal](https://portal.azure.com) och navigera till resurs gruppen som är värd för klustret.  I **Inställningar**väljer du **distributioner**.  Välj den senaste distributionen och klicka på **Visa mall**.

![Visa mallar][image1]

Ladda ned mall-och parameter-JSON-filer till den lokala datorn.

Öppna först parameter filen i en text redigerare och Lägg till följande parameter värde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Öppna sedan mallfilen i en text redigerare och gör tre uppdateringar till att ge stöd för certifikatets egna namn.

1. I avsnittet **parametrar** lägger du till en *certificateCommonName* -parameter:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Överväg också att ta bort *certificateThumbprint*, den kanske inte längre refereras till i Resource Manager-mallen.

2. I **Microsoft. Compute/virtualMachineScaleSets** -resursen uppdaterar du tillägget för den virtuella datorn så att det använder det egna namnet i certifikat inställningarna i stället för tumavtrycket.  I **virtualMachineProfile**->**extensionProfile**tillägg egenskaper inställningar certifikat,Läggtill->->->-> `"commonNames": ["[parameters('certificateCommonName')]"],` och ta `"thumbprint": "[parameters('certificateThumbprint')]",`bort.
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

3.  Uppdatera API-versionen till "2018-02-01" i resursen **Microsoft. ServiceFabric/Clusters** .  Lägg också till en **certificateCommonNames** -inställning med en **commonNames** -egenskap och ta bort **certifikat** inställningen (med egenskapen tumavtryck) som i följande exempel:
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

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [förnyar ett kluster certifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera kluster certifikat](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
