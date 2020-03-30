---
title: Kryptera diskar på en Windows VM i Azure
description: Kryptera virtuella diskar på en Virtuell Windows-dator för ökad säkerhet med hjälp av Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033533"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Kryptera virtuella diskar på en Virtuell Windows-dator
För förbättrad säkerhet och efterlevnad för virtuella datorer kan virtuella diskar i Azure krypteras. Diskar krypteras med hjälp av kryptografiska nycklar som är säkrade i ett Azure Key Vault. Du styr dessa kryptografiska nycklar och kan granska deras användning. I den här artikeln beskrivs hur du krypterar virtuella diskar på en Virtuell Windows-dator med hjälp av Azure PowerShell. Du kan också [kryptera Virtuella Linux-datorer.](../linux/disk-encryption-overview.md)

 

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Windows-datorer krypteras i vila med BitLocker. Det finns ingen avgift för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i ett Azure Key Vault med hjälp av programvaruskydd, eller så kan du importera eller generera dina nycklar i HSM (Hardware Security Modules) som är certifierade enligt FIPS 140-2-standarder. Kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. 

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i ett Azure Key Vault.
1. Konfigurera den kryptografiska nyckeln så att den kan användas för kryptering av diskar.
1. Aktivera diskkryptering för virtuella diskar.
1. De kryptografiska nycklarna som krävs begärs från Azure Key Vault.
1. De virtuella diskarna krypteras med den medföljande kryptografiska nyckeln.


## <a name="requirements-and-limitations"></a>Krav och begränsningar

Scenarier och krav som stöds för diskkryptering:

* Aktivera kryptering på nya Virtuella Windows-datorer från Azure Marketplace-avbildningar eller anpassade VHD-avbildningar.
* Aktivera kryptering på befintliga Virtuella Windows-datorer i Azure.
* Aktivera kryptering på virtuella Windows-datorer som konfigureras med hjälp av lagringsutrymmen.
* Inaktivera kryptering på operativsystem och dataenheter för virtuella Windows-datorer.
* Virtuella datorer med standardnivå, till exempel virtuella datorer i A-, DS-, G- och GS-serien.

    > [!NOTE]
    > Alla resurser (inklusive Key Vault, Storage-konto och VIRTUELL) måste finnas i samma Azure-region och prenumeration.

Diskkryptering stöds för närvarande inte i följande scenarier:

* Virtuella datorer på grundläggande nivå.
* Virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen.
* Uppdaterar kryptografiska nycklar på en redan krypterad virtuell dator.
* Integration med lokal key management service.


## <a name="create-an-azure-key-vault-and-keys"></a>Skapa ett Azure Key Vault och nycklar
Innan du börjar kontrollerar du att den senaste versionen av Azure PowerShell-modulen har installerats. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). I följande kommandoexempel ersätter du alla exempelparametrar med dina egna namn, plats- och nyckelvärden, till exempel *myResourceGroup,* *myKeyVault*, *myVM*och så vidare.

Det första steget är att skapa ett Azure Key Vault för att lagra dina kryptografiska nycklar. Azure Key Vaults kan lagra nycklar, hemligheter eller lösenord som gör att du kan implementera dem på ett säkert sätt i dina program och tjänster. För kryptering av virtuella diskar skapar du ett Nyckelvalv för att lagra en kryptografisk nyckel som används för att kryptera eller dekryptera dina virtuella diskar. 

Aktivera Azure Key Vault-providern i din [Azure-prenumeration med Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)och skapa sedan en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas ett resursgruppnamn *myResourceGroup* på platsen i *östra USA:*

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och själva den virtuella datorn måste alla vara i samma region. Skapa ett Azure Key Vault med [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) och aktivera Key Vault för användning med diskkryptering. Ange ett unikt Key Vault-namn för *keyVaultName* enligt följande:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Du kan lagra kryptografiska nycklar med hjälp av antingen program- eller maskinvarusäkerhetsmodell (HSM) skydd.  I ett standardvalv för Key Vault lagras endast programvaruskyddade nycklar. Att använda en HSM kräver ett förstklassigt Key Vault mot en extra kostnad. Om du vill skapa ett premiumnyckelvalv lägger du till parametern *-Sku "Premium"* i föregående steg. I följande exempel används programvaruskyddade nycklar sedan vi skapade ett standardnyckelvalv. 

För båda skyddsmodellerna måste Azure-plattformen beviljas åtkomst för att begära kryptografiska nycklar när den virtuella datorn startar för att dekryptera de virtuella diskarna. Skapa en kryptografisk nyckel i key vault med [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). I följande exempel skapas en nyckel med namnet *myKey:*

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Om du vill testa krypteringsprocessen skapar du en virtuell dator med [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Datacenteravbildning i Windows Server 2016.* När du uppmanas att ange användarnamn och lösenord som ska användas för den virtuella datorn:

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
Kryptera din virtuella dator med [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) med azure key vault-nyckeln. I följande exempel hämtas all nyckelinformation och krypterar sedan den virtuella datorn med namnet *myVM:*

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

Acceptera uppmaningen om att fortsätta med vm-krypteringen. Den virtuella datorn startas om under processen. När krypteringsprocessen är klar och den virtuella datorn har startats om, granska krypteringsstatus med [Get-AzVmDiskEncryptionStatus:](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)

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
* Mer information om hur du hanterar ett Azure Key Vault finns i [Konfigurera ett nyckelvalv för virtuella datorer](key-vault-setup.md).
* Mer information om diskkryptering, till exempel förbereda en krypterad anpassad virtuell dator för att ladda upp till Azure, finns i [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
