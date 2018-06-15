---
title: Skapa en virtuell Windows-dator från en särskild virtuell Hårddisk i Azure | Microsoft Docs
description: Skapa en ny Windows virtuell dator genom att koppla en särskild hanterade diskar som OS-disk i Resource Manager-distributionsmodellen.
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
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: be7933b038fb5a648249e9b0c73415bff778930b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012792"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Skapa en virtuell Windows-dator från en särskild disk med hjälp av PowerShell

Skapa en ny virtuell dator genom att koppla en särskild hanterade diskar som OS-disk. En specialiserad disk är en kopia av virtuell hårddisk (VHD) från en befintlig virtuell dator som hanterar användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

När du använder en särskild virtuell Hårddisk för att skapa en ny virtuell dator, behåller datornamnet för den ursprungliga virtuella datorn i den nya virtuella datorn. Andra datorspecifik information sparas också och i vissa fall kan den här dubblettinformation kan orsaka problem. Ta reda på vilka typer av information om datorn dina program förlitar sig på när du kopierar en virtuell dator.

Har du flera alternativ:
* [Använd en befintlig hanterade disk](#option-1-use-an-existing-disk). Detta är användbart om du har en virtuell dator som inte fungerar korrekt. Du kan ta bort VM återanvändning hanterade disken för att skapa en ny virtuell dator. 
* [Överföra en virtuell hårddisk](#option-2-upload-a-specialized-vhd) 
* [Kopiera en befintlig Azure-dator med hjälp av ögonblicksbilder](#option-3-copy-an-existing-azure-vm)

Du kan också använda Azure portal och [skapa en ny virtuell dator från en särskild virtuell Hårddisk](create-vm-specialized-portal.md).

Det här avsnittet visar hur du använder hanterade diskar. Om du har en äldre distribution som kräver att du använder ett lagringskonto, se [skapa en virtuell dator från en särskild virtuell Hårddisk i ett lagringskonto](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Innan du börjar
Om du använder PowerShell kan du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Mer information finns i [Azure PowerShell versionshantering](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Alternativ 1: Använd en befintlig disk

Om du har en virtuell dator som du har tagit bort och du vill återanvända OS-disk om du vill skapa en ny virtuell dator använda [Get-AzureRmDisk](/azure/powershell/get-azurermdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nu kan du koppla den här disken som OS-disk till en [ny virtuell dator](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Alternativ 2: Ladda upp en särskild virtuell Hårddisk

Du kan överföra den virtuella Hårddisken från en särskild virtuell dator som skapats med ett lokalt virtualisering verktyg, som Hyper-V eller en virtuell dator som har exporterats från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Om du tänker använda den virtuella Hårddisken som – är att skapa en ny virtuell dator och se till att följande åtgärder har slutförts. 
  
  * [Förbereda en Windows-VHD att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Inte** generalisera den virtuella datorn med hjälp av Sysprep.
  * Ta bort gäst virtualiseringsverktyg och agenter som installerats på den virtuella datorn (till exempel VMware-verktyg).
  * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningarna via DHCP. Detta säkerställer att servern erhåller en IP-adress inom VNet när den startas. 


### <a name="get-the-storage-account"></a>Hämta storage-konto
Du behöver ett lagringskonto i Azure för att lagra den överförda virtuella Hårddisken. Du kan använda ett befintligt lagringskonto eller skapa en ny. 

Om du vill visa tillgängliga storage-konton, skriver du:

```powershell
Get-AzureRmStorageAccount
```

Om du vill använda ett befintligt lagringskonto fortsätter du till den [överför den virtuella Hårddisken](#upload-the-vhd-to-your-storage-account) avsnitt.

Följ dessa steg om du behöver skapa ett lagringskonto:

1. Du måste namnet på resursgruppen där lagringskontot ska skapas. Om du vill ta reda på alla resursgrupper i din prenumeration, skriver du:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Så här skapar du en resursgrupp med namnet *myResourceGroup* i den *västra USA* region, typ:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Skapa ett lagringskonto med namnet *mittlagringskonto* i den här resursgruppen med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Överför den virtuella Hårddisken till ditt lagringskonto 
Använd den [Lägg till AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) för att ladda upp den virtuella Hårddisken till en behållare i ditt lagringskonto. Det här exemplet överför filen *myVHD.vhd* från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagringskonto med namnet *mittlagringskonto* i den *myResourceGroup* resursgruppen. Filen lagras i behållare med namnet *minbehållare* och det nya namnet kommer att *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om det lyckas, kan du få ett svar som liknar denna:

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

Det här kommandot kan ta en stund att slutföra beroende på nätverksanslutningen och storleken på VHD-filen

### <a name="create-a-managed-disk-from-the-vhd"></a>Skapa en hanterade diskar från den virtuella Hårddisken

Skapa en hanterad disk från särskilda VHD: N i din storage-konto med [ny AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Det här exemplet används **myOSDisk1** för disk-namn, placerar disken i *Standard_LRS* lagring och använder *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* som URI för källan VHD.

Skapa en ny resursgrupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Skapa den nya OS-disken från den överförda virtuella Hårddisken. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Alternativ 3: Kopiera en befintlig virtuell Azure-dator

Du kan skapa en kopia av en virtuell dator som använder hanterade diskar genom att ta en ögonblicksbild av den virtuella datorn och sedan med den ögonblicksbilden för att skapa en ny hanterade disken och en ny virtuell dator.


### <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicksbild av OS-disk

Du kan ta en ögonblicksbild av och en hel virtuell dator (inklusive alla diskar) eller bara en enda disk. Följande steg visar hur du kan ta en ögonblicksbild av bara OS-disk för virtuell dator med hjälp av den [ny AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Ange vissa parametrar. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Hämta det Virtuella datorobjektet.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Hämta OS-diskens namn.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Skapa en ögonblicksbild av konfigurationen. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Ta ögonblicksbilden.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Om du planerar att använda ögonblicksbilden för att skapa en virtuell dator som måste vara höga prestanda, använder du parametern `-AccountType Premium_LRS` med kommandot Ny AzureRmSnapshot. Parametern skapar ögonblicksbilden så att den lagras som en Premium hanteras Disk. Premium-hanterade diskar är dyrare än Standard. Vara säker på att du verkligen behöver Premium innan du använder parametern.

### <a name="create-a-new-disk-from-the-snapshot"></a>Skapa en ny disk från ögonblicksbilden

Skapa en hanterad disk från en ögonblicksbild med hjälp av [ny AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Det här exemplet används *myOSDisk* för diskens namn.

Skapa en ny resursgrupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ange namnet på OS-disk. 

```powershell
$osDiskName = 'myOsDisk'
```

Skapa den hantera disken.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Skapa ny virtuell dator 

Skapa nätverk och andra VM resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-vnet"></a>Skapa undernät och virtuella nätverk

Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Skapa undernätet. Det här exemplet skapar ett undernät med namnet **mySubNet**, i resursgrupp **myDestinationResourceGroup**, och anger adressprefixet undernät till **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

Skapa vNet. Det här exemplet anger det virtuella nätverksnamnet ska **myVnetName**, platsen för **västra USA**, och adressprefixet för det virtuella nätverket till **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa säkerhetsgrupp för nätverk och en RDP-regel
Du måste ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. Eftersom den virtuella Hårddisken för den nya virtuella datorn har skapats från en befintlig särskild virtuell dator kan du använda ett konto från den virtuella källdatorn för RDP.

Det här exemplet anger NSG namn **myNsg** och Regelnamnet RDP till **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slutpunkter och NSG-regler finns [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

Skapa offentlig IP. I det här exemplet anges det offentliga IP-adress namnet till **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Skapa nätverkskortet. I det här exemplet NIC-namnet har angetts **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Ange namn på virtuell dator och storlek

Det här exemplet anger namnet på virtuella datorn till *myVM* och VM-storlek till *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till nätverkskortet
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Lägg till OS-disk 

Lägg till OS-disk i en konfiguration med hjälp av [Set AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Det här exemplet anger storleken på disken för att *128 GB* och bifogar hanterade disken som en *Windows* OS-disken.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa en virtuell dator med hjälp av [ny AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)de konfigurationer som vi just skapade.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Om det här kommandot lyckades visas utdata som liknar detta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
Du bör se den nyligen skapade Virtuellt antingen i den [Azure-portalen](https://portal.azure.com)under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på den nya virtuella datorn. Mer information finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](connect-logon.md).

