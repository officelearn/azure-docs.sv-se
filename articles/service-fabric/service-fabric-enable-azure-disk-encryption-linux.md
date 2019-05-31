---
title: Aktivera diskkryptering för Azure Service Fabric Linux-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar diskkryptering för Azure Service Fabric-klusternoder i Linux med hjälp av Azure Resource Manager och Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258769"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Aktivera diskkryptering för Azure Service Fabric-klusternoder i Linux 
> [!div class="op_single_selector"]
> * [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

I de här självstudierna lär du dig att aktivera diskkryptering på Azure Service Fabric-klusternoder i Linux. Du måste följa dessa steg för varje nodtyper och VM-skalningsuppsättningar. För att kryptera noderna, använder vi Azure Disk Encryption-funktionen på VM-skalningsuppsättningar.

Guiden innehåller följande avsnitt:

* Viktiga begrepp att känna till när aktiverar diskkryptering på VM-skalningsuppsättning i Service Fabric-kluster anger i Linux.
* Steg för att följas innan du aktiverar diskkryptering på Service Fabric klusternoderna i Linux.
* Steg för att om du vill aktivera diskkryptering på noder i Service Fabric i Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

 **Självregistrering**

Självregistrering kräver att förhandsversionen av disk-kryptering för virtuella datorns skalningsuppsättning. Använd följande steg:

1. Kör följande kommando: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Vänta ungefär 10 minuter förrän statusen har ändrats *registrerad*. Du kan kontrollera statusen genom att köra följande kommando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Skapa ett nyckelvalv i samma prenumeration och region som skalningsuppsättningen. Välj sedan den **EnabledForDiskEncryption** åtkomstprincipen i nyckelvalvet med hjälp av dess PowerShell-cmdlet. Du kan också ange principen med hjälp av Key Vault-Användargränssnittet i Azure-portalen med följande kommando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installera den senaste versionen av den [Azure CLI](/cli/azure/install-azure-cli), som innehåller de nya kommandona för kryptering.

3. Installera den senaste versionen av den [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är VM-skalningsuppsättningen Azure Disk Encryption-cmdletar för att aktivera ([ange](/powershell/module/az.compute/set-azvmssdiskencryptionextension))-kryptering, hämta ([hämta](/powershell/module/az.compute/get-azvmssvmdiskencryption)) krypteringsstatus och ta bort ([inaktivera](/powershell/module/az.compute/disable-azvmssdiskencryption)) kryptering på skalan skalningsuppsättningsinstans.


| Kommando | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 eller senare | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* Kryptering för VM-skalningsuppsättningar stöds endast för skalningsuppsättningar som skapats med hanterade diskar. Det finns inte stöd för internt (eller ohanterade) disk scale sets.
* Både kryptering och inaktivera kryptering har stöd för OS- och datavolymer i VM scale sets i Linux. 
* Åtgärder för virtuell dator (VM) reimage och uppgraderingen för VM-skalningsuppsättningar stöds inte i den aktuella förhandsversionen.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Skapa ett nytt kluster och aktivera diskkryptering

Använd följande kommandon för att skapa ett kluster och aktivera diskkryptering med hjälp av en Azure Resource Manager-mall och ett självsignerat certifikat.

### <a name="sign-in-to-azure"></a>Logga in på Azure  

Logga in med följande kommandon:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du behöver skapa en anpassad mall, rekommenderar vi att du använder någon av mallar på den [mallexempel för Azure Service Fabric-kluster skapas](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) sidan. 

Om du redan har en anpassad mall kan du kontrollera att alla tre certifikatrelaterade parametrar i mallen och parameterfilen namnges enligt följande. Se också till att värden är null på följande sätt:

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

Eftersom endast data diskkryptering stöds för VM scale sets i Linux, måste du lägga till en datadisk med hjälp av Resource Manager-mall. Uppdatera din mall för data disk etablera enligt följande:

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
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Här är motsvarande CLI-kommando. Ändra värdena i en declare-instruktioner till lämpliga värden. CLI har stöd för alla andra parametrar som har stöd för det föregående PowerShell-kommandot.

```azurecli
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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montera en datadisk till en Linux-instans
Innan du fortsätter med kryptering på en skalningsuppsättning för virtuell dator, se till att datadisken har lagts till korrekt monterade. Logga in på virtuell dator för Linux-kluster och kör den **LSBLK** kommando. Utdata bör visa data som har lagts till disken i den **monteringspunkt** kolumn.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Distribuera program till ett Service Fabric-kluster i Linux
Om du vill distribuera ett program i klustret, följer du stegen och vägledning vid [snabbstarten: Distribuera Linux-behållare till Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Aktivera diskkryptering för VM-skalningsuppsättningar som skapats tidigare
Anger du skapade via de föregående stegen, kör följande kommandon om du vill aktivera diskkryptering för VM-skalningsuppsättningen:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Verifiera om diskkryptering är aktiverat för en VM-skalningsuppsättning i Linux
Om du vill hämta status för en skalningsuppsättning för hela den virtuella datorn eller en instans i en skalningsuppsättning, kör du följande kommandon.
Dessutom kan du logga in på virtuell dator för Linux-kluster och köra den **LSBLK** kommando. Utdata bör visa datadisken har lagts till i den **monteringspunkt** kolumnen och **typ** kolumnen bör läsa *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Inaktivera disk encryption för en VM-skalningsuppsättning i Service Fabric-kluster
Inaktivera diskkryptering för en VM-skalningsuppsättning genom att köra följande kommandon. Observera att inaktiverar diskkryptering gäller skalningsuppsättningen hela den virtuella datorn och inte en enskild instans.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Du bör nu ha ett säkert kluster och vet hur du aktiverar och inaktiverar diskkryptering för Service Fabric-klusternoder och VM-skalningsuppsättningar. Liknande vägledning på noder i Service Fabric i Linux finns i [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
