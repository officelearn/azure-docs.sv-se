---
title: Skapa och kryptera en Linux VM med Azure Powershell
description: I den här snabbstarten får du lära dig hur du använder Azure Powershell för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 7c44ffefccd4d78713d29aa2f9ab6006b8ba819e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081485"
---
# <a name="quickstart-create-and-encrypt-a-linux-virtual-machine-in-azure-with-powershell"></a>Snabbstart: Skapa och kryptera en Linux-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure PowerShell-modulen för att skapa en Linux-dator (VM), skapa ett Key Vault för lagring av krypteringsnycklar och kryptera den virtuella datorn. Den här snabbstarten använder Ubuntu 16.04 LTS marketplace-avbildning från Canonical och en Standard_D2S_V3 för VM-storlek. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en Azure-dator med [New-AzVM](/powershell/module/az.compute/new-azvm), skicka till den VM-konfigurationen objekt du skapade ovan.

```powershell
$securePassword = ConvertTo-SecureString 'AZUREuserPA$$W0RD' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest  -Size Standard_D2S_V3
```

Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa ett Nyckelvalv som konfigurerats för krypteringsnycklar

Azure-diskkryptering lagrar dess krypteringsnyckeln i ett Azure Key Vault. Skapa ett Nyckelvalv med [nya AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Använd parametern - EnabledForDiskEncryption om du vill aktivera Key Vault för att lagra krypteringsnycklar.

> [!Important]
> För varje Key Vault måste ha ett unikt namn. I följande exempel skapas ett Key Vault med namnet *myKV*, men du måste namnge din något annat.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension kräver vissa värden från Key Vault-objekt. Du kan hämta dessa värden genom att skicka det unika namnet på ditt nyckelvalv och [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Efter ett par minuter returnerar processen följande:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Du kan kontrollera krypteringsprocessen genom att köra [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

När kryptering är aktiverat visas följande i den returnerade utdatan:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator och skapat ett Nyckelvalv som har aktivera för krypteringsnycklar och krypteras den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Krav för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)