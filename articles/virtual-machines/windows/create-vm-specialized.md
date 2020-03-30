---
title: Skapa en Virtuell Windows-dator från en specialiserad virtuell hårddisk i Azure
description: Skapa en ny Windows-virtuell dator genom att koppla en specialiserad hanterad disk som OS-disk med hjälp av resurshanterarens distributionsmodell.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: fc157c2253a718860e028fa493574cb9aa2ccdf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243372"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Skapa en virtuell Windows-dator från en särskild disk med PowerShell

Skapa en ny virtuell dator genom att koppla en specialiserad hanterad disk som OS-disk. En specialiserad disk är en kopia av en virtuell hårddisk (VHD) från en befintlig virtuell dator som innehåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

När du använder en specialiserad virtuell hårddisk för att skapa en ny virtuell dator behåller den nya virtuella datorn namnet på den ursprungliga virtuella datorn. Annan datorspecifik information sparas också och i vissa fall kan den här dubblettinformationen orsaka problem. När du kopierar en virtuell dator bör du vara medveten om vilka typer av datorspecifik information som programmen förlitar sig på.

I det syftet har du flera alternativ:
* [Använd en befintlig hanterad disk](#option-1-use-an-existing-disk). Det här alternativet är användbart om du har en virtuell dator som inte fungerar som den ska. Du kan ta bort den virtuella datorn och sedan återanvända den hanterade disken för att skapa en ny virtuell dator. 
* [Ladda upp en virtuell hårddisk](#option-2-upload-a-specialized-vhd) 
* [Kopiera en befintlig virtuell Azure-dator med hjälp av ögonblicksbilder](#option-3-copy-an-existing-azure-vm)

Du kan också använda Azure-portalen för att [skapa en ny virtuell dator från en specialiserad virtuell hårddisk](create-vm-specialized-portal.md).

Den här artikeln visar hur du använder hanterade diskar. Om du har en äldre distribution som kräver att du använder ett lagringskonto läser du [Skapa en virtuell dator från en specialiserad virtuell hårddisk i ett lagringskonto](sa-create-vm-specialized.md).

Vi rekommenderar att du begränsar antalet samtidiga distributioner till 20 virtuella datorer från en enda virtuell hårddisk eller ögonblicksbild. 

## <a name="option-1-use-an-existing-disk"></a>Alternativ 1: Använda en befintlig disk

Om du hade en virtuell dator som du har tagit bort och vill återanvända OS-disken för att skapa en ny virtuell dator använder du [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Du kan nu ansluta den här disken som OS-disk till en [ny virtuell dator](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Alternativ 2: Ladda upp en specialiserad virtuell hårddisk

Du kan ladda upp den virtuella hårddisken från en specialiserad virtuell dator som skapats med ett lokalt virtualiseringsverktyg, till exempel Hyper-V, eller en virtuell dator som exporteras från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Använd den virtuella hårddisken som den är för att skapa en ny virtuell dator. 
  
  * [Förbered en Virtuell Windows-hårddisk för att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Generalisera inte** den virtuella datorn med Sysprep.
  * Ta bort alla gästvirtualiseringsverktyg och agenter som är installerade på den virtuella datorn (till exempel VMware-verktyg).
  * Kontrollera att den virtuella datorn är konfigurerad för att hämta IP-adressen och DNS-inställningarna från DHCP. Detta säkerställer att servern skaffar en IP-adress i det virtuella nätverket när den startar. 


### <a name="upload-the-vhd"></a>Ladda upp den virtuella hårddisken

Du kan nu ladda upp en virtuell hårddisk direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hårddisk till Azure med Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Alternativ 3: Kopiera en befintlig Virtuell Azure-dator

Du kan skapa en kopia av en virtuell dator som använder hanterade diskar genom att ta en ögonblicksbild av den virtuella datorn och sedan genom att använda den ögonblicksbilden för att skapa en ny hanterad disk och en ny virtuell dator.

Om du vill kopiera en befintlig virtuell dator till en annan region kanske du vill använda askad för att [skapa en kopia av en disk i en annan region](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicksbild av OS-disken

Du kan ta en ögonblicksbild av en hel virtuell dator (inklusive alla diskar) eller bara en enda disk. Följande steg visar hur du tar en ögonblicksbild av bara OS-disken på din virtuella dator med [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet. 

Ställ först in några parametrar. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Hämta VM-objektet.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Hämta OS-diskens namn.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Skapa ögonblicksbildkonfigurationen. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Ta ögonblicksbilden.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Om du vill använda den här ögonblicksbilden för att `-AccountType Premium_LRS` skapa en virtuell dator som måste vara högpresterande lägger du till parametern i kommandot New-AzSnapshotConfig. Den här parametern skapar ögonblicksbilden så att den lagras som en Premium-hanterad disk. Premium Managed Disks är dyrare än Standard, så se till att du behöver Premium innan du använder den här parametern.

### <a name="create-a-new-disk-from-the-snapshot"></a>Skapa en ny disk från ögonblicksbilden

Skapa en hanterad disk från ögonblicksbilden med hjälp av [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). I det här exemplet används *myOSDisk* för disknamnet.

Skapa en ny resursgrupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ange os-diskens namn. 

```powershell
$osDiskName = 'myOsDisk'
```

Skapa den hanterade disken.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Skapa den nya virtuella datorn 

Skapa nätverk och andra vm-resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-virtual-network"></a>Skapa undernätet och det virtuella nätverket

Skapa det [virtuella nätverket](../../virtual-network/virtual-networks-overview.md) och undernätet för den virtuella datorn.

1. Skapa undernätet. I det här exemplet skapas ett undernät med namnet *mySubNet*i resursgruppen *myDestinationResourceGroup*och undernätsadressprefixet anges *till 10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Skapa det virtuella nätverket. I det här exemplet anges det virtuella nätverksnamnet på *myVnetName*, platsen till *västra USA*och adressprefixet för det virtuella nätverket till *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en RDP-regel
För att kunna logga in på den virtuella datorn med fjärrskrivbordsprotokoll (RDP) måste du ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389. I vårt exempel skapades DEN virtuella hårddisken för den nya virtuella datorn från en befintlig specialiserad virtuell dator, så att du kan använda ett konto som fanns på källvirtualen för RDP.

I det här exemplet anges namnet på nätverkssäkerhetsgruppen (NSG) på *myNsg* och RDP-regelnamnet till *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slutpunkter och NSG-regler finns i [Öppna portar till en virtuell dator i Azure med PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
Om du vill aktivera kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa den offentliga IP-adressen. I det här exemplet är det offentliga IP-adressnamnet inställt på *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Skapa nätverkskortet. I det här exemplet är nätverkskortets namn inställt på *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Ange vm-namn och storlek

I det här exemplet anges vm-namnet på *myVM* och vm-storleken för att *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till nätverkskortet
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Lägga till OS-disken 

Lägg till OS-disken i konfigurationen med hjälp av [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). I det här exemplet anges storleken på disken på *128 GB* och den hanterade disken kopplas till en *Windows* OS-disk.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med hjälp av [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) med de konfigurationer som vi just skapade.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Om det här kommandot lyckas visas utdata så här:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
Du bör se den nyskapade virtuella datorn antingen i [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**eller genom att använda följande PowerShell-kommandon.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på din nya virtuella dator. Mer information finns i [Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](connect-logon.md).

