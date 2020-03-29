---
title: Expandera virtuella hårddiskar på en Virtuell Linux-dator
description: Lär dig hur du expanderar virtuella hårddiskar på en virtuell Linux-dator med Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945189"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandera virtuella hårddiskar på en virtuell Linux-dator med Azure CLI

I den här artikeln beskrivs hur du expanderar hanterade diskar för en virtuell Linux-dator (VM) med Azure CLI. Du kan [lägga till datadiskar](add-disk.md) för att tillhandahålla ytterligare lagringsutrymme, och du kan också expandera en befintlig datadisk. Standardstorleken för virtuell hårddisk för operativsystemet (OS) är vanligtvis 30 GB på en Virtuell Linux-dator i Azure. 

> [!WARNING]
> Kontrollera alltid att filsystemet är felfritt, att diskpartitionstabelltypen stöder den nya storleken och se till att dina data säkerhetskopieras innan du utför diskstorleksåtgärder. Mer information finns i [Säkerhetskopiera virtuella Linux-datorer i Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandera en Azure-hanterad disk
Se till att du har den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och är inloggad på ett Azure-konto med hjälp av [az login](/cli/azure/reference-index#az-login).

Den här artikeln kräver en befintlig virtuell dator i Azure med minst en datadisk ansluten och förberedd. Om du inte redan har en virtuell dator som du kan använda läser du [Skapa och förbereda en virtuell dator med datadiskar](tutorial-manage-disks.md#create-and-attach-disks).

I följande exempel ersätter du exempelparameternamn som *myResourceGroup* och *myVM* med dina egna värden.

1. Åtgärder på virtuella hårddiskar kan inte utföras med den virtuella datorn som körs. Dela ut din virtuella dator med [az vm deallocate](/cli/azure/vm#az-vm-deallocate). I följande exempel frigörs den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Den virtuella datorn måste frigöras för att expandera den virtuella hårddisken. Om du `az vm stop` stoppar den virtuella datorn med frigörs inte beräkningsresurserna. Om du vill `az vm deallocate`frigöra beräkningsresurser använder du .

1. Visa en lista över hanterade diskar i en resursgrupp med [az-disklista](/cli/azure/disk#az-disk-list). I följande exempel visas en lista över hanterade diskar i resursgruppen *myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expandera den disk som krävs med [az-diskuppdatering](/cli/azure/disk#az-disk-update). I följande exempel utökas den hanterade disken med namnet *myDataDisk* till *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > När du expanderar en hanterad disk avrundas den uppdaterade storleken uppåt till närmaste hanterade diskstorlek. En tabell över tillgängliga hanterade diskstorlekar och nivåer finns i [Azure Managed Disks Overview - Pricing and Billing](../windows/managed-disks-overview.md).

1. Starta den virtuella datorn med [az vm start](/cli/azure/vm#az-vm-start). I följande exempel startar den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup:*

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandera en diskpartition och ett filsystem
Om du vill använda en utökad disk expanderar du den underliggande partitionen och filsystemet.

1. SSH till din virtuella dator med lämpliga autentiseringsuppgifter. Du kan se den offentliga IP-adressen för din virtuella dator med [az vm show:](/cli/azure/vm#az-vm-show)

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Expandera den underliggande partitionen och filsystemet.

    a. Om disken redan är monterad avmonterar du den:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Används `parted` för att visa diskinformation och ändra storlek på partitionen:

    ```bash
    sudo parted /dev/sdc
    ```

    Visa information om den `print`befintliga partitionslayouten med . Utdata liknar följande exempel, som visar att den underliggande disken är 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expandera partitionen `resizepart`med . Ange partitionsnummer, *1*och en storlek för den nya partitionen:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Om du `quit`vill avsluta anger du .

1. Kontrollera partitionens konsekvens med `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ändra storlek på filsystemet med: `resize2fs`

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montera partitionen på önskad plats, till exempel `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Om du vill kontrollera att datadisken har ändrats använder du `df -h`. Följande exempelutdata visar att dataenheten */dev/sdc1* nu är 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Nästa steg
* Om du behöver ytterligare lagringsutrymme kan du också [lägga till datadiskar i en Virtuell Linux.](add-disk.md) 
* Mer information om diskkryptering finns i [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md).
