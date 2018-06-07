---
title: Aktivera kryptering för service fabric Linux-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar kryptering för Service Fabric-kluster skala in i Azure med hjälp av Azure Resource Manager, Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2018
ms.author: v-viban
ms.openlocfilehash: 46f7f88768ab7ae9d84f392f340750865fef3b96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655751"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Aktivera kryptering för service fabric Linux klusternoder 
> [!div class="op_single_selector"]
> * [Disk Encryption för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Följ stegen nedan för att aktivera kryptering på klusternoder för Service Fabric Linux. Behöver du gör följande för varje nod-skalningsuppsättningar för typer för virtuella datorer. För att kryptera noderna kommer vi utnyttja Azure Disk Encryption-funktioner för virtuella datorer.

Guiden innehåller följande procedurer:

* Nyckelkoncept som du behöver känna av att aktivera kryptering på Service Fabric-kluster för Linux virtuella skala har angetts.
* Förutsättningar steg ska följas innan du aktiverar kryptering på den virtuella datorns skaluppsättning för Service Fabric Linux-kluster.
* Steg ska följas för att aktivera kryptering på Service Fabric-kluster för Linux virtuella skala ställer.


## <a name="prerequisites"></a>Förutsättningar

1. **Självregistrering** – för att kunna använda, virtuella skala disk encryption Förhandsgranskning kräver självregistrering
2. Du kan registrera din prenumeration genom att köra följande steg: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Vänta ungefär 10 minuter tills tillståndet som 'Registrerade'. Du kan kontrollera status genom att köra följande kommando: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** -skapa en KeyVault i samma prenumeration och region, eftersom virtuella datorns skaluppsättning och ange åtkomstprincipen 'EnabledForDiskEncryption' för KeyVault med dess PS-cmdlet. Du kan också ange principen med hjälp av KeyVault UI i Azure-portalen: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Installera senaste [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , som innehåller de nya kommandona för kryptering.
6. Installera den senaste versionen av [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är VMSS ADE-cmdletar för att aktivera ([ange](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1))-kryptering, hämta ([hämta](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) krypteringsstatus och ta bort ([inaktivera](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) kryptering på skala anger instansen. 

| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Inaktivera AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Ange AzureRmVmssDiskEncryptionExtension   | 3.4.0 eller senare | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* Virtual machine scale set kryptering stöds endast för skalningsuppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterad) disk skaluppsättningar.
* Virtual machine scale set kryptering stöds för datavolymen för Linux virtuella datorns skaluppsättning. OS-diskkryptering stöds inte i den aktuella förhandsvisningen för Linux.
* Virtuella skaluppsättning VM spegla och uppgraderingsåtgärderna stöds inte i den aktuella förhandsvisningen.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Skapa nya Linux-kluster och aktivera kryptering

Använd följande kommandon för att skapa kluster och aktivera kryptering med hjälp av Azure Resource Manager-mall & automatiskt signerat certifikat.

### <a name="log-in-to-azure"></a>Logga in på Azure  

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du behöver skapa en anpassad mall så att de passar dina behov, rekommenderas att du börjar med en av de mallar som är tillgängliga på den [azure service fabric mallen prover](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) för Linux-kluster. 

Om du redan har en anpassad mall och sedan se till är att kontrollera att alla tre certifikatrelaterade parametrar i mallen och parameterfilen är namngivna enligt följande och värden null på följande sätt.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Sedan för Linux virtuella datorns skaluppsättning - stöds endast kryptering av data så vi måste lägga till datadisk med hjälp av Azure Resource Manager-mall. Uppdatera mallen för data disk etablera enligt nedan:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Här är motsvarande CLI-kommando för att göra detsamma. Ändra värdena i declare-satser till lämpliga värden. CLI har stöd för alla andra parametrar som har stöd för ovanstående powershell-kommando.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Linux data disk montering
Innan vi fortsätter med kryptering på Linux virtuella datorns skaluppsättning behöver vi Kontrollera tillagda datadisk monteras korrekt eller inte. Logga in på Linux-kluster VM och kör LSBLK kommandot. Utdata ska visa data som lagts till disken på montera punkt kolumnen.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Distribuera program till Linux Service Fabric-kluster
Följ steg och vägledning för att [distribuera program i klustret](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Aktivera kryptering för Service Fabric-kluster för Linux virtuella datorns skaluppsättning skapade ovan
 
```Powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Validera om kryptering aktiverats för Linux virtuella skala har angetts.
Hämta status för en skaluppsättning för hela den virtuella datorn eller en instans av virtuell dator i skaluppsättning. Se kommandona nedan.
Dessutom kan användare logga in på Linux-kluster VM och kör LSBLK kommando. Utdata ska visa data som lagts till disken på montera punkt kolumn och typen Crypt för som lagts till datadisken.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```



#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Inaktivera hårddiskkryptering för Service Fabric-kluster virtuella datorns skaluppsättning 
Inaktivera diskkryptering gäller för skaluppsättning för hela den virtuella datorn och inte av instans 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Nu har du en säker kluster med hur du aktiverar/inaktiverar kryptering för Linux Service Fabric-kluster virtuella datorns skaluppsättning. Nästa [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md) 

