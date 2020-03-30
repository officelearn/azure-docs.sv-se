---
title: Uppdatera ett kluster för att använda certifikatets gemensamma namn
description: Lär dig hur du växlar ett Service Fabric-kluster från att använda certifikattumavtryck till att använda certifikatets gemensamma namn.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 66c49ccb7b7633d0eff392b676bb381118eb64a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610207"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Ändra kluster från tumavtrycket för certifikat till eget namn
Det finns inga två certifikat som har samma tumavtryck, vilket gör det svårt att rulla över klustercertifikat eller hantera. Flera certifikat kan dock ha samma gemensamma namn eller ämne.  Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare. I den här artikeln beskrivs hur du uppdaterar ett Service Fabric-kluster som körs för att använda certifikatets gemensamma namn i stället för certifikatets tumavtryck.

>[!NOTE]
> Om du har två tumavtryck som deklarerats i mallen måste du utföra två distributioner.  Den första distributionen görs innan du följer stegen i den här artikeln.  Den första distributionen anger **egenskapen tumavtryck** i mallen till det certifikat som används och tar bort **egenskapen tumavtryckSecondary.**  För den andra distributionen följer du stegen i den här artikeln.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Skaffa ett certifikat
Hämta först ett certifikat från en [certifikatutfärdarcertifikatutfärdning](https://wikipedia.org/wiki/Certificate_authority).  Certifikatets gemensamma namn ska vara värdnamnet för klustret.  Till exempel "myclustername.southcentralus.cloudapp.azure.com".  

I testsyfte kan du få ett certifikatutfärdare signerat certifikat från en kostnadsfri eller öppen certifikatutfärdare.

> [!NOTE]
> Självsignerade certifikat, inklusive de som genereras vid distribution av ett Service Fabric-kluster i Azure-portalen, stöds inte.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera det i skalningsuppsättningen
I Azure distribueras ett Service Fabric-kluster på en skalningsuppsättning för virtuella datorer.  Ladda upp certifikatet till ett nyckelvalv och installera det sedan på den virtuella datorns skalningsuppsättning som klustret körs på.

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
> Skalningsuppsättningshemligheter stöder inte samma resurs-ID för två separata hemligheter, eftersom varje hemlighet är en version av, unik resurs. 

## <a name="download-and-update-the-template-from-the-portal"></a>Hämta och uppdatera mallen från portalen
Certifikatet har installerats på den underliggande skalningsuppsättningen, men du måste också uppdatera Service Fabric-klustret för att använda certifikatet och dess gemensamma namn.  Nu hämtar du mallen för klusterdistributionen.  Logga in på [Azure-portalen](https://portal.azure.com) och navigera till resursgruppen som är värd för klustret.  Välj **Distributioner**i **Inställningar**.  Markera den senaste distributionen och klicka på **Visa mall**.

![Visa mallar][image1]

Ladda ner mallen och parametrarna JSON filer till din lokala dator.

Öppna först parameterfilen i en textredigerare och lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Öppna sedan mallfilen i en textredigerare och gör tre uppdateringar för att stödja certifikatets gemensamma namn.

1. Lägg till en *parameter för certificateCommonName* i **parametern parametrar:**
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Också överväga att ta bort *certifikatetDumbprint*, det kanske inte längre refereras i Resource Manager mallen.

2. I **microsoft.Compute/virtualMachineScaleSets-resursen** uppdaterar du tillägget för virtuella datorer så att det använder det vanliga namnet i certifikatinställningarna i stället för tumavtrycket.  I **virtualMachineProfile**->**extensionProfile-tilläggsegenskapersinställningar**->**extensions**->**properties**->**settings**-> `"thumbprint": "[parameters('certificateThumbprint')]",`**certifikat**lägger du till `"commonNames": ["[parameters('certificateCommonName')]"],` och tar bort .
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

3.  I **Microsoft.ServiceFabric/clusters-resursen** uppdaterar du API-versionen till "2018-02-01".  Lägg också till en **certifikatInställningar** med egenskapen **commonNames** och ta bort **certifikatinställningen** (med tumavtrycksegenskapen) som i följande exempel:
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

Mer information finns i [Distribuera ett Service Fabric-kluster som använder certifikatets gemensamma namn i stället för tumavtryck.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [klustersäkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [övertäcker ett klustercertifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
