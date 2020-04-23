---
title: Skapa och kryptera en virtuell Windows-dator med Azure PowerShell
description: I den här snabbstarten får du lära dig hur du använder Azure PowerShell för att skapa och kryptera en virtuell Windows-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 6f564a9a4f13136bbe7e28a3600ca71892c82439
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081599"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Snabbstart: Skapa och kryptera en virtuell Windows-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure PowerShell-modulen för att skapa en virtuell Dator (VM), skapa ett nyckelvalv för lagring av krypteringsnycklar och kryptera den virtuella datorn. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell [Azure-dator med New-AzVM](/powershell/module/az.compute/new-azvm). Du måste ange autentiseringsuppgifter till cmdleten. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa ett nyckelvalv som konfigurerats för krypteringsnycklar

Azure-diskkryptering lagrar sin krypteringsnyckel i ett Azure Key Vault. Skapa ett nyckelvalv med [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill aktivera nyckelvalvet för att lagra krypteringsnycklar använder du parametern -EnabledForDiskEncryption.

> [!Important]
> Varje Key Vault måste ha ett unikt namn. I följande exempel skapas ett nyckelvalv med namnet *myKV*, men du måste namnge ditt något annat.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension kräver vissa värden från key vault-objektet. Du kan hämta dessa värden genom att skicka det unika namnet på ditt nyckelvalv till [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Efter några minuter kommer processen att returnera följande:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Du kan verifiera krypteringsprocessen genom att köra [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

När kryptering är aktiverat visas följande i den returnerade utdata:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator, skapade ett Nyckelvalv som var aktiverat för krypteringsnycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)