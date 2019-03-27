---
title: Aktivera diskkryptering för Azure Service Fabric Linux-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar diskkryptering för Service Fabric-kluster skalningsuppsättning i Azure med hjälp av Azure Resource Manager, Azure Key Vault.
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
ms.openlocfilehash: 3de26efb74b9349282d36beb94e8a2a269227fbf
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488423"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Aktivera diskkryptering för service fabric Linux-klusternoderna 
> [!div class="op_single_selector"]
> * [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Följ stegen nedan för att aktivera diskkryptering på Service Fabric Linux-klusternoderna. Du måste göra detta för varje nod-skalningsuppsättningar för typer/virtuella datorer. Vi ska använda Azure Disk Encryption-funktionen på VM-skalningsuppsättningar för att kryptera noderna.

Guiden innehåller följande procedurer:

* Ange nyckel-koncept som du behöver känna av att aktivera diskkryptering på VM-skalningsuppsättning i Service Fabric Linux-kluster.
* Förutsättningar steg ska följas innan du aktiverar diskkryptering på Service Fabric-kluster för Linux virtual machine scale Sets.
* Ange stegen för att om du vill aktivera diskkryptering på VM-skalningsuppsättning i Service Fabric Linux-kluster.


## <a name="prerequisites"></a>Förutsättningar

* **Självregistrering** – om du vill använda, kryptering förhandsversionen för VM scale set disk kräver självregistrering
* Du kan registrera din prenumeration genom att köra följande steg: 
```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Vänta ungefär 10 minuter tills status som ”Registered”. Du kan kontrollera status genom att köra följande kommando: 
```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** -skapa ett KeyVault i samma prenumeration och region som VM-skalningsuppsättningen och åtkomstprincip 'EnabledForDiskEncryption' i Nyckelvalvet med dess PS-cmdlet. Du kan också ange principen med KeyVault UI i Azure-portalen: 
```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installera senaste [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) , som innehåller de nya kommandona för kryptering.
* Installera den senaste versionen av [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är VM-skalningsuppsättningen ADE-cmdletar för att aktivera ([ange](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1))-kryptering, hämta ([hämta](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) krypteringsstatus och ta bort ([inaktivera](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) kryptering på skala set-instans. 

| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 eller senare | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 eller senare | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 eller senare | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* VM scale set kryptering stöds endast för skalningsuppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterade) disk scale sets.
* VM scale set kryptering stöds för datavolymen för Linux VM-skalningsuppsättning. OS-diskkryptering stöds inte i den aktuella förhandsversionen för Linux.
* Återställa VM-avbildning för VM-skalningsuppsättningen och åtgärder för uppgradering stöds inte i aktuella förhandsvisningen.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Skapa nya Linux-kluster och aktivera diskkryptering

Använd följande kommandon för att skapa kluster och aktivera diskkryptering med hjälp av Azure Resource Manager-mall & självsignerat certifikat.

### <a name="sign-in-to-azure"></a>Logga in på Azure  

```powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [azure service fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) för Linux-kluster. 

Om du redan har en anpassad mall och sedan se till att är du dubbelkolla att alla tre certifikatrelaterade parametrar i mallen och parameterfilen namnges enligt följande och värden null på följande sätt.

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

Sedan för Linux virtual machine scale Sets – endast data diskkryptering stöds så vi måste lägga till datadisk med hjälp av Azure Resource Manager-mall. Uppdatera din mall för data disk etablera enligt nedan:

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


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Här är det motsvarande CLI-kommandot för att göra samma sak. Ändra värdena i en declare-instruktioner till lämpliga värden. CLI har stöd för alla andra parametrar som har stöd för ovanstående powershell-kommando.

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

#### <a name="linux-data-disk-mounting"></a>Linux data disk montering
Innan vi fortsätter med kryptering på Linux VM-skalningsuppsättning som vi behöver kontrollera har lagts till datadisk är korrekt monterade eller inte. Logga in på virtuella Linux-kluster-datorn och kör LSBLK kommando. Utdata ska visa den extra datadisk på monteringspunkten punkt kolumn.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Distribuera program till Linux Service Fabric-kluster
Följ stegen och den hjälp [distribuera programmet till ditt kluster](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Aktivera diskkryptering för Service Fabric-kluster för Linux virtual machine scale Sets skapade ovan
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Verifiera om diskkryptering aktiverat för Linux VM-skalningsuppsättning.
Hämta status för en skalningsuppsättning för hela den virtuella datorn eller en instans av virtuell dator i skalningsuppsättningen. Se nedanstående kommandon.
Dessutom kan användare logga in på virtuella Linux-kluster-datorn och kör LSBLK kommando. Utdata ska visa den extra datadisk på monteringspunkten punkt kolumnerna och typen Crypt för extra datadisk.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Inaktivera disk encryption för Service Fabric-kluster virtual machine scale Sets 
Inaktivera diskkryptering gäller för hela virtual machine scale Sets och inte av instans 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster med hur du aktiverar/inaktiverar diskkryptering för Service Fabric-kluster för Linux virtual machine scale Sets. Nästa [diskkryptering för Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
