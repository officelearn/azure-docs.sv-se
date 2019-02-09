---
title: Kryptera diskar för Azure skalningsuppsättningar med Azure PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell för att kryptera VM-instanser och anslutna diskar i en Windows VM-skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: cc1405d2dd972aff6091a9d5b60ff9da18185286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978110"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Kryptera OS och anslutna datadiskar i en VM-skalningsuppsättning med Azure PowerShell (förhandsversion)

För att skydda och skydda data i vila med branschstandardiserad krypteringsteknik, stöd VM-skalningsuppsättningar för Azure Disk Encryption (ADE). Kryptering kan aktiveras för Windows och Linux VM-skalningsuppsättningar. Mer information finns i [Azure Disk Encryption för Windows och Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption för VM-skalningsuppsättningar är för närvarande i offentlig förhandsversion, som är tillgängliga i alla offentliga Azure-regioner.

Azure-diskkryptering stöds:
- för att skala uppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterade) disk scale sets.
- för OS- och datavolymer i Windows-skalningsuppsättningar. Inaktivera kryptering stöds för OS- och datavolymer för Windows-skalningsuppsättningar.
- för datavolymer i Linux-skalningsuppsättningar. OS-diskkryptering stöds inte i den aktuella förhandsversionen för Linux-skalningsuppsättningar.

Scale set VM åtgärderna reimage och uppgraderingen åtgärder stöds inte i den aktuella förhandsversionen. Azure-diskkryptering för förhandsversionen av virtual machine scale sets rekommenderas endast i testmiljöer. I förhandsversionen av att inte aktivera diskkryptering i produktionsmiljöer där du kan behöva uppgradera en OS-avbildning i en krypterad skalningsuppsättning.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="register-for-disk-encryption-preview"></a>Registrera dig för förhandsversion för kryptering av disk

Azure-diskkryptering för VM-skalningsuppsättningar förhandsversion måste du registrera din prenumeration med [registrera AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature). Du behöver bara utföra följande steg första gången du använder förhandsgranskningsfunktionen disk kryptering:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```


Det kan ta upp till 10 minuter för registreringsbegäran att spridas. Du kan kontrollera status för enhetsregistreringen med [Get-AzProviderFeature](/powershell/module/az.resources/Get-AzProviderFeature). När den `RegistrationState` rapporter *registrerad*, registrera den *Microsoft.Compute* provider med [registrera AzResourceProvider](/powershell/module/az.resources/Register-AzResourceProvider):


```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure Key Vault-aktiverat för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord så att du kan implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Skapa ett Nyckelvalv med [nya AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill tillåta Nyckelvalvet som ska användas för diskkryptering, ange den *EnabledForDiskEncryption* parametern. I följande exempel definierar också variablerna för resursgruppens namn, Key Vault-namn och plats. Ange ditt eget unika Key Vault-namn:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget är bara obligatoriskt om du har ett befintligt Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett Nyckelvalv i föregående avsnitt.

Du kan aktivera en befintlig Key Vault i samma prenumeration och region som skalningsuppsättningen för diskkryptering med [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Ange namnet på ditt befintliga Nyckelvalv i den *$vaultName* variabeln enligt följande:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Först anger du ett administratörsanvändarnamn och lösenord för virtuella datorer med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu skapa en VM-skalningsuppsättning med [New AzVmss](/powershell/module/az.compute/new-azvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Aktivera kryptering

För att kryptera Virtuella datorinstanser i en skalningsuppsättning, först hämta en del information i Key Vault-URI och resurs-ID med [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Dessa variabler används för att starta krypteringsprocessen med [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

När du uppmanas, skriver *y* fortsätta disk krypteringsprocessen på skala ange VM-instanser.

## <a name="check-encryption-progress"></a>Kontrollera förloppet för kryptering

Du kan kontrollera statusen för diskkryptering genom att använda [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

När VM-instanser är krypterad, den *EncryptionSummary* code rapporter *ProvisioningState/lyckades* som visas i följande Exempelutdata:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Inaktivera kryptering

Om du inte längre vill använda krypterade diskar för VM-instanser kan du inaktivera kryptering med [inaktivera AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) på följande sätt:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har använt du Azure PowerShell för att kryptera en VM-skalningsuppsättning. Du kan också använda den [Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md) eller mallar för [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
