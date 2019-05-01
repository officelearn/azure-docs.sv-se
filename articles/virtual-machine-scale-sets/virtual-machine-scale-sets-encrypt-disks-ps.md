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
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: 7ebb88317da45ff496385b72c603a44d628b0202
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869065"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Kryptera OS och anslutna datadiskar i en VM-skalningsuppsättning med Azure PowerShell

För att skydda och skydda data i vila med branschstandardiserad krypteringsteknik, stöd VM-skalningsuppsättningar för Azure Disk Encryption (ADE). Kryptering kan aktiveras för Windows och Linux VM-skalningsuppsättningar. Mer information finns i [Azure Disk Encryption för Windows och Linux](../security/azure-security-disk-encryption.md).

Azure-diskkryptering stöds:
- för att skala uppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterade) disk scale sets.
- för OS- och datavolymer i Windows-skalningsuppsättningar. Inaktivera kryptering stöds för OS- och datavolymer för Windows-skalningsuppsättningar.
- för datavolymer i Linux-skalningsuppsättningar. OS-diskkryptering stöds inte för närvarande för Linux-skalningsuppsättningar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

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

För att kryptera Virtuella datorinstanser i en skalningsuppsättning, först hämta en del information i Key Vault-URI och resurs-ID med [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Dessa variabler används för att starta krypteringsprocessen med [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

När du uppmanas, skriver *y* fortsätta disk krypteringsprocessen på skala ange VM-instanser.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med hjälp av KEK du omsluter nyckeln

Du kan också använda en krypteringsnyckel för ökad säkerhet när du krypterar virtuella datorns skalningsuppsättning.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng:</br>
/ subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

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

- I den här artikeln har använt du Azure PowerShell för att kryptera en VM-skalningsuppsättning. Du kan också använda den [Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md) eller mallar för [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Om du vill att Azure Disk Encryption tillämpas när ytterligare ett tillägg har etablerats kan du använda [ordningsföljd för](virtual-machine-scale-sets-extension-sequencing.md). Du kan använda [dessa exempel](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) att komma igång.
