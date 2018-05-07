---
title: Kryptera diskar för Azure skalningsuppsättningarna med Azure PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell för att kryptera VM-instanser och anslutna diskar i en skaluppsättning för virtuell dator i Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 91138ffad0fd906061e0b0ce5cdb251f402d3341
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Kryptera OS och anslutna diskar i en virtuell dator-skala med Azure PowerShell (förhandsgranskning)

Om du vill skydda och skydda data i viloläge med hjälp industry standard krypteringsteknik stöder skalningsuppsättningar i virtuella datorer Azure Disk Encryption (ADE). Kryptering kan aktiveras för Windows och Linux virtuella skala anger. Mer information finns i [Azure Disk Encryption för Windows och Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption för skalningsuppsättningar i virtuella datorer är för närvarande i förhandsversion, tillgänglig i alla offentliga Azure-regioner.

Azure disk encryption stöds:
- för att skala anger skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterad) disk skaluppsättningar.
- för OS- och datavolymer i skalningsuppsättningar i Windows. Inaktivera kryptering stöds av Operativsystemet och datavolymer för skalningsuppsättningar i Windows.
- för datavolymer i skalningsuppsättningar i Linux. OS-diskkryptering stöds inte i den aktuella förhandsvisningen för skalningsuppsättningar i Linux.

Ange VM avbildningsåterställning och uppgradering skalningsåtgärder stöds inte i den aktuella förhandsvisningen. Azure-diskkryptering för virtuella skala anger preview rekommenderas endast i testmiljöer. I förhandsgranskningen gör inte diskkryptering i produktionsmiljöer där du kan behöva uppgradera en OS-avbildning i en skaluppsättning för krypterade.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kursen krävs av Azure PowerShell Modulversion 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="register-for-disk-encryption-preview"></a>Registrera dig för förhandsgranskning för kryptering av disk

Azure disk encryption för den virtuella datorn anger preview måste du registrera prenumerationen med [registrera AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Du behöver bara utför följande steg första gången du använder funktionen disk encryption förhandsgranskning:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Det kan ta upp till 10 minuter för att sprida registrering förfrågan. Du kan kontrollera registreringstillstånd med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). När den `RegistrationState` rapporter *registrerade*, registrera den *Mirosoft.Compute* provider med [registrera AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure Key Vault som aktiverats för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning.

Skapa ett Nyckelvalv med [nya AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Om du vill tillåta Nyckelvalvet ska användas för kryptering, ange den *EnabledForDiskEncryption* parameter. I följande exempel definieras även variabler resursgruppens namn, Nyckelvalvets namn och plats. Ange ditt eget unikt namn för Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget är bara obligatoriskt om du har en befintlig Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett Nyckelvalv i föregående avsnitt.

Du kan aktivera en befintliga Nyckelvalvet i samma prenumeration och region som skalan för diskkryptering med [Set AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Ange namnet på din befintliga Nyckelvalvet i den *$vaultName* variabeln på följande sätt:

```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Först anger du ett administratörsanvändarnamn och lösenord för virtuella datorer med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa nu en skalningsuppsättning för en virtuell dator med [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en belastningsutjämnare. Belastningsutjämnaren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzureRmVmss `
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

Om du vill kryptera VM-instanser i en skaluppsättning först hämta viss information på Key Vault URI och resurs-ID med [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Dessa variabler används för att starta krypteringsprocessen med [Set AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

När du uppmanas ange *y* fortsätta krypteringsprocessen disk på skalan ange VM-instanser.

## <a name="check-encryption-progress"></a>Kontrollera kryptering pågår

Om du vill kontrollera statusen för diskkryptering [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```azurepowershell-interactive
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

När VM-instanser som är krypterade, den *EncryptionSummary* code rapporter *ProvisioningState/lyckades* som visas i följande exempel utdata:

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

Om du inte längre vill använda krypterade VM-instanser diskar kan du inaktivera kryptering med [inaktivera AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption) på följande sätt:

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln används Azure PowerShell för att kryptera en skaluppsättning för virtuell dator. Du kan också använda den [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) eller mallar för [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
