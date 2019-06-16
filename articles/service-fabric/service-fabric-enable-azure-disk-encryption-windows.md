---
title: Aktivera diskkryptering för Azure Service Fabric Windows-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar diskkryptering för Azure Service Fabric-klusternoder med hjälp av Azure Key Vault i Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471408"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Aktivera diskkryptering för Azure Service Fabric-klusternoder i Windows 
> [!div class="op_single_selector"]
> * [Disk Encryption för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

I de här självstudierna lär du dig att aktivera diskkryptering på Service Fabric-klusternoder i Windows. Du måste följa dessa steg för varje nodtyper och VM-skalningsuppsättningar. För att kryptera noderna, använder vi Azure Disk Encryption-funktionen på VM-skalningsuppsättningar.

Guiden innehåller följande avsnitt:

* Viktiga begrepp att känna till när aktiverar diskkryptering på VM-skalningsuppsättning i Service Fabric-kluster anger i Windows.
* Steg för att följas innan du aktiverar diskkryptering på Service Fabric klusternoderna i Windows.
* Steg för att om du vill aktivera diskkryptering på Service Fabric-klusternoder i Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

**Självregistrering** 

Självregistrering kräver att förhandsversionen av disk-kryptering för virtuella datorns skalningsuppsättning. Använd följande steg: 

1. Först kör du följande kommando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Vänta ungefär 10 minuter förrän statusen har ändrats *registrerad*. Du kan kontrollera statusen genom att köra följande kommando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Skapa ett nyckelvalv i samma prenumeration och region som skalningsuppsättningen och välj sedan den **EnabledForDiskEncryption** åtkomstprincipen i nyckelvalvet med hjälp av dess PowerShell-cmdlet. Du kan också ange principen med hjälp av Key Vault-Användargränssnittet i Azure-portalen med följande kommando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installera den senaste versionen av den [Azure CLI](/cli/azure/install-azure-cli), som innehåller de nya kommandona för kryptering.
3. Installera den senaste versionen av den [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) versionen. Följande är VM-skalningsuppsättningen Azure Disk Encryption-cmdletar för att aktivera ([ange](/powershell/module/az.compute/set-azvmssdiskencryptionextension))-kryptering, hämta ([hämta](/powershell/module/az.compute/get-azvmssvmdiskencryption)) krypteringsstatus och ta bort ([inaktivera](/powershell/module/az.compute/disable-azvmssdiskencryption)) kryptering på skalan skalningsuppsättningsinstans.

| Kommando | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 eller senare | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* Kryptering för VM-skalningsuppsättningar stöds endast för skalningsuppsättningar som skapats med hanterade diskar. Det finns inte stöd för internt (eller ohanterade) disk scale sets.
* Kryptering stöds för Operativsystemet och datavolymer i VM-skalningsuppsättning som anger i Windows. Inaktivera kryptering stöds också för Operativsystemet och datavolymer för VM-skalningsuppsättning anger i Windows.
* Virtuell dator åtgärderna reimage och uppgraderingen åtgärder för VM-skalningsuppsättningar stöds inte i den aktuella förhandsversionen.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Skapa ett nytt kluster och aktivera diskkryptering

Använd följande kommandon för att skapa ett kluster och aktivera diskkryptering med hjälp av en Azure Resource Manager-mall och ett självsignerat certifikat.

### <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in med följande kommandon:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du behöver skapa en anpassad mall så att den passar dina behov, rekommenderar vi att du börjar med en av de mallar som är tillgängliga på den [mallexempel för Azure Service Fabric-kluster skapas](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) sidan. Att [anpassa klustermallen] [ customize-your-cluster-template] avsnittet, se följande riktlinjer.

Om du redan har en anpassad mall kan du kontrollera att alla tre certifikatrelaterade parametrar i mallen och parameterfilen namnges enligt följande och att värden är null på följande sätt:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Distribuera ett program till ett Service Fabric-kluster i Windows
Om du vill distribuera ett program i klustret, följer du stegen och vägledning vid [distribuera och ta bort program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Verifiera om diskkryptering är aktiverat för en VM-skalningsuppsättning i Windows
Hämta status för en skalningsuppsättning för hela den virtuella datorn eller en instans i en skalningsuppsättning genom att köra följande kommandon.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Dessutom kan du logga in på virtuella datorns skalningsuppsättning och kontrollera att enheterna som är krypterade.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Inaktivera disk encryption för en VM-skalningsuppsättning i Service Fabric-kluster 
Inaktivera diskkryptering för en VM-skalningsuppsättning genom att köra följande kommandon. Observera att inaktiverar diskkryptering gäller skalningsuppsättningen hela den virtuella datorn och inte en enskild instans.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Du bör nu ha ett säkert kluster och vet hur du aktiverar och inaktiverar diskkryptering för Service Fabric-klusternoder och VM-skalningsuppsättningar. Liknande vägledning på noder i Service Fabric i Linux finns i [diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
