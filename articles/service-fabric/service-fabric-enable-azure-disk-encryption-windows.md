---
title: Aktivera diskkryptering för Azure Service Fabric Windows-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar diskkryptering för Service Fabric-klusternoder i Azure med hjälp av Azure Resource Manager, Azure Key Vault.
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
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050450"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Aktivera diskkryptering för service fabric Windows-klusternoder 
> [!div class="op_single_selector"]
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Följ stegen nedan för att aktivera diskkryptering på Service Fabric Windows-klusternoder. Du måste göra detta för varje nod-skalningsuppsättningar för typer/virtuella datorer. Vi ska använda Azure Disk Encryption-funktionen på VM-skalningsuppsättningar för att kryptera noderna.

Guiden innehåller följande procedurer:

* Ange nyckel-koncept som du behöver känna av att aktivera diskkryptering på VM-skalningsuppsättning i Service Fabric Windows-kluster.
* Förutsättningar steg ska följas innan du aktiverar diskkryptering på Service Fabric-kluster i Windows virtual machine scale Sets.
* Ange stegen för att om du vill aktivera diskkryptering på VM-skalningsuppsättning i Service Fabric Windows-kluster.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Självregistrering** – om du vill använda, kryptering förhandsversionen för VM scale set disk kräver självregistrering
* Du kan registrera din prenumeration genom att köra följande steg: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Vänta ungefär 10 minuter tills status som ”Registered”. Du kan kontrollera status genom att köra följande kommando: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** -skapa ett KeyVault i samma prenumeration och region som skalan och åtkomstprincip 'EnabledForDiskEncryption' i Nyckelvalvet med dess PS-cmdlet. Du kan också ange principen med KeyVault UI i Azure-portalen: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installera senaste [Azure CLI](/cli/azure/install-azure-cli) , som innehåller de nya kommandona för kryptering.
* Installera den senaste versionen av [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är VM-skalningsuppsättningen ADE-cmdletar för att aktivera ([ange](/powershell/module/az.compute/set-azvmssdiskencryptionextension))-kryptering, hämta ([hämta](/powershell/module/az.compute/get-azvmssvmdiskencryption)) krypteringsstatus och ta bort ([inaktivera](/powershell/module/az.compute/disable-azvmssdiskencryption)) kryptering på skalningsuppsättningen instans.

| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 eller senare | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* VM scale set kryptering stöds endast för skalningsuppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterade) disk scale sets.
* VM scale set kryptering har stöd för OS- och datavolymer för Windows virtual machine scale Sets. Inaktivera kryptering stöds för OS- och datavolymer för Windows-skalningsuppsättning.
* Återställa VM-avbildning för VM-skalningsuppsättningen och åtgärder för uppgradering stöds inte i aktuella förhandsvisningen.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Skapa nya kluster och aktivera diskkryptering

Använd följande kommandon för att skapa kluster och aktivera diskkryptering med hjälp av Azure Resource Manager-mall & självsignerat certifikat.

### <a name="sign-in-to-azure"></a>Logga in på Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [azure service fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar till [anpassa klustermallen] [ customize-your-cluster-template] nedan.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Distribuera program till Windows Service Fabric-kluster
Följ stegen och den hjälp [distribuera programmet till ditt kluster](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Aktivera diskkryptering för Service Fabric-kluster virtual machine scale Sets skapade ovan
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Verifiera om diskkryptering aktiverat för Windows VM-skalningsuppsättning.
Hämta status för en skalningsuppsättning för hela den virtuella datorn eller en instans i skalningsuppsättningen. Se nedanstående kommandon.
Dessutom kan användaren kan logga in på virtuell dator i skalningsuppsättningen och se till att enheter krypteras

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Inaktivera disk encryption för Service Fabric-kluster virtual machine scale Sets 
Inaktivera diskkryptering gäller för hela virtual machine scale Sets och inte av instans 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster med hur du aktiverar/inaktiverar diskkryptering för Service Fabric-kluster virtual machine scale Sets. Nästa [diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
