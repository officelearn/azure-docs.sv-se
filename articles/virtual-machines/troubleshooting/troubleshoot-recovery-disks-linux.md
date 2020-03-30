---
title: Använd en virtuell linuxfelsökning med Azure CLI | Microsoft-dokument
description: Lär dig hur du felsöker Problem med Linux VM genom att ansluta OS-disken till en återställnings-VM med Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73620568"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Felsöka en virtuell Linux-dator genom att koppla OS-disken till en återställnings-VM med Azure CLI
Om din virtuella Linux-dator (VM) stöter på ett start- eller diskfel kan du behöva utföra felsökningssteg på själva den virtuella hårddisken. Ett vanligt exempel skulle vara `/etc/fstab` en ogiltig post i som förhindrar att den virtuella datorn kan starta. I den här artikeln beskrivs hur du använder Azure CLI för att ansluta din virtuella hårddisk till en annan Virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den berörda virtuella datorn.
1. Ta en ögonblicksbild från OS-disken för den virtuella datorn.
1. Skapa en disk från ögonblicksbilden av OS-disken.
1. Koppla och montera den nya OS-disken på en annan Virtuell Linux-dator för felsökning.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör verktyg för att åtgärda problem på den nya OS-disken.
1. Avmontera och koppla bort den nya OS-disken från felsökningsdatorn.
1. Ändra OS-disken för den berörda virtuella datorn.

För att kunna utföra de här felsökningsstegen behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

> [!Important]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [Hanterad disk](../linux/managed-disks-overview.md). 

I följande exempel ersätter du parameternamn med `myResourceGroup` `myVM`dina egna värden, till exempel och .

## <a name="determine-boot-issues"></a>Ta reda på startproblem
Undersök serieutdata för att ta reda på varför den virtuella datorn inte kan starta korrekt. Ett vanligt exempel är `/etc/fstab`en ogiltig post i eller den underliggande virtuella hårddisken som tas bort eller flyttas.

Få startloggar med [az vm boot-diagnostik get-boot-log](/cli/azure/vm/boot-diagnostics). I följande exempel hämtar serieutdata `myVM` från den `myResourceGroup`virtuella datorn som heter i resursgruppen :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Granska den seriella utdata för att avgöra varför den virtuella datorn inte startar. Om den seriella utdata inte ger någon indikation kan `/var/log` du behöva granska loggfiler när du har den virtuella hårddisken ansluten till en felsöknings-VM.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

I följande exempel stoppas den virtuella datorn som namnges `myVM` från resursgruppen: `myResourceGroup`

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Ta en ögonblicksbild från OS-disken för den berörda virtuella datorn

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. Den kan inte kopplas till en virtuell dator. I nästa steg skapar vi en disk från den här ögonblicksbilden. I följande exempel skapas `mySnapshot` en ögonblicksbild med namn från OS-disken på den virtuella datorn med namnet "myVM". 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicksbilden

Det här skriptet skapar `myOSDisk` en hanterad `mySnapshot`disk med namn från ögonblicksbilden med namnet .  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Om resursgruppen och källögonblicksbilden inte finns i samma region visas felet "Resurs `az disk create`hittades inte" när du kör . I det här fallet `--location <region>` måste du ange att disken ska skapas i samma region som källögonblicksbilden.

Nu har du en kopia av den ursprungliga OS-disken. Du kan montera den nya disken på en annan Virtuell Windows-dator för felsökning.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Koppla den nya virtuella hårddisken till en annan virtuell dator
För de följande stegen använder du en annan virtuell dator för felsökning. Du kan koppla disken till den här felsökningsdatorn för att bläddra bland och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program- eller systemloggfiler.

Det här skriptet bifogar disken `myNewOSDisk` till den virtuella datorn: `MyTroubleshootVM`

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Montera den bifogade datadisken

> [!NOTE]
> I följande exempel beskrivs de steg som krävs på en virtuell Ubuntu-dator. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise `mount` Linux eller SUSE, kan loggfilens platser och kommandon vara lite annorlunda. Se dokumentationen för din specifika distribution för lämpliga ändringar i kommandon.

1. SSH till din felsökning VM med lämpliga autentiseringsuppgifter. Om den här disken är den första datadisken som är `/dev/sdc`ansluten till den virtuella felsökningsdatorn är disken troligen ansluten till . Används `dmesg` för att visa bifogade diskar:

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

    I föregående exempel är OS-disken på `/dev/sda` och den temporära disken som tillhandahålls för varje virtuell dator finns på `/dev/sdb`. Om du hade flera datadiskar `/dev/sdd`ska `/dev/sde`de vara på , och så vidare.

2. Skapa en katalog för att montera den befintliga virtuella hårddisken. I följande exempel skapas `troubleshootingdisk`en katalog med namnet :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Om du har flera partitioner på den befintliga virtuella hårddisken monterar du den nödvändiga partitionen. I följande exempel monteras den `/dev/sdc1`första primära partitionen på:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Bästa praxis är att montera datadiskar på virtuella datorer i Azure med hjälp av den universellt unika identifieraren (UUID) på den virtuella hårddisken. För det här korta felsökningsscenariot är det inte nödvändigt att montera den virtuella hårddisken med UUID. Vid normal användning kan `/etc/fstab` dock redigering för att montera virtuella hårddiskar med enhetsnamn i stället för UUID leda till att den virtuella datorn inte startar.


## <a name="fix-issues-on-the-new-os-disk"></a>Åtgärda problem på den nya OS-disken
Med den befintliga virtuella hårddisken monterad kan du nu utföra alla underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-the-new-os-disk"></a>Avmontera och koppla från den nya OS-disken
När dina fel har lösts avmonterar och tar du bort den befintliga virtuella hårddisken från den virtuella felsökningsdatorn. Du kan inte använda den virtuella hårddisken med någon annan virtuell dator förrän lånet som kopplar den virtuella hårddisken till felsökningsdatorn släpps.

1. Från SSH-sessionen till den virtuella felsökningsdatorn avmonterar du den befintliga virtuella hårddisken. Byt ut från den överordnade katalogen för monteringspunkten först:

    ```bash
    cd /
    ```

    Nu avmontera den befintliga virtuella hårddisken. I följande exempel avmonteras `/dev/sdc1`enheten på :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nu koppla den virtuella hårddisken från den virtuella datorn. Avsluta SSH-sessionen till den virtuella felsökningsdatorn:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra OS-disken för den berörda virtuella datorn

Du kan använda Azure CLI för att byta os-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn.

Det här exemplet `myVM` stoppar den virtuella `myNewOSDisk` datorn som namnges och tilldelar disken som namnges som den nya OS-disken.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn läser [du Felsöka SSH-anslutningar till en Azure VM](troubleshoot-ssh-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med programanslutning på en Virtuell Linux.](troubleshoot-app-connection.md)

