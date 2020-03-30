---
title: Kryptera diskar för Azure-skalningsuppsättningar med Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att kryptera VM-instanser och anslutna diskar i en windows-skalningsuppsättning för virtuella datorer
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278990"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Kryptera operativsystem och anslutna datadiskar i en skalningsuppsättning för virtuella datorer med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript.  Den här artikeln visar hur du använder Azure PowerShell för att skapa och kryptera en skalningsuppsättning för virtuella datorer. Mer information om hur du använder Azure Disk Encryption på en skalningsuppsättning för virtuella datorer finns i [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure Key Vault aktiverat för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör att du kan implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med programvaruskydd, eller så kan du importera eller generera dina nycklar i HSM (Hardware Security Modules) som är certifierade enligt FIPS 140-2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Skapa ett nyckelvalv med [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill att nyckelvalvet ska kunna användas för diskkryptering ställer du in parametern *EnabledForDiskEncryption.* I följande exempel definieras också variabler för resursgruppnamn, Namn på Nyckelvalv och plats. Ange ditt eget unika Key Vault-namn:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Använda ett befintligt nyckelvalv

Det här steget krävs bara om du har ett befintligt Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett nyckelvalv i föregående avsnitt.

Du kan aktivera ett befintligt Nyckelvalv i samma prenumeration och region som skalningsuppsättningen för diskkryptering med [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definiera namnet på ditt befintliga Key Vault i *variabeln $vaultName* enligt följande:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Först anger du ett administratörsanvändarnamn och lösenord för virtuella datorer med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985:

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

Om du vill kryptera VM-instanser i en skalningsuppsättning får du först information om Key Vault URI och resurs-ID med [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Dessa variabler används för att sedan starta krypteringsprocessen med [Set-AzVmssDiskEncryptionExtension:](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

När du uppmanas att skriva skriver *du y* för att fortsätta diskkrypteringsprocessen på skalningsuppsättningen VM-instanser.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med KEK för att slå in nyckeln

Du kan också använda en nyckelkrypteringsnyckel för ökad säkerhet när du krypterar skaluppsättningen för den virtuella datorn.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n till KEK som i:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://

## <a name="check-encryption-progress"></a>Kontrollera krypteringsstatus

Om du vill kontrollera status för diskkryptering använder du [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

När VM-instanser krypteras rapporterar *Code-koden EncryptionSummary* *ProvisioningState/som* visas i följande exempelutdata:

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

Om du inte längre vill använda diskar med krypterade VM-instanser kan du inaktivera kryptering med [Inaktivera-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) enligt följande:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln använde du Azure PowerShell för att kryptera en skalningsuppsättning för virtuella datorer. Du kan också använda [Azure CLI-](disk-encryption-cli.md) eller [Azure Resource Manager-mallarna](disk-encryption-azure-resource-manager.md).
- Om du vill att Azure Disk Encryption ska tillämpas när ett annat tillägg har etablerats kan du använda [tilläggssekvensering](virtual-machine-scale-sets-extension-sequencing.md).
