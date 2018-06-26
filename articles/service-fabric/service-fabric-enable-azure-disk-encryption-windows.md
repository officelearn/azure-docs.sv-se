---
title: Aktivera kryptering för Service Fabric-Windows-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar kryptering för Service Fabric klusternoder i Azure med hjälp av Azure Resource Manager och Azure Key Vault.
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
ms.openlocfilehash: 58f9481fa27f44de30fd80fe52e9b6d06d341b41
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750901"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Aktivera kryptering för Service Fabric Windows klusternoder 
> [!div class="op_single_selector"]
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Disk Encryption för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Använd följande steg för att aktivera kryptering på Azure Service Fabric Windows klusternoder. Du behöver gör följande för varje nodtyper eller skalningsuppsättningar i virtuella datorer. För att kryptera noder, ska du använda Azure Disk Encryption-kapaciteten på virtuella datorer.

Guiden innehåller följande procedurer:

* Viktiga begrepp för att aktivera kryptering på skalningsuppsättningar i virtuella datorer för Service Fabric-Windows-kluster.
* Nödvändiga steg att följa innan du aktiverar kryptering på den virtuella datorn skala uppsättningar för Service Fabric-Windows-kluster.
* Steg för att aktivera och inaktivera disk kryptering på skalningsuppsättningar i virtuella datorer för Service Fabric-Windows-kluster.


## <a name="prerequisites"></a>Förutsättningar
1. Registrera din prenumeration genom att ange följande kommando:

   ```PowerShell
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
   ```
   
   Vänta i cirka 10 minuter tills tillståndet är `Registered`. Du kan kontrollera status genom att köra följande kommandon:

   ```PowerShell
   Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
   ```

2. Skapa en nyckelvalvet i samma prenumeration och region som angetts för skalan. Ange åtkomstprincipen `EnabledForDiskEncryption` på nyckelvalv med dess PowerShell-cmdlet. Du kan också ange principen med hjälp av Azure Key Vault Användargränssnittet i Azure-portalen.

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
   ```

3. Installera [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), som har de senaste kryptering-kommandona.

4. Installera den senaste versionen av den [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Använda följande cmdletar för att aktivera ([ange](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1))-kryptering, hämta ([hämta](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) krypteringsstatus och ta bort ([inaktivera](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) kryptering på en skala ange instans:

| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Inaktivera AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Ange AzureRmVmssDiskEncryptionExtension   | 3.4.0 eller senare | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* Virtual machine scale set kryptering stöds endast för skalningsuppsättningar som skapats med hanterade diskar. Den stöds inte för inbyggda (eller ohanterad) disk skaluppsättningar.
* Virtual machine scale set kryptering stöds av Operativsystemet och datavolymer för Windows skalningsuppsättningar i virtuella datorer. Du kan aktivera och inaktivera kryptering.
* VM avbildningsåterställning skaluppsättning för virtuell dator och uppgraderingsåtgärderna stöds inte i den aktuella förhandsvisningen.


## <a name="create-a-windows-cluster"></a>Skapa ett Windows-kluster

Använd följande kommandon för att skapa ett kluster och aktivera kryptering genom att använda en Azure Resource Manager-mall och ett självsignerat certifikat.

### <a name="log-in-to-azure"></a>Logga in på Azure 

```PowerShell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-a-custom-template"></a>Använda en anpassad mall 

Om du behöver skapa en anpassad mall så att de passar dina behov, rekommenderar vi att du börjar med en av de [Azure Service Fabric mallen exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master).

Om du redan har en anpassad mall, se till att är alla tre certifikatrelaterade parametrar i mallen och parameterfilen namngivna på följande sätt. Kontrollera också att följande värden är null.

```JSON
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


```PowerShell


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

### <a name="deploy-an-application-to-the-windows-service-fabric-cluster"></a>Distribuera program till Windows Service Fabric-kluster
Följ steg och vägledning för att [distribuera ett program i klustret](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications).


## <a name="enable-disk-encryption-for-a-virtual-machine-scale-set"></a>Aktivera kryptering för en skaluppsättning för virtuell dator
Aktivera kryptering för virtuella datorns skaluppsättning som du skapade tidigare för Service Fabric-Windows-kluster.
 
```PowerShell

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


## <a name="validate-that-disk-encryption-is-enabled-for-a-virtual-machine-scale-set"></a>Validera disken kryptering är aktiverat för en skaluppsättning för virtuell dator
Använd följande kommandon för att hämta status för en skaluppsättning för hela den virtuella datorn eller en instans av virtuell dator i en skaluppsättning. Du kan också logga in på en virtuell dator i skaluppsättning för den och se till att enheterna är krypterad.

```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


## <a name="disable-disk-encryption-for-a-virtual-machine-scale-set"></a>Inaktivera kryptering för en skaluppsättning för virtuell dator 
Om du inaktiverar kryptering för skaluppsättningen för virtuell dator för ett Service Fabric-Windows-kluster måste du använda följande kommandon. Inaktivera diskkryptering gäller för skaluppsättning för hela den virtuella datorn och inte av instans. 


```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Nu är du har en säker kluster och du vet hur du aktiverar och inaktiverar kryptering för ett Service Fabric-Windows-kluster. Lär dig om [hårddiskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md). 

