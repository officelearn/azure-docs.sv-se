---
title: Använda en Windows felsöknings-VM med Azure PowerShell | Microsoft Docs
description: Lär dig att felsöka problem med Windows-VM i Azure genom att ansluta operativsystemdisken till en virtuell dator med Azure PowerShell för återställning
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 45e595e932c4e7070836372a4fd48791f68c5e08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761573"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator med Azure PowerShell för återställning
Om din Windows virtuell dator (VM) i Azure påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på själva disken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure PowerShell för att ansluta disken till en annan virtuell Windows-dator att åtgärda eventuella fel och reparera den ursprungliga virtuella datorn. 

> [!Important]
> Skripten i den här artikeln gäller endast för de virtuella datorerna som använder [Managed Disk](../windows/managed-disks-overview.md). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Vi kan nu använda Azure PowerShell för att ändra OS-disk för en virtuell dator. Vi behöver inte längre ta bort och återskapa den virtuella datorn.

Så här ser felsökningsprocessen ut:

1. Stoppa den berörda virtuella datorn.
2. Skapa en ögonblicksbild från den virtuella datorn OS-Disk.
3. Skapa en disk från OS-diskens ögonblicksbild.
4. Koppla disken som en datadisk till en virtuell dator för återställning.
5. Anslut till den Virtuella återställningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den kopierade OS-disken.
6. Demontera och koppla från disken från den Virtuella återställningsdatorn.
7. Ändra operativsystemdisken för den berörda virtuella datorn.

Du kan använda skript för VM-återställning för att automatisera steg 1, 2, 3, 4, 6 och 7. Mer dokumentation och instruktioner finns i [VM Recovery skript för Resource Manager-VM](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Se till att du har [den senaste Azure PowerShell](/powershell/azure/overview) installerat och loggat in till din prenumeration:

```powershell
Connect-AzAccount
```

I följande exempel, ersätter du parameternamnen med dina egna värden. 

## <a name="determine-boot-issues"></a>Fastställa startproblem
Du kan visa en skärmbild av den virtuella datorn i Azure för att felsöka startproblem. Den här skärmbilden kan hjälpa dig att identifiera varför det inte går att starta en virtuell dator. I följande exempel hämtas skärmbilden från Windows-VM med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärmbilden för att avgöra varför den virtuella datorn kan inte starta. Observera några felmeddelanden eller felkoder som tillhandahålls.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Följande exempel stoppar den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du bearbetar till nästa steg.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Skapa en ögonblicksbild från den virtuella datorn OS-Disk

I följande exempel skapas en ögonblicksbild med namnet `mySnapshot` från Operativsystemet den virtuella datorn disk med namnet ”myVM”. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. Det går inte att koppla till en virtuell dator. I nästa steg ska vi skapa en disk från den här ögonblicksbilden.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicksbilden

Det här skriptet skapar en hanterad disk med namnet `newOSDisk` från ögonblicksbilden med namnet `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Nu har du en kopia av den ursprungliga OS-disken. Du kan montera denna disk i en annan virtuell Windows-dator för felsökning.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Koppla disken till en annan virtuell Windows-dator för felsökning

Nu koppla vi kopia av den ursprungliga OS-disken till en virtuell dator som en datadisk. Den här processen kan du korrigera konfigurationsfel eller granska ytterligare program eller systemloggfilerna i disken. I följande exempel kopplar disk med namnet `newOSDisk` till den virtuella datorn med namnet `RecoveryVM`.

> [!NOTE]
> Om du vill koppla disken måste kopian av den ursprungliga OS-disken och den Virtuella återställningsdatorn vara på samma plats.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Ansluta till den Virtuella återställningsdatorn och åtgärda problem på den anslutna disken

1. RDP till ditt recovery virtuell dator med rätt autentiseringsuppgifter. I följande exempel hämtar RDP-anslutning-filen för den virtuella datorn med namnet `RecoveryVM` i resursgruppen med namnet `myResourceGroup`, och laddar ned den till `C:\Users\ops\Documents`”

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datadisken ska identifieras automatiskt och kopplas. Visa en lista över anslutna volymer för att fastställa enhetsbeteckningen på följande sätt:

    ```powershell
    Get-Disk
    ```

    Följande Exempelutdata visar att disken är ansluten till en disk **2**. (Du kan också använda `Get-Volume` att visa enhetsbeteckningen):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

När kopia av den ursprungliga OS-disken är monterad kan du utföra underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-os-disk"></a>Demontera och koppla från ursprungliga OS-disk
När din fel har åtgärdats du demontera och koppla från den befintliga disken från den Virtuella återställningsdatorn. Du kan inte använda disken med andra virtuella datorer tills frigörs lånet ansluta disken till den Virtuella återställningsdatorn.

1. Demontera datadisken på den Virtuella återställningsdatorn i RDP-session från. Du behöver numret på den disk från den tidigare `Get-Disk` cmdlet. Använd sedan `Set-Disk` att ställa in disken som offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bekräfta disken nu är inställd som offline med hjälp av `Get-Disk` igen. Följande Exempelutdata visar disken nu är inställd som offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Avsluta RDP-session. Ta bort disken med namnet från Azure PowerShell-session `newOSDisk` från den virtuella datorn med namnet ”RecoveryVM”.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra operativsystemdisken för den berörda virtuella datorn

Du kan använda Azure PowerShell för att växla OS-diskar. Du behöver ta bort och återskapa den virtuella datorn.

Det här exemplet stoppar den virtuella datorn med namnet `myVM` och tilldelar disk med namnet `newOSDisk` som den nya OS-disken. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Kontrollera och aktivera startdiagnostik

I följande exempel aktiveras diagnostiktillägget på den virtuella datorn med namnet `myVMDeployed` i resursgruppen med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en virtuell Windows-dator](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du använder Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
