---
title: Aktivera diskkryptering för Windows-kluster
description: I den här artikeln beskrivs hur du aktiverar diskkryptering för Azure Service Fabric-klusternoder med hjälp av Azure Key Vault i Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251824"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Aktivera diskkryptering för Azure Service Fabric-klusternoder i Windows 
> [!div class="op_single_selector"]
> * [Diskkryptering för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

I den här självstudien får du lära dig hur du aktiverar diskkryptering på klusternoder i Service Fabric i Windows. Du måste följa dessa steg för var och en av nodtyperna och skalningsuppsättningarna för virtuella datorer. För att kryptera noderna använder vi Azure Disk Encryption-funktionen på skalningsuppsättningar för virtuella datorer.

Guiden behandlar följande ämnen:

* Viktiga begrepp att vara medveten om när du aktiverar diskkryptering på Service Fabric-klusters virtuella datorskalauppsättningar i Windows.
* Steg som ska följas innan diskkryptering aktiveras på klusternoder för Service Fabric i Windows.
* Steg som ska följas för att aktivera diskkryptering på klusternoder för Service Fabric i Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

**Självregistrering** 

Förhandsgranskningen av diskkryptering för skalningsuppsättningen för virtuella datorer kräver självregistrering. Använd följande steg: 

1. Kör först följande kommando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Vänta ca 10 minuter tills statusen läser *Registrerad*. Du kan kontrollera status genom att köra följande kommando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Skapa ett nyckelvalv i samma prenumeration och region som skalningsuppsättningen och välj sedan åtkomstprincipen **AktiveradForDiskEncryption** i nyckelvalvet med hjälp av dess PowerShell-cmdlet. Du kan också ange principen med hjälp av key vault-användargränssnittet i Azure-portalen med följande kommando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli), som har de nya krypteringskommandona.
3. Installera den senaste versionen av [Azure SDK från Azure PowerShell-versionen.](https://github.com/Azure/azure-powershell/releases) Följande följer den virtuella datorn skala uppsättning Azure Disk Encryption cmdlets för att aktivera[(ange)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)kryptering, hämta[(hämta)](/powershell/module/az.compute/get-azvmssvmdiskencryption)krypteringsstatus och ta bort[(inaktivera)](/powershell/module/az.compute/disable-azvmssdiskencryption)kryptering på skalningsuppsättningsinstansen.

| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Inaktivera-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 eller senare | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för diskkryptering
* Kryptering för skalningsuppsättningar för virtuella datorer stöds endast för skalningsuppsättningar som skapats med hanterade diskar. Det stöds inte för inbyggda (eller ohantslagna) diskskaleuppsättningar.
* Kryptering stöds för operativsystem och datavolymer i skalningsuppsättningar för virtuella datorer i Windows. Inaktivera kryptering stöds också för operativsystem och datavolymer för skalningsuppsättningar för virtuella datorer i Windows.
* Ominmatning och uppgradering av virtuella datorer för skalningsuppsättningar för virtuella datorer stöds inte i den aktuella förhandsversionen.


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

### <a name="use-the-custom-template-that-you-already-have"></a>Använda den anpassade mallen som du redan har 

Om du behöver skapa en anpassad mall som passar dina behov rekommenderar vi starkt att du börjar med en av de mallar som är tillgängliga på [exempelsidan för azure service fabric-klusterskapandemall.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Information om hur du [anpassar avsnittet klustermall][customize-your-cluster-template] finns i följande anvisningar.

Om du redan har en anpassad mall dubbelkollar du att alla tre certifikatrelaterade parametrarna i mallen och parameterfilen har följande namn och att värdena är null enligt följande:

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
Om du vill distribuera ett program till klustret följer du stegen och vägledningen på [Distribuera och ta bort program med PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Aktivera diskkryptering för skalningsuppsättningar för virtuella datorer som skapats tidigare

Om du vill aktivera diskkryptering för de virtuella datorskaleuppsättningar som du har skapat genom föregående steg kör du följande kommandon:
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Verifiera om diskkryptering är aktiverat för en skala för virtuella datorer i Windows
Hämta status för en hel skalauppsättning för virtuella datorer eller en instans i en skalningsuppsättning genom att köra följande kommandon.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Dessutom kan du logga in på skalningsuppsättningen för den virtuella datorn och se till att enheterna är krypterade.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Inaktivera diskkryptering för en skala för virtuella datorer i ett Service Fabric-kluster 
Inaktivera diskkryptering för en skala för virtuella datorer genom att köra följande kommandon. Observera att inaktivera diskkryptering gäller för hela den virtuella datorns skalningsuppsättning och inte en enskild instans.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
Nu bör du ha ett säkert kluster och veta hur du aktiverar och inaktiverar diskkryptering för klusternoder för Service Fabric och skalningsuppsättningar för virtuella datorer. Liknande vägledning om klusternoder för Service Fabric i Linux finns i [Diskkryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
