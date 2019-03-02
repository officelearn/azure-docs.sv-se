---
title: Kryptera diskar på en Windows-dator i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en virtuell Windows-dator för förbättrad säkerhet med hjälp av Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: cc0eee9dc36878f7a02b97453c859d94ea99b901
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217146"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Kryptera virtuella diskar på en virtuell Windows-dator
För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar i Azure krypteras. Diskar krypteras med hjälp av kryptografiska nycklar som skyddas i ett Azure Key Vault. Du kontrollerar dessa kryptografiska nycklar och kan granska deras användning. Den här artikeln beskriver hur du kryptera virtuella diskar på en virtuell Windows-dator med hjälp av Azure PowerShell. Du kan också [kryptera en Linux VM med hjälp av Azure CLI](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på Windows virtuella datorer krypteras i vila med hjälp av BitLocker. Det är kostnadsfritt för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i ett Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning. 

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i ett Azure Key Vault.
1. Konfigurera den kryptografiska nyckeln kan användas för att kryptera diskar.
1. Aktivera diskkryptering för din virtuella diskar.
1. De obligatoriska kryptografiska nycklarna begärs från Azure Key Vault.
1. Virtuella diskar krypteras med hjälp av den angivna kryptografiska nyckeln.


## <a name="requirements-and-limitations"></a>Krav och begränsningar

Krav för diskkryptering och scenarier som stöds:

* Aktiverar kryptering på den nya virtuella Windows-datorer från Azure Marketplace-avbildningar eller anpassade VHD-avbildningar.
* Aktiverar kryptering på den befintliga virtuella Windows-datorer i Azure.
* Aktivera kryptering på Windows virtuella datorer som konfigureras med hjälp av lagringsutrymmen.
* Inaktivera kryptering på operativsystem och enheter för Windows-datorer.
* Standard-nivån virtuella datorer, till exempel A, D, DS, G och GS-serien virtuella datorer.

    > [!NOTE]
    > Alla resurser (inklusive Key Vault, lagringskontot och virtuella datorer) måste finnas i samma Azure-region och prenumeration.

Diskkryptering stöds inte för närvarande i följande scenarier:

* Basic-nivån virtuella datorer.
* Virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen.
* Uppdaterar de kryptografiska nycklarna på en redan krypterad virtuell dator.
* Integrering med lokala nyckelhanteringstjänst.


## <a name="create-an-azure-key-vault-and-keys"></a>Skapa ett Azure Key Vault och nycklar
Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell-modulen har installerats. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). I följande kommandoexempel ersätter alla exempel parametrar med namn, plats och nyckelvärden, till exempel *myResourceGroup*, *Mittkeyvault*, *myVM*, och så vidare.

Det första steget är att skapa ett Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vaults kan lagra nycklar, hemligheter eller lösenord så att du kan implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering, ska du skapa ett Nyckelvalv för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar. 

Aktivera Azure Key Vault-providern i Azure-prenumerationen med [registrera AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), skapa en resursgrupp med [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *USA, östra* plats:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Azure-Nyckelvalv som kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste vara i samma region. Skapa ett Azure Key Vault med [New AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) och aktivera Key Vault för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyVaultName* på följande sätt:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara Security Model (HSM)-skydd.  Ett standard Key Vault lagrar bara programvaruskyddade nycklar. Använda en HSM kräver en premium Key Vault en extra kostnad. När du skapar en premium Key Vault i föregående steg till den *- Sku ”Premium”* parametern. I följande exempel används programvaruskyddade nycklar eftersom vi har skapat ett Nyckelvalv som standard. 

För båda modellerna för skydd måste Azure-plattformen beviljas åtkomst för att begära krypteringsnycklarna när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en krypteringsnyckel i Key Vault med [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). I följande exempel skapas en nyckel med namnet *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Testa krypteringsprocessen genom att skapa en virtuell dator med [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Windows Server 2016 Datacenter* bild. När du tillfrågas om autentiseringsuppgifter, anger du användarnamnet och lösenordet som ska användas för den virtuella datorn:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Kryptera en virtuell dator
Kryptera din virtuella dator med [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) med hjälp av Azure Key Vault-nyckeln. I följande exempel hämtar all viktig information och sedan krypterar den virtuella datorn med namnet *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Acceptera uppmaningen att fortsätta med VM-kryptering. Den virtuella datorn startas om under processen. När krypteringsprocessen har slutförts och den virtuella datorn har startats om, granska krypteringsstatus med [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Utdata ser ut ungefär så här:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du hanterar ett Azure Key Vault finns i [ställa in ett Key Vault för virtuella datorer](key-vault-setup.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassad virtuell dator att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
