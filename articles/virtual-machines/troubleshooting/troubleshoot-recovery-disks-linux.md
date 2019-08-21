---
title: Använda en Linux felsöka virtuell dator med Azure CLI | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Linux-datorer genom att ansluta OS-disken till en virtuell återställnings dator med Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641060"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell dator för återställning med Azure CLI
Om din virtuella Linux-dator (VM) påträffar ett start-eller diskfel kan du behöva utföra fel söknings stegen på den virtuella hård disken. Ett vanligt exempel är en ogiltig post i `/etc/fstab` som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure CLI för att ansluta din virtuella hård disk till en annan virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den virtuella datorn som påverkas.
1. Ta en ögonblicks bild från den virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken.
1. Anslut och montera den nya OS-disken till en annan virtuell Linux-dator i fel söknings syfte.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den nya OS-disken.
1. Demontera och koppla från den nya OS-disken från den virtuella fel söknings datorn.
1. Ändra OS-disken för den berörda virtuella datorn.

Om du vill utföra de här fel söknings stegen måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

> [!Important]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [hanterad disk](../linux/managed-disks-overview.md). 

I följande exempel ersätter du parameter namn med dina egna värden. Exempel på parameter namn `myResourceGroup` är `myVM`och.

## <a name="determine-boot-issues"></a>Fastställa start problem
Granska de seriella utdata för att avgöra varför den virtuella datorn inte kan starta korrekt. Ett vanligt exempel är en ogiltig post i `/etc/fstab`, eller den underliggande virtuella hård disken som tas bort eller flyttas.

Hämta start loggarna med [AZ VM Boot-Diagnostics get-Boot-log](/cli/azure/vm/boot-diagnostics). I följande exempel hämtas serie resultatet från den virtuella datorn som `myVM` heter i resurs gruppen med `myResourceGroup`namnet:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Granska de seriella utdata för att avgöra varför den virtuella datorn inte kan starta. Om den seriella utmatningen inte ger någon indikation kan du behöva granska loggfilerna `/var/log` i när du har en virtuell hård disk ansluten till en felsöknings-VM.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

I följande exempel stoppas den virtuella `myVM` datorn med namnet från resurs `myResourceGroup`gruppen med namnet:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du bearbetar nästa steg.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Skapa en ögonblicks bild från den virtuella datorns OS-disk

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Den kan inte kopplas till en virtuell dator. I nästa steg ska vi skapa en disk från den här ögonblicks bilden. I följande exempel skapas en ögonblicks bild `mySnapshot` med namnet från operativ system disken för den virtuella datorn med namnet "myVM". 

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
## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicks bilden

Det här skriptet skapar en hanterad disk `newOSDisk` med namnet från ögonblicks bilden med namnet. `mysnapshot`  

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
Nu har du en kopia av den ursprungliga OS-disken. Du kan montera den här nya disken till en annan virtuell Windows-dator i fel söknings syfte.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Koppla den nya virtuella hård disken till en annan virtuell dator
För kommande steg använder du en annan virtuell dator i fel söknings syfte. Du ansluter disken till den här fel söknings datorn för att bläddra och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurations fel eller granska ytterligare program-eller systemloggfiler, till exempel. Välj eller skapa en annan virtuell dator som ska användas i fel söknings syfte.

Anslut den befintliga virtuella hård disken med [AZ VM unmanaged-disk Attach](/cli/azure/vm/unmanaged-disk). När du ansluter den befintliga virtuella hård disken anger du URI: n till disken som hämtades i `az vm show` föregående kommando. I följande exempel kopplas en befintlig virtuell hård disk till den virtuella fel söknings datorn med namnet `myVMRecovery` i resurs `myResourceGroup`gruppen:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>Montera den anslutna data disken

> [!NOTE]
> Följande exempel beskriver stegen som krävs på en virtuell Ubuntu-dator. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise Linux eller SUSE, kan logg filens platser `mount` och kommandon vara lite annorlunda. Se dokumentationen för din speciella distribution för lämpliga ändringar i kommandon.

1. SSH till din felsöka virtuella dator med hjälp av lämpliga autentiseringsuppgifter. Om den här disken är den första datadisk som är ansluten till den virtuella fel söknings datorn är `/dev/sdc`disken antagligen ansluten till. Använd `dmseg` för att Visa anslutna diskar:

    ```bash
    dmesg | grep SCSI
    ```

    Utdata ser ut ungefär så här:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    I föregående exempel är OS-disken på `/dev/sda` och den temporära disken som anges för varje virtuell dator finns på. `/dev/sdb` Om du hade flera data diskar bör de vara på `/dev/sdd`, `/dev/sde`och så vidare.

2. Skapa en katalog för att montera din befintliga virtuella hård disk. I följande exempel skapas en katalog med `troubleshootingdisk`namnet:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Om du har flera partitioner på din befintliga virtuella hård disk monterar du den nödvändiga partitionen. I följande exempel monteras den första primära partitionen på `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Bästa praxis är att montera data diskar på virtuella datorer i Azure med hjälp av den virtuella hård diskens UUID (Universal Unique Identifier). För det här korta fel söknings scenariot är det inte nödvändigt att montera den virtuella hård disken med UUID. Men under normal användning kan redigering `/etc/fstab` av virtuella hård diskar med enhets namn i stället för UUID leda till att den virtuella datorn inte kan starta.


## <a name="fix-issues-on-the-new-os-disk"></a>Åtgärda problem på den nya OS-disken
Med den befintliga virtuella hård disken monterad kan du nu utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-the-new-os-disk"></a>Demontera och koppla från den nya OS-disken
När dina fel har åtgärd ATS demonterar du och kopplar från den befintliga virtuella hård disken från den virtuella fel söknings datorn. Du kan inte använda den virtuella hård disken med någon annan virtuell dator förrän lånet som ansluter till den virtuella hård disken till den virtuella fel söknings datorn har släppts.

1. Från SSH-sessionen till din felsöka virtuella hård disk avmonterar du den befintliga virtuella hård disken. Ändra från den överordnade katalogen för monterings punkten först:

    ```bash
    cd /
    ```

    Demontera nu den befintliga virtuella hård disken. I följande exempel demonterar du enheten på `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Koppla nu från den virtuella hård disken från den virtuella datorn. Avsluta SSH-sessionen till din felsöka VM. Lista de anslutna data diskarna till din felsöka virtuella datorn med [AZ VM unmanaged-disk List](/cli/azure/vm/unmanaged-disk). I följande exempel visas de data diskar som är anslutna till den `myVMRecovery` virtuella datorn med namnet i `myResourceGroup`resurs gruppen med namnet:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Anteckna namnet på din befintliga virtuella hård disk. Till exempel är namnet på en disk med URI: n för **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** **myVHD**. 

    Koppla bort data disken från den virtuella datorn [AZ VM unmanaged-disk Detached](/cli/azure/vm/unmanaged-disk). Följande exempel kopplar från disken med namnet `myVHD` från den virtuella datorn med namnet `myVMRecovery` i `myResourceGroup` resurs gruppen:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra OS-disken för den berörda virtuella datorn

Du kan använda Azure PowerShell för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn.

Det här exemplet stoppar den virtuella `myVM` datorn med namnet och tilldelar disken namnet `newOSDisk` som den nya OS-disken. 

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

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure-dator](troubleshoot-ssh-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Linux-dator](troubleshoot-app-connection.md).

