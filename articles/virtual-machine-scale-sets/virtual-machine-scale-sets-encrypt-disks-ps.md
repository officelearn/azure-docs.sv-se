---
title: "Skaluppsättningar för virtuell dator i Azure kryptera diskar | Microsoft Docs"
description: "Lär dig hur du krypterar anslutna diskar i skalningsuppsättningar i virtuella datorer."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Kryptera OS och anslutna diskar i en skaluppsättning för virtuell dator
Azure [skalningsuppsättningar i virtuella](/azure/virtual-machine-scale-sets/) har stöd för Azure disk encryption (ADE).  Azure disk encryption kan aktiveras för Windows och Linux virtuella datorn anger att skydda och skydda skalan anger data vilande med industry standard krypteringsteknik. Mer information finns i Azure Disk Encryption för Windows och Linux-datorer.

> [!NOTE]
>  Azure disk encryption för skalningsuppsättningar i virtuella datorer är för närvarande i förhandsversion, tillgänglig i alla offentliga Azure-regioner.

Azure disk encryption stöds:
- för att skala anger skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterad) disk skaluppsättningar.
- för OS- och datavolymer i skalningsuppsättningar i Windows. Inaktivera kryptering stöds av Operativsystemet och datavolymer för skalningsuppsättningar i Windows.
- för datavolymer i skalningsuppsättningar i Linux. OS-diskkryptering stöds inte i den aktuella förhandsvisningen för skalningsuppsättningar i Linux.

Ange VM avbildningsåterställning och uppgradering skalningsåtgärder stöds inte i den aktuella förhandsvisningen. Azure-diskkryptering för virtuella skala anger preview rekommenderas endast i testmiljöer. I förhandsgranskningen gör inte diskkryptering i produktionsmiljöer där du kan behöva uppgradera en OS-avbildning i en skaluppsättning för krypterade.

## <a name="prerequisites"></a>Förutsättningar
Installera de senaste versionerna av [Azure Powershell](https://github.com/Azure/azure-powershell/releases), som innehåller kommandon för kryptering.

Azure-diskkryptering för virtuella skala anger preview måste du registrera prenumerationen med följande PowerShell-kommandon: 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Vänta i cirka 10 minuter tills tillståndet 'Registrerade' returneras av följande kommando: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure nyckelvalv aktiverad för diskkryptering av
Skapa ett nytt nyckelvalv i samma prenumeration och region som skala och åtkomstprincipen 'EnabledForDiskEncryption'.

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

Eller aktivera en befintlig nyckelvalvet i samma prenumeration och region som skalan för diskkryptering.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Aktivera kryptering
Följande kommandon för att kryptera en datadisk i en körs skala som anges med ett nyckelvalv i samma resursgrupp. Du kan också använda mallar för att kryptera diskar i en löpande [Windows skaluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux skaluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Kontrollera kryptering pågår
Använd följande kommandon om du vill visa krypteringsstatus för skaluppsättning.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Inaktivera kryptering
Inaktivera kryptering på en aktiv virtuell dator skala anges med följande kommandon. Du kan också använda mallar för att inaktivera kryptering körs [Windows skaluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) eller [Linux skaluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux).

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
