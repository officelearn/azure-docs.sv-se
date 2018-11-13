---
title: Självstudier – Hantera Azure-diskar med Azure PowerShell | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att skapa och hantera Azure-diskar för virtuella datorer
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c69a91ce360b5476541de29dc52ea89057aa726c
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037642"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Självstudier – Hantera Azure-diskar med Azure PowerShell

Azure Virtual Machines använder diskar för att lagra de virtuella datorernas operativsystem, program och data. När du skapar en virtuell dator är det viktigt att du väljer en diskstorlek och konfiguration som motsvarar den förväntade arbetsbelastningen. Den här kursen visar hur du distribuerar och hanterar VM-diskar. Du får lära dig om:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="default-azure-disks"></a>Azure-standarddiskar

När en virtuell Azure-dator skapas kopplas två diskar automatiskt till den virtuella datorn. 

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 4 TB och innehåller de virtuella datorernas operativsystem.  OS-disken tilldelas enhetsbokstaven *C:* som standard. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. OS-disken **bör inte** innehålla program eller data. För program och data använder du en datadisk (beskrivs senare i den här artikeln).

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporära diskar har höga prestanda och kan användas för åtgärder som till exempel tillfällig databearbetning. Men om den virtuella datorn flyttas till en ny värd tas alla data som är lagrade på den temporära disken bort. Storleken på den temporära disken bestäms av [VM-storleken](sizes.md). Temporära diskar tilldelas enhetsbeteckningen *D:* som standard.



## <a name="azure-data-disks"></a>Azure-datadiskar

Du kan lägga till ytterligare datadiskar för att installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Varje datadisk har en maxkapacitet på 4 TB. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator. Fyra datadiskar kan kopplas för varje VM-vCPU. 


## <a name="vm-disk-types"></a>VM-disktyper

Azure tillhandahåller två disktyper.

**Standard-diskar** – Stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

**Premiumdiskar** – Backas av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien GS-serien och FS-serien. Det finns fem typer av Premiumdiskar (P10, P20, P30, P40 och P50). Storleken på disken bestämmer disktypen. När du gör ditt val avrundas diskstorleken uppåt till nästa typ. Om storleken till exempel är under 128 GB är disktypen P10. Om storleken är mellan 129 GB och 512 GB är disktypen P20.

### <a name="premium-disk-performance"></a>Premiumdiskprestanda

|Premium Storage-disktyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 | p60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Diskens storlek (avrundas uppåt) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB)
| Högsta IOPS per disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 | 12 500 |
Dataflöde per disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. Du kan till exempel koppla 64 datadiskar till en virtuell Standard GS5-dator. Om var och en av dessa diskar har storleken P30 kan du ha högst 80 000 IOPS. Mer information om högsta IOPS per VM finns i [VM-typer och storlekar](./sizes.md).

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i den här självstudiekursen. Skapa en virtuell dator med följande kommandon, om det behövs.

Ange användarnamnet och lösenordet för administratörskontot på den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Du uppmanas att ange ett användarnamn och lösenord för den virtuella datorns administratörkonto.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Skapa den inledande konfigurationen med [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). I följande exempel konfigureras en disk med storleken 128 GB.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Skapa datadisken med kommandot [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Hämta den virtuella dator som du vill lägga till datadisken för med kommandot [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Lägg till datadisken i VM-konfigurationen med kommandot [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Uppdatera den virtuella datorn med kommandot [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Förbereda datadiskar

När en disk har kopplats till den virtuella datorn måste operativsystemet konfigureras för att använda disken. I följande exempel visas hur du manuellt konfigurerar den första disken som lagts till i den virtuella datorn. Den här processen kan också automatiseras med hjälp av [tillägget för anpassat skript](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Manuell konfiguration

Skapa en RDP-anslutning med den virtuella datorn. Öppna PowerShell och kör det här skriptet.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Kontrollera datadisken

Du kan kontrollera att datadisken är kopplad genom att visa `StorageProfile` för de kopplade `DataDisks`.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Utdata bör se ut ungefär som i följande exempel:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig mer om VM-diskar, till exempel:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Koppling och förberedelse av datadiskar

Gå vidare till nästa självstudie om du vill lära dig hur du automatiserar konfigurationen av virtuella datorer.

> [!div class="nextstepaction"]
> [Automatisera konfiguration av virtuella datorer](./tutorial-automate-vm-deployment.md)
