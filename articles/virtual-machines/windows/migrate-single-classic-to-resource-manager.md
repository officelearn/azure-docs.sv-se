---
title: Migrera en klassisk virtuell dator till en hanterad ARM-Disk VM | Microsoft Docs
description: "Migrera en enda Azure-VM från den klassiska distributionsmodellen till hanterade diskar i Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: 82389834d85981c0ed71bdcc891fbfdbe1377654
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Migrera manuellt en klassisk virtuell dator till en ny ARM hanteras Disk virtuell dator från den virtuella Hårddisken 


Det här avsnittet hjälper dig att migrera dina befintliga virtuella Azure-datorer från den klassiska distributionsmodellen till [hanterade diskar](managed-disks-overview.md) i Resource Manager-distributionsmodellen.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migrering till hanterade diskar

Det här avsnittet hjälper dig att göra det bästa på VM- och diskresurser typer.


### <a name="location"></a>Plats

Välj en plats där Azure hanterade diskar är tillgängliga. Om du migrerar till hanterade Premiumdiskar också se till att Premium-lagring är tillgänglig i den region där du planerar att migrera till. Se [Azure Services byRegion](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium hanterade diskar som du behöver uppdatera storleken på den virtuella datorn till Premium-lagring kan storlek i den region där VM finns. Granska storlek på Virtuella datorer som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaper för virtuella datorer som fungerar med Premium-lagring och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att ge disk-trafik.

### <a name="disk-sizes"></a>Diskstorlekar

**Premium hanterade diskar**

Det finns sju typer av premium hanterade diskar som kan användas med den virtuella datorn var och en har särskilda IOPs och genomströmning gränser. Överväg att dessa begränsningar när du väljer Premium disktyp för den virtuella datorn baserat på dina behov av ditt program vad gäller kapacitet, prestanda, skalbarhet och belastning läses in.

| Premium diskar typ  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 128 GB| 512 GB| 128 GB| 512 GB            | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund | 

**Hanterade standarddiskar**

Det finns sju typer av Standard hanterade diskar som kan användas med den virtuella datorn. Var och en av dem har olika kapacitet men samma IOPS och genomströmning gränser. Välj typ av Standard hanterade diskar baserat på kapacitetsbehov för programmet.

| Disk av standardtyp  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1 024 GB (1 TB)   | 2 048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 


### <a name="disk-caching-policy"></a>Princip för cachelagring av disk 

**Premium hanterade diskar**

Princip för cachelagring av disk är som standard *skrivskyddad* för alla Premium datadiskar, och *Read-Write* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimal prestanda för ditt program IOs. Inaktivera cachelagring på disk så att du kan få bättre prestanda för programmet för skrivintensiv eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

### <a name="pricing"></a>Prissättning

Granska de [priser för hanterade diskar](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Priser för hanterade Premiumdiskar är samma som ohanterad Premiumdiskar. Men priser för hanterade standarddiskar skiljer sig från ohanterade standarddiskar.


## <a name="checklist"></a>Checklista

1.  Om du migrerar till hanterade Premiumdiskar, kontrollera att den är tillgänglig i den region du migrerar till.

2.  Bestäm den nya VM-serien som du kommer att använda. Om du migrerar till hanterade Premiumdiskar bör det vara en Premium-lagring som är kompatibla.

3.  Bestäm den exakta VM-storlek du vill använda som är tillgängliga i den region du migrerar till. VM-storlek måste vara tillräckligt stor för att stödja antalet datadiskar som du har. Om du har fyra datadiskar, måste den virtuella datorn ha minst två kärnor. Du kan också processorkraft, minne och nätverksbandbredd måste.

4.  Har den aktuella informationen för VM praktisk, inklusive en lista över diskar och motsvarande VHD-blobbar.

Förbered ditt program för driftstopp. För att göra en ren migrering, måste du stoppa all bearbetning i det aktuella systemet. Först då kan du få till konsekvent tillstånd som du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror på mängden data på diskarna att migrera.


## <a name="migrate-the-vm"></a>Migrera den virtuella datorn

Förbered ditt program för driftstopp. För att göra en ren migrering, måste du stoppa all bearbetning i det aktuella systemet. Först då kan du få till konsekvent tillstånd som du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror mängden data på diskarna att migrera.


1.  Innan du kan definiera de gemensamma parametrarna:

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

2.  Skapa en hanterad OS-disk med hjälp av den virtuella Hårddisken från den klassiska virtuella datorn.

    Se till att du har angett fullständiga URI för OS-VHD i parametern $osVhdUri. Ange dessutom **- AccountType** som **PremiumLRS** eller **StandardLRS** baserat på typ av diskar (Standard eller Premium) du migrerar till.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Koppla OS-disken till den nya virtuella datorn.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Skapa en hanterad datadisk från VHD-datafilen och Lägg till den nya virtuella datorn.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Skapa den nya virtuella datorn genom att ange offentlig IP, virtuella nätverk och nätverkskort.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Det kan finnas ytterligare steg behövs för ditt program som inte omfattas av den här guiden.
>
>

## <a name="next-steps"></a>Nästa steg

- Ansluta till den virtuella datorn. Instruktioner finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

