---
title: Aktivera kryptering för service fabric-kluster i Windows | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar kryptering för Service Fabric klusternoder i Azure med hjälp av Azure Resource Manager, Azure Key Vault.
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
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655748"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Aktivera kryptering för service fabric Windows klusternoder 
> [!div class="op_single_selector"]
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Disk Encryption för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Följ stegen nedan för att aktivera kryptering på klusternoder för Service Fabric Windows. Behöver du gör följande för varje nod-skalningsuppsättningar för typer för virtuella datorer. För att kryptera noderna kommer vi utnyttja Azure Disk Encryption-funktioner för virtuella datorer.

Guiden innehåller följande procedurer:

* Nyckelkoncept som du behöver känna av att aktivera kryptering på Windows Service Fabric-kluster virtuella skala har angetts.
* Förutsättningar steg ska följas innan du aktiverar kryptering på den virtuella datorns skaluppsättning för Service Fabric-kluster i Windows.
* Steg ska följas för att aktivera kryptering på Windows Service Fabric-kluster virtuella skala ställer.


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
4. **Azure Key Vault** -skapa en KeyVault i samma prenumeration och region eftersom skalan och ange åtkomstprincipen 'EnabledForDiskEncryption' för KeyVault med dess PS-cmdlet. Du kan också ange principen med hjälp av KeyVault UI i Azure-portalen: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Installera senaste [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , som innehåller de nya kommandona för kryptering.
6. Installera den senaste versionen av [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är virtuella datorns skaluppsättning ange ADE-cmdletar för att aktivera ([ange](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1))-kryptering, hämta ([hämta](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) krypteringsstatus och ta bort ([inaktivera](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) kryptering på skaluppsättning instans.

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
* Virtual machine scale set kryptering stöds av Operativsystemet och datavolymer för Windows VMSS. Inaktivera kryptering stöds av Operativsystemet och datavolymer för skaluppsättning för Windows.
* Virtuella skaluppsättning VM spegla och uppgraderingsåtgärderna stöds inte i den aktuella förhandsvisningen.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Skapa nytt kluster och aktivera kryptering

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

Om du behöver skapa en anpassad mall så att de passar dina behov, rekommenderas att du börjar med en av de mallar som är tillgängliga på den [azure service fabric mallen exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar [anpassa mallen för klustret] [anpassa din-kluster-mall] nedan.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Distribuera program till Windows Service Fabric-kluster
Följ steg och vägledning för att [distribuera program i klustret](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Aktivera kryptering för Service Fabric-kluster virtuella datorns skaluppsättning skapade ovan
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Validera om kryptering aktiverat för Windows virtuella skala har angetts.
Hämta status för en skaluppsättning för hela den virtuella datorn eller en instans i skaluppsättning. Se kommandona nedan.
Dessutom kan användare logga in till en virtuell dator i skaluppsättning och se till att enheter krypteras

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
Nu har du en säker kluster med hur du aktiverar/inaktiverar kryptering för Service Fabric-kluster virtuella datorns skaluppsättning. Nästa [Disk kryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

