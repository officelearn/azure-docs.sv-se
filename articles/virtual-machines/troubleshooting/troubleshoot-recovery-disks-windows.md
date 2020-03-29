---
title: Använda en virtuell dator med Windows felsökning med Azure PowerShell | Microsoft-dokument
description: Lär dig hur du felsöker Problem med Windows VM i Azure genom att ansluta OS-disken till en virtuell återställnings-DATOR med Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374140"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings-VM med Azure PowerShell
Om din virtuella Windows-dator (VM) i Azure stöter på ett start- eller diskfel kan du behöva utföra felsökningssteg på själva disken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar att den virtuella datorn kan starta. I den här artikeln beskrivs hur du använder Azure PowerShell för att ansluta disken till en annan Windows-virtuell dator för att åtgärda eventuella fel och sedan reparera den ursprungliga virtuella datorn. 

> [!Important]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [Hanterad disk](../windows/managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Vi kan nu använda Azure PowerShell för att ändra OS-disken för en virtuell dator. Vi behöver inte längre ta bort och återskapa den virtuella datorn.

Så här ser felsökningsprocessen ut:

1. Stoppa den berörda virtuella datorn.
2. Skapa en ögonblicksbild från den virtuella datorns os-disk.
3. Skapa en disk från ögonblicksbilden av OS-disken.
4. Koppla disken som en datadisk till en återställnings-VM.
5. Anslut till återställningsdass. Redigera filer eller kör verktyg för att åtgärda problem på den kopierade OS-disken.
6. Avmontera och koppla bort disk från återställnings-VM.
7. Ändra OS-disken för den berörda virtuella datorn.

Du kan använda vm-reparationskommandona för att automatisera steg 1, 2, 3, 4, 6 och 7. Mer dokumentation och instruktioner finns i [Reparera en Virtuell Windows-dator med hjälp av reparationskommandona för Azure Virtual Machine](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Kontrollera att du har [den senaste Azure PowerShell](/powershell/azure/overview) installerad och inloggad på din prenumeration:

```powershell
Connect-AzAccount
```

I följande exempel ersätter du parameternamnen med dina egna värden. 

## <a name="determine-boot-issues"></a>Ta reda på startproblem
Du kan visa en skärmbild av din virtuella dator i Azure för att felsöka startproblem. Den här skärmbilden kan hjälpa till att identifiera varför en virtuell dator inte startar. I följande exempel visas skärmbilden `myVM` från den virtuella `myResourceGroup`datorn för Windows som heter i resursgruppen:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärmbilden för att avgöra varför den virtuella datorn inte startar. Observera eventuella specifika felmeddelanden eller felkoder.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

I följande exempel stoppas den virtuella datorn som namnges `myVM` från resursgruppen: `myResourceGroup`

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du bearbetar till nästa steg.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Skapa en ögonblicksbild från den virtuella datorns operativsystemdisk

I följande exempel skapas `mySnapshot` en ögonblicksbild med namn från OS-disken på den virtuella datorn med namnet "myVM". 

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

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. Den kan inte kopplas till en virtuell dator. I nästa steg skapar vi en disk från den här ögonblicksbilden.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicksbilden

Det här skriptet skapar `newOSDisk` en hanterad `mysnapshot`disk med namn från ögonblicksbilden med namnet .  

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

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Nu har du en kopia av den ursprungliga OS-disken. Du kan montera den här disken på en annan Virtuell Windows-dator för felsökning.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Koppla disken till en annan Virtuell Windows-dator för felsökning

Nu bifogar vi kopian av den ursprungliga OS-disken till en virtuell dator som en datadisk. Med den här processen kan du korrigera konfigurationsfel eller granska ytterligare program- eller systemloggfiler på disken. I följande exempel bifogas `newOSDisk` disken `RecoveryVM`som heter den virtuella datorn .

> [!NOTE]
> Om du vill koppla disken måste kopian av den ursprungliga OS-disken och återställningsdatorn vara på samma plats.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Ansluta till återställningsdatorn och åtgärda problem på den anslutna disken

1. RDP till återställnings-VM med lämpliga autentiseringsuppgifter. I följande exempel hämtas RDP-anslutningsfilen för den virtuella datorn som nämns `RecoveryVM` i resursgruppen med namnet `myResourceGroup`och hämtas den till `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datadisken ska identifieras och anslutas automatiskt. Visa listan över bifogade volymer för att bestämma enhetsbeteckningen enligt följande:

    ```powershell
    Get-Disk
    ```

    I följande exempelutdata visas den disk som är ansluten till en disk **2**. (Du kan `Get-Volume` också använda för att visa enhetsbeteckningen):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

När kopian av den ursprungliga OS-disken har monterats kan du utföra alla underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-os-disk"></a>Avmontera och koppla från den ursprungliga OS-disken
När dina fel har lösts avmonterar och tar du bort den befintliga disken från återställningsdatorn. Du kan inte använda disken med någon annan virtuell dator förrän lånet som ansluter disken till återställningsdatorn släpps.

1. Ta bort datadisketten på återställnings-VM i din RDP-session. Du behöver disknumret från `Get-Disk` föregående cmdlet. Använd `Set-Disk` sedan för att ställa in disken som offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bekräfta att disken nu `Get-Disk` är inställd som offline med igen. I följande exempelutdata visas att disken nu är offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Avsluta rdp-sessionen. Ta bort disken `newOSDisk` från den virtuella datorn som heter RecoveryVM från din Azure PowerShell-session.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra OS-disken för den berörda virtuella datorn

Du kan använda Azure PowerShell för att byta os-diskar. Du behöver inte ta bort och återskapa den virtuella datorn.

Det här exemplet `myVM` stoppar den virtuella `newOSDisk` datorn som namnges och tilldelar disken som namnges som den nya OS-disken. 

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

## <a name="verify-and-enable-boot-diagnostics"></a>Verifiera och aktivera startdiagnostik

I följande exempel kan diagnostiktillägget `myVMDeployed` på den virtuella `myResourceGroup`datorn som nämns i resursgruppen :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn läser [du Felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med programanslutning på en Windows VM](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du använder Resource Manager finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).
