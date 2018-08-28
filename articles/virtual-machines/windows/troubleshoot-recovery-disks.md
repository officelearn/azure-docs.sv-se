---
title: Använda en Windows felsöknings-VM med Azure PowerShell | Microsoft Docs
description: Lär dig att felsöka problem med Windows-VM i Azure genom att ansluta operativsystemdisken till en virtuell dator med Azure PowerShell för återställning
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: f099eefbc6d196f25c2b09669cdc1c3cdec68a12
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050022"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator med Azure PowerShell för återställning
Om din Windows virtuell dator (VM) i Azure påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på själva disken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure PowerShell för att ansluta disken till en annan virtuell Windows-dator att åtgärda eventuella fel och reparera den ursprungliga virtuella datorn. 

> [!Important]
> Skripten i den här artikeln gäller endast för de virtuella datorerna som använder [Managed Disk](managed-disks-overview.md). 


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
Connect-AzureRmAccount
```

I följande exempel, ersätter du parameternamnen med dina egna värden. 

## <a name="determine-boot-issues"></a>Fastställa startproblem
Du kan visa en skärmbild av den virtuella datorn i Azure för att felsöka startproblem. Den här skärmbilden kan hjälpa dig att identifiera varför det inte går att starta en virtuell dator. I följande exempel hämtas skärmbilden från Windows-VM med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärmbilden för att avgöra varför den virtuella datorn kan inte starta. Observera några felmeddelanden eller felkoder som tillhandahålls.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Följande exempel stoppar den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```powershell
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
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
$vm = get-azurermvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzureRmSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzureRmSnapshot `
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

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
#Get-AzureRmLocation
$location = 'eastus'

$snapshot = Get-AzureRmSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzureRmDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
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
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Ansluta till den Virtuella återställningsdatorn och åtgärda problem på den anslutna disken

1. RDP till ditt recovery virtuell dator med rätt autentiseringsuppgifter. I följande exempel hämtar RDP-anslutning-filen för den virtuella datorn med namnet `RecoveryVM` i resursgruppen med namnet `myResourceGroup`, och laddar ned den till `C:\Users\ops\Documents`”

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
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
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra operativsystemdisken för den berörda virtuella datorn

Du kan använda Azure PowerShell för att växla OS-diskar. Du behöver ta bort och återskapa den virtuella datorn.

Det här exemplet stoppar den virtuella datorn med namnet `myVM` och tilldelar disk med namnet `newOSDisk` som den nya OS-disken. 

```powershell
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Kontrollera och aktivera startdiagnostik

I följande exempel aktiveras diagnostiktillägget på den virtuella datorn med namnet `myVMDeployed` i resursgruppen med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en virtuell Windows-dator](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du använder Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
