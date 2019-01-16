---
title: Migrera en klassisk virtuell dator till en hanterad Disk för ARM VM | Microsoft Docs
description: Migrera en virtuell Azure-dator från den klassiska distributionsmodellen till Managed Disks i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: a662a61d737dbb620d07fa6d114649e70c082796
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329777"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>Migrera en klassisk virtuell dator om du vill använda en hanterad Disk 


Det här avsnittet hjälper dig att migrera dina befintliga virtuella Azure-datorer från den klassiska distributionsmodellen till [Managed Disks](managed-disks-overview.md) i Resource Manager-distributionsmodellen.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migrering till Managed Disks

Det här avsnittet hjälper dig att fatta det bästa beslutet på virtuell dator och disk-typer.


### <a name="location"></a>Plats

Välj en plats där Managed Disks är tillgängliga. Om du migrerar till hanterad Disk som backas upp av Premium storage kan också kontrollera att Premium storage är tillgängligt i den regionen. Se [Azure Services byRegion](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till en Managed Disks använder Premium storage kan behöva du uppdatera storleken på den virtuella datorn till Premium Storage kan storlek tillgängliga i den region där VM finns. Granska de storlekar som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaperna för virtuella datorer med Premium Storage och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Se till att det finns tillräckligt mycket bandbredd på den virtuella datorn att driva trafiken disk.

### <a name="disk-sizes"></a>Diskstorlekar

**Premium**

Det finns sju typer av Premium-lagring som kan användas med den virtuella datorn och alla har specifika IOPs och dataflöde gränser. Överväg att dessa begränsningar när du väljer Premium-disktyp för en virtuell dator baserat på dina behov för ditt program när det gäller kapacitet, prestanda, skalbarhet och högsta läses in.

| Typen för Premium-diskar  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 128 GB| 512 GB| 128 GB| 512 GB            | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4 095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund | 

**Standard**

Det finns sju typer av standarddiskar som kan användas med den virtuella datorn. Var och en av dem har kapacitet för olika men har samma IOPS och dataflöde gränser. Välj typ av Standard Managed disks baserat på programmets kapacitetsbehov.

| Disk av standardtyp  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1 024 GB (1 TB)   | 2 048 GB (2 TB)    | 4 095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 


### <a name="disk-caching-policy"></a>Disk-principen för cachelagring 

**Premium Managed Disks**

Som standard disken Cachelagringsprincip är *skrivskyddad* för alla Premium datadiskar, och *skrivskyddad* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. Inaktivera diskcachelagring så att du kan få bättre prestanda för hög eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

### <a name="pricing"></a>Prissättning

Granska den [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Priser för hanterade premiumdiskar är samma som ohanterade premium-diskar. Men priserna för hanterade standarddiskar skiljer sig från ohanterade standarddiskar.


## <a name="checklist"></a>Checklista

1.  Om du migrerar till Premium Managed Disks kan du kontrollera att den är tillgänglig i den region som du migrerar till.

2.  Bestäm den nya VM-serien som du kommer att använda. Om du migrerar till Premium Managed Disks bör den vara en Premium-lagring som är kompatibla.

3.  Bestämma den exakta VM-storlek du ska använda som är tillgängliga i den region som du migrerar till. VM-storlek måste vara tillräckligt stor för att stödja antalet datadiskar som du har. Om du har fyra datadiskar, måste den virtuella datorn ha minst två kärnor. Tänk också på processorkraft, minne, och bandbreddsbehov för nätverket.

4.  Har den aktuella VM-informationen till hands, inklusive en lista över diskar och motsvarande VHD-blobbar.

Förbereda programmet för driftstopp. Du måste stoppa all bearbetning i det aktuella systemet om du vill göra en ren migrering. Först då får du det konsekventa tillståndet, vilket du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror på mängden data i diskar för att migrera.


## <a name="migrate-the-vm"></a>Migrera den virtuella datorn

Förbereda programmet för driftstopp. Du måste stoppa all bearbetning i det aktuella systemet om du vill göra en ren migrering. Du kan bara flytta det till konsekvent tillstånd som du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror mängden data i diskar för att migrera.

Den här delen kräver Azure PowerShell-Modulversion 6.0.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Du måste också köra `Connect-AzureRmAccount` för att upprätta en anslutning till Azure.


Skapa variabler för gemensamma parametrar.

```powershell
$resourceGroupName = 'yourResourceGroupName'

$location = 'your location' 

$virtualNetworkName = 'yourExistingVirtualNetworkName'

$virtualMachineName = 'yourVMName'

$virtualMachineSize = 'Standard_DS3'

$adminUserName = "youradminusername"

$adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force

$imageName = 'yourImageName'

$osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'

$dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'

$dataDiskName = 'dataDisk1'
```

Skapa en hanterad OS-disk med hjälp av den virtuella Hårddisken från den klassiska virtuella datorn. Kontrollera att du har angett den fullständiga URI: N för OS-VHD till parametern $osVhdUri. Ange dessutom **- AccountType** som **Premium_LRS** eller **Standard_LRS** baserat på typ av diskar (premium eller standard) du migrerar till.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

Koppla OS-disken till den nya virtuella datorn.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

Skapa en hanterad datadisk från VHD-datafil och lägga till den i den nya virtuella datorn.

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

Skapa den nya virtuella datorn genom att ange offentlig IP-adress, virtuellt nätverk och nätverkskort.

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>Det kan finnas ytterligare steg behövs som stöder ditt program som inte omfattas av den här guiden.
>
>

## <a name="next-steps"></a>Nästa steg

- Ansluta till den virtuella datorn. Anvisningar finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

