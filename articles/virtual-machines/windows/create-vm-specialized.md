---
title: Skapa en virtuell Windows-dator från en specialiserad virtuell Hårddisk i Azure | Microsoft Docs
description: Skapa en ny virtuell Windows-dator genom att koppla en särskild hanterad disk som OS-disk med hjälp av Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 364dca8d7cab3698e501e74d0c2817aac1a36f88
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137985"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Skapa en virtuell Windows-dator från en särskild disk med hjälp av PowerShell

Skapa en ny virtuell dator genom att koppla en särskild hanterad disk som OS-disk. En särskild disk är en kopia av en virtuell hårddisk (VHD) från en befintlig virtuell dator som innehåller användarkonton, program och andra systemtillstånd från den ursprungliga virtuella datorn. 

När du använder en specialiserad virtuell Hårddisk för att skapa en ny virtuell dator, behåller den nya virtuella datorn namnet på den ursprungliga virtuella datorn. Andra datorspecifika informationen sparas också och i vissa fall kan den här dubblettinformation kan orsaka problem. När du kopierar en virtuell dator vara medveten om vilka typer av datorspecifik information ditt program förlitar sig på.

Har du flera alternativ:
* [Använd en befintlig hanterad disk](#option-1-use-an-existing-disk). Det här alternativet är användbart om du har en virtuell dator som inte fungerar korrekt. Du kan ta bort den virtuella datorn och sedan återanvända den hantera disken för att skapa en ny virtuell dator. 
* [Överföra en virtuell hårddisk](#option-2-upload-a-specialized-vhd) 
* [Kopiera en befintlig virtuell Azure-dator med hjälp av ögonblicksbilder](#option-3-copy-an-existing-azure-vm)

Du kan också använda Azure portal för att [skapa en ny virtuell dator från en specialiserad virtuell Hårddisk](create-vm-specialized-portal.md).

Den här artikeln visar hur du använder hanterade diskar. Om du har en äldre distribution som kräver att du använder ett lagringskonto, se [skapa en virtuell dator från en specialiserad virtuell Hårddisk i ett lagringskonto](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="option-1-use-an-existing-disk"></a>Alternativ 1: Använd en befintlig disk

Om du har en virtuell dator som du har tagit bort och du vill återanvända OS-disken du skapar en ny virtuell dator, använder [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nu kan du koppla den här disken som OS-disk till en [ny virtuell dator](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Alternativ 2: Ladda upp en specialiserad virtuell Hårddisk

Du kan överföra den virtuella Hårddisken från en specialiserad virtuell dator som skapats med en lokal virtualisering verktyg, som Hyper-V eller en virtuell dator som har exporterats från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Använd den virtuella Hårddisken som – är att skapa en ny virtuell dator. 
  
  * [Förbereda en Windows virtuell Hårddisk för överföring till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Inte** generalisera den virtuella datorn med hjälp av Sysprep.
  * Ta bort alla gäst virtualization-verktyg och agenter som installerats på den virtuella datorn (till exempel VMware-verktyg).
  * Kontrollera att den virtuella datorn är konfigurerad för att hämta IP-adress och DNS-inställningar från DHCP. Detta säkerställer att servern får en IP-adress inom det virtuella nätverket när den startas. 


### <a name="get-the-storage-account"></a>Hämta lagringskontot
Du behöver ett lagringskonto i Azure för att lagra den överförda virtuella Hårddisken. Du kan antingen använda ett befintligt lagringskonto eller skapa en ny. 

Visa tillgängliga storage-konton.

```powershell
Get-AzStorageAccount
```

Om du vill använda ett befintligt lagringskonto, fortsätter du till den [överför den virtuella Hårddisken](#upload-the-vhd-to-your-storage-account) avsnittet.

Skapa ett lagringskonto.

1. Du behöver namnet på resursgruppen där lagringskontot ska skapas. Använd Get-AzResourceGroup finns i alla resursgrupper i prenumerationen.
   
    ```powershell
    Get-AzResourceGroup
    ```

    Skapa en resursgrupp med namnet *myResourceGroup* i den *västra USA* region.

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Skapa ett lagringskonto med namnet *mystorageaccount* i den nya resursgruppen med hjälp av den [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet.
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella Hårddisken till ditt storage-konto 
Använd den [Lägg till AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) cmdlet för att ladda upp den virtuella Hårddisken till en behållare i ditt storage-konto. Det här exemplet överför filen *myVHD.vhd* från ”C:\Users\Public\Documents\Virtual hårddiskar\" till ett lagringskonto med namnet *mystorageaccount* i den  *myResourceGroup* resursgrupp. Filen lagras i behållare med namnet *mycontainer* och det nya filnamnet blir *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om kommandona lyckas kan få du ett svar som ser ut ungefär så här:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Det här kommandot kan ta en stund att slutföra beroende på din nätverksanslutning och storleken på VHD-filen.

### <a name="create-a-managed-disk-from-the-vhd"></a>Skapa en hanterad disk från den virtuella Hårddisken

Skapa en hanterad disk från en specialiserad virtuell Hårddisk i ditt storage-konto med hjälp av [New AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Det här exemplet används *myOSDisk1* för namnet på disken, placerar du disken i *Standard_LRS* lagring och använder *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* som URI för källan VHD.

Skapa en ny resursgrupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Skapa den nya OS-disken från den överförda virtuella Hårddisken. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Alternativ 3: Kopiera en befintlig Azure VM

Du kan skapa en kopia av en virtuell dator som använder hanterade diskar genom att ta en ögonblicksbild av den virtuella datorn och hanterade disk och en ny virtuell dator med hjälp av den ögonblicksbilden för att skapa en ny.


### <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicksbild av OS-disken

Du kan ta en ögonblicksbild av en hel virtuell dator (inklusive alla diskar) eller bara en enda disk. Följande steg visar hur du kan ta en ögonblicksbild av bara operativsystemdisken för den virtuella datorn med den [New AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet. 

Innan du definiera vissa parametrar. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Hämta det Virtuella datorobjektet.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Hämta namnet på OS-disk.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Skapa ögonblicksbild-konfigurationen. 

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


Om du vill använda den här ögonblicksbilden för att skapa en virtuell dator som måste vara hög prestanda, lägger du till parametern `-AccountType Premium_LRS` till kommandot New-AzSnapshotConfig. Den här parametern skapar ögonblicksbilden så att den lagras som en hanterad Disk i Premium. Premium Managed Disks är dyrare än Standard, så du behöver Premium innan du använder den här parametern.

### <a name="create-a-new-disk-from-the-snapshot"></a>Skapa en ny disk från ögonblicksbilden

Skapa en hanterad disk från ögonblicksbilden med hjälp av [New AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Det här exemplet används *myOSDisk* för diskens namn.

Skapa en ny resursgrupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ange namnet på OS-disken. 

```powershell
$osDiskName = 'myOsDisk'
```

Skapa den hantera disken.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Skapa ny virtuell dator 

Skapa nätverk och andra VM-resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-virtual-network"></a>Skapa undernät och virtuellt nätverk

Skapa den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) och undernät för den virtuella datorn.

1. Skapa undernätet. Det här exemplet skapar ett undernät med namnet *mySubNet*, i resursgruppen *myDestinationResourceGroup*, och anger adressprefixet undernätet till *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Skapa det virtuella nätverket. Det här exemplet anges det virtuella nätverksnamnet *myVnetName*, platsen som *västra USA*, och adressprefixet för det virtuella nätverket till *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en regel för RDP
För att kunna logga in på den virtuella datorn med remote desktop protocol (RDP), måste du ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389. I vårt exempel har den virtuella Hårddisken för den nya virtuella datorn skapats från en befintlig specialiserad virtuell dator, så du kan använda ett konto som fanns på den virtuella källdatorn för RDP.

Det här exemplet anger nätverkssäkerhetsgruppsnamn (NSG) till *myNsg* och Regelnamnet RDP till *myRdpRule*.

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

Läs mer om slutpunkter och NSG-regler, [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och nätverkskort
Om du vill aktivera kommunikation med den virtuella datorn i det virtuella nätverket, behöver du en [offentliga IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa offentlig IP-adress. I det här exemplet anges namnet för offentliga IP-adress till *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Skapa nätverkskortet. I det här exemplet är namnet på nätverkskortet har angetts *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Ange den virtuella datorns namn och storlek

Det här exemplet anges VM-namnet *myVM* och VM-storleken till *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägga till NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Lägg till OS-disk 

Lägg till OS-disken i konfigurationen med hjälp av [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Det här exemplet anger storleken på disken för att *128 GB* och kopplar den hantera disken som en *Windows* OS-disken.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med hjälp av [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) med konfigurationer som vi precis skapade.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Om det här kommandot lyckas visas utdata som liknar detta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
Du bör se den nya virtuella datorn antingen i den [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på din nya virtuella dator. Mer information finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](connect-logon.md).

