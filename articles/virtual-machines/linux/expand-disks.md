---
title: Expandera virtuella hårddiskar på en Linux-VM i Azure | Microsoft Docs
description: Lär dig hur du expanderar virtuella hårddiskar på en Linux VM med Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4113d582647b5bea86980824714936d24dafc870
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511152"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandera virtuella hårddiskar på en Linux VM med Azure CLI

Den här artikeln beskriver hur du expanderar hanterade diskar för en Linux-dator (VM) med Azure CLI. Du kan [lägga till datadiskar](add-disk.md) för att tillhandahålla ytterligare lagringsutrymme utrymme, och du kan också expandera en befintlig datadisk. Standardstorleken för virtuell hårddisk för operativsystemet (OS) är vanligtvis 30 GB på en Linux-VM i Azure. 

> [!WARNING]
> Se alltid till att ditt filsystem är i felfritt tillstånd, tabellen för disk partitionstypen kommer stöd för den nya storleken och se till att dina data har säkerhetskopierats innan du utför åtgärder för storleksändring av disk. Mer information finns i [säkerhetskopiera virtuella Linux-datorer i Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandera en Azure-hanterad Disk
Se till att du har senast [Azure CLI](/cli/azure/install-az-cli2) installerad och har loggat in på en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az-login).

Den här artikeln kräver en befintlig virtuell dator i Azure med minst en datadisk som är anslutna och förberett. Om du inte redan har en virtuell dator som du kan använda finns i [skapa och Förbered en virtuell dator med datadiskar](tutorial-manage-disks.md#create-and-attach-disks).

I följande exempel ersätter exempel parameternamn som *myResourceGroup* och *myVM* med dina egna värden.

1. Det går inte att utföra åtgärder på virtuella hårddiskar med den virtuella datorn körs. Frigör den virtuella datorn med [az vm deallocate](/cli/azure/vm#az-vm-deallocate). I följande exempel bort den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Den virtuella datorn måste frigöras för att expandera den virtuella hårddisken. Stoppa den virtuella datorn med `az vm stop` inte släpptes beräkningsresurser. Använd om du vill frigöra beräkningsresurser `az vm deallocate`.

1. Visa en lista över hanterade diskar i en resursgrupp med [az disk list](/cli/azure/disk#az-disk-list). I följande exempel visar en lista över hanterade diskar i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expandera krävs disken med [az disk update](/cli/azure/disk#az-disk-update). Följande exempel utvecklar hanterad disk med namnet *myDataDisk* till *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > När du expanderar en hanterad disk är uppdaterade storleken avrundas uppåt till närmaste hanterade diskens storlek. En tabell över tillgängliga hanterade diskstorlekar och nivåerna finns i [Azure översikten över Managed Disks - priser och fakturering](../windows/managed-disks-overview.md).

1. Starta den virtuella datorn med [az vm start](/cli/azure/vm#az-vm-start). Följande exempel startar den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandera en diskpartition och filsystem
Om du vill använda en utökad disk, expanderar du den underliggande partition och filsystem.

1. SSH till den virtuella datorn med rätt autentiseringsuppgifter. Du kan se den offentliga IP-adressen för den virtuella datorn med [az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Expandera den underliggande partition och filsystem.

    a. Om disken redan är monterad demontera den:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Använd `parted` att visa information om och ändra storlek på partitionen:

    ```bash
    sudo parted /dev/sdc
    ```

    Visa information om befintliga partitionslayout med `print`. Utdata liknar följande exempel som visar den underliggande disken är 215 GB:

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

    c. Expandera partitionen med `resizepart`. Ange partitionsnumret *1*, och en storlek för den nya partitionen:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Om du vill avsluta, ange `quit`.

1. Med den partition som storlek, kontrollera partition konsekvens med `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ändra storlek på filsystemet med `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montera partitionen till önskad plats, till exempel `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Använd för att kontrollera storleken har datadisken `df -h`. Följande Exempelutdata visar dataenheten */dev/sdc1* är nu 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Nästa steg
* Om du behöver ytterligare lagringsutrymme, kan du också [lägga till datadiskar i en Linux VM](add-disk.md). 
* Läs mer om diskkryptering [kryptera diskar på en Linux VM med Azure CLI](encrypt-disks.md).
