---
title: Skapa och kryptera en virtuell Linux-dator med Azure Powershell
description: I den här snabbstarten får du lära dig hur du använder Azure Powershell för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970537"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Snabbstart: Skapa och kryptera en Virtuell Linux-dator i Azure med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure PowerShell-modulen för att skapa en virtuell Linux-dator (VM), skapa ett nyckelvalv för lagring av krypteringsnycklar och kryptera den virtuella datorn. Den här snabbstarten använder Ubuntu 16.04 LTS marketplace-avbildningen från Canonical och en vm-Standard_D2S_V3 storlek. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell [Azure-dator med New-AzVM](/powershell/module/az.compute/new-azvm)och skicka till det det vm-konfigurationsobjekt som du skapade ovan.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa ett nyckelvalv som konfigurerats för krypteringsnycklar

Azure-diskkryptering lagrar sin krypteringsnyckel i ett Azure Key Vault. Skapa ett nyckelvalv med [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill aktivera nyckelvalvet för att lagra krypteringsnycklar använder du parametern -EnabledForDiskEncryption.

> [!Important]
> Varje nyckelvalv måste ha ett namn som är unikt i Azure. I exemplen nedan ersätter du <ditt unika nyckelval-namn> med det namn du väljer.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension kräver vissa värden från key vault-objektet. Du kan hämta dessa värden genom att skicka det unika namnet på ditt nyckelvalv till [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Efter några minuter kommer processen att returnera följande:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Du kan verifiera krypteringsprocessen genom att köra [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

När kryptering är aktiverat visas följande i den returnerade utdata:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator, skapade ett Nyckelvalv som var aktiverat för krypteringsnycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)