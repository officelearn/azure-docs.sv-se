---
title: Kryptera diskar på en virtuell Windows-dator i Azure
description: Kryptera virtuella diskar på en virtuell Windows-dator för ökad säkerhet med hjälp av Azure PowerShell
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033533"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Kryptera virtuella diskar på en virtuell Windows-dator
Virtuella diskar i Azure kan krypteras för förbättrad säkerhet och efterlevnad för virtuella datorer. Diskar krypteras med hjälp av kryptografiska nycklar som skyddas i en Azure Key Vault. Du styr dessa kryptografiska nycklar och kan granska deras användning. Den här artikeln beskriver hur du krypterar virtuella diskar på en virtuell Windows-dator med hjälp av Azure PowerShell. Du kan också [kryptera virtuella Linux-datorer](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Översikt över disk kryptering
Virtuella diskar på virtuella Windows-datorer är krypterade i vila med hjälp av BitLocker. Det kostar inget att kryptera virtuella diskar i Azure. Krypterings nycklar lagras i en Azure Key Vault med hjälp av program varu skydd, eller så kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Krypterings nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. 

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i en Azure Key Vault.
1. Konfigurera den kryptografiska nyckeln så att den kan användas för kryptering av diskar.
1. Aktivera disk kryptering för virtuella diskar.
1. De nödvändiga kryptografiska nycklarna begärs från Azure Key Vault.
1. De virtuella diskarna krypteras med den angivna kryptografiska nyckeln.


## <a name="requirements-and-limitations"></a>Krav och begränsningar

Scenarier som stöds och krav för disk kryptering:

* Aktivera kryptering på nya virtuella Windows-datorer från Azure Marketplace-avbildningar eller anpassade VHD-avbildningar.
* Aktivera kryptering på befintliga virtuella Windows-datorer i Azure.
* Aktivera kryptering på virtuella Windows-datorer som har kon figurer ATS med hjälp av lagrings utrymmen.
* Inaktivera kryptering på operativ system och data enheter för virtuella Windows-datorer.
* Virtuella datorer på standard nivå, till exempel virtuella datorer i D, DS, G och GS-serien.

    > [!NOTE]
    > Alla resurser (inklusive Key Vault, lagrings konto och virtuell dator) måste finnas i samma Azure-region och prenumeration.

Disk kryptering stöds inte för närvarande i följande scenarier:

* Virtuella datorer på Basic-nivå.
* Virtuella datorer som skapats med den klassiska distributions modellen.
* Uppdatera de kryptografiska nycklarna på en redan krypterad virtuell dator.
* Integrering med lokal nyckel hanterings tjänst.


## <a name="create-an-azure-key-vault-and-keys"></a>Skapa en Azure Key Vault och nycklar
Innan du börjar bör du kontrol lera att den senaste versionen av Azure PowerShell-modulen har installerats. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). I följande kommando exempel ersätter du alla exempel parametrar med dina egna namn, plats och nyckel värden, till exempel *myResourceGroup*, *mittkeyvault*, *myVM*och så vidare.

Det första steget är att skapa en Azure Key Vault för att lagra dina kryptografiska nycklar. Med Azure Key Vault kan du lagra nycklar, hemligheter eller lösen ord som gör det möjligt att på ett säkert sätt implementera dem i dina program och tjänster. För virtuell disk kryptering skapar du en Key Vault för att lagra en kryptografisk nyckel som används för att kryptera eller dekryptera dina virtuella diskar. 

Aktivera Azure Key Vault-providern i din Azure-prenumeration med [register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)och skapa sedan en resurs grupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen *USA, östra* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Azure Key Vault som innehåller de kryptografiska nycklarna och de associerade beräknings resurserna, till exempel lagring och den virtuella datorn, måste vara i samma region. Skapa en Azure Key Vault med [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) och aktivera Key Vault för användning med disk kryptering. Ange ett unikt Key Vault namn för *keyVaultName* enligt följande:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Du kan lagra kryptografiska nycklar med hjälp av HSM-skydd (Software eller Hardware Security Model).  En standard Key Vault lagrar endast program skyddade nycklar. Att använda en HSM kräver en Premium-Key Vault till ytterligare kostnad. Om du vill skapa en Premium-Key Vault lägger du till parametern *-SKU "Premium"* i föregående steg. I följande exempel används program skyddade nycklar eftersom vi skapade en standard Key Vault. 

För båda skydds modellerna måste Azure-plattformen beviljas åtkomst för att begära de kryptografiska nycklarna när den virtuella datorn startas för att dekryptera de virtuella diskarna. Skapa en kryptografisk nyckel i Key Vault med [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). I följande exempel skapas en nyckel med namnet *myKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Testa krypterings processen genom att skapa en virtuell dator med [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Windows Server 2016 Data Center* -avbildning. När du uppmanas att ange autentiseringsuppgifter anger du det användar namn och lösen ord som ska användas för din virtuella dator:

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
Kryptera den virtuella datorn med [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) med hjälp av Azure Key Vault nyckeln. I följande exempel hämtas all viktig information och krypterar sedan den virtuella datorn med namnet *myVM*:

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

Acceptera uppvarningen för att fortsätta med VM-krypteringen. Den virtuella datorn startas om under processen. När krypterings processen är klar och den virtuella datorn har startats om, granskar du krypterings statusen med [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

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
* Mer information om hur du hanterar en Azure Key Vault finns i [Konfigurera en Key Vault för virtuella datorer](key-vault-setup.md).
* Mer information om disk kryptering, till exempel förbereda en krypterad virtuell dator som ska överföras till Azure finns i [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
