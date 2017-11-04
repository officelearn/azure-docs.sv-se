---
title: "Lägg till en disk i Linux VM som använder Azure CLI | Microsoft Docs"
description: "Lär dig att lägga till en beständig disk till din Linux VM med Azure CLI 1.0 och 2.0."
keywords: "Linux-dator, Lägg till resurs-disk"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9db7d300b745001906bdc38769dcbe6e4d7c7b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-disk-to-a-linux-vm"></a>Lägg till en disk till en virtuell Linux-dator
Den här artikeln visar hur du kopplar en beständig disk till den virtuella datorn så att du kan behålla data – även om den virtuella datorn etableras på grund av underhåll eller ändrar storlek på. 


## <a name="use-managed-disks"></a>Använda hanterade diskar
Azure-hanterade diskar förenklar Diskhantering för virtuella Azure-datorer genom att hantera de lagringskonton som är associerade med VM-diskarna. Du behöver bara ange typen (Standard eller Premium) och storleken på disken som du behöver och Azure skapar och hanterar disken åt dig. Mer information finns i [översikt för hanterade diskar](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Koppla en ny disk till en virtuell dator
Om du vill skapa en ny disk på den virtuella datorn, använder den [az vm disk bifoga](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) kommandot med de `--new` parameter. Om den virtuella datorn finns i en zon för tillgänglighet, skapas disken automatiskt i samma zon som den virtuella datorn. Mer information finns i [översikt av tillgänglighet zoner](../../availability-zones/az-overview.md). I följande exempel skapas en disk med namnet *myDataDisk* som *50*Gb i storlek:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk 
I många fall. Du kan koppla diskar som redan har skapats. Om du vill koppla en befintlig disk, disk-ID: t och skickar ID som den [az vm disk bifoga](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) kommando. Följande Exempelfrågor för en disk med namnet *myDataDisk* i *myResourceGroup*, sedan kopplas till den virtuella datorn med namnet *myVM*:

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

Resultatet ser ut ungefär så här (du kan använda den `-o table` alternativet alla kommandot för att formatera utdata i):

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Använda ohanterade diskar
Ohanterad diskar kräver ytterligare belastningen för att skapa och hantera de underliggande lagringskontona. Ohanterad diskar skapas i samma lagringskonto som OS-disk. Skapa och koppla en ohanterad disk, Använd den [az vm ohanterad disk bifoga](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach) kommando. I följande exempel bifogar en *50*GB ohanterade disk till den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ansluta till Linux-VM för att montera den nya disken
Att partitionera, formatera och montera den nya disken så att din Linux VM kan använda den SSH till den virtuella Azure-datorn. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). I följande exempel ansluter till en virtuell dator med offentliga DNS-posten för *mypublicdns.westus.cloudapp.azure.com* med användarnamnet *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

När du är ansluten till den virtuella datorn, är du redo att ansluta en disk. Ta först en disk med hjälp av `dmesg` (vilken metod du använder för att identifiera den nya disken kan variera). I följande exempel används dmesg för att filtrera på *SCSI* diskar:

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

Här, *sdc* är en disk som vi vill. Partitionera disk med `fdisk`, att den primära disken på partitionen 1 och Godkänn andra standardinställningarna. Följande exempel startar den `fdisk` på */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Utdata ser ut ungefär så här:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Skapa en partition genom att skriva `p` i Kommandotolken på följande sätt:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Nu kan skriva ett filsystem till partitionen med det `mkfs` kommando. Ange vilken typ av filsystem och namnet på enheten. I följande exempel skapas en *ext4* filsystemet på den */dev/sdc1* partition som skapades i föregående steg:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Utdata ser ut ungefär så här:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Nu ska du skapa en katalog för att montera filsystemet med `mkdir`. I följande exempel skapas en katalog på */datadrive*:

```bash
sudo mkdir /datadrive
```

Använd `mount` att montera filsystemet. I följande exempel monterar den */dev/sdc1* partitions till den */datadrive* monteringspunkt:

```bash
sudo mount /dev/sdc1 /datadrive
```

För att säkerställa att enheten är på nytt automatiskt efter en omstart, måste den läggas till i */etc/fstab* fil. Även rekommenderas att UUID (universellt Unique IDentifier) används i */etc/fstab* att referera till enheten i stället för bara namnet på en enhet (t.ex, */dev/sdc1*). Om Operativsystemet upptäcker ett diskfel under Start, undviker med hjälp av UUID den felaktiga disken monteras på en viss plats. Återstående datadiskar sedan tilldelas de samma enhets-ID. Använd för att hitta UUID för den nya enheten i `blkid` verktyget:

```bash
sudo -i blkid
```

Utdata liknar följande exempel:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Felaktigt redigering av **/etc/fstab** filen kan resultera i ett system som inte kan startas. Om du är osäker, i den distribution dokumentationen finns information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av /etc/fstab-filen har skapats innan du redigerar.

Därefter öppnar den */etc/fstab* filen i en textredigerare på följande sätt:

```bash
sudo vi /etc/fstab
```

I det här exemplet använder vi UUID-värdet för den */dev/sdc1* enheten som skapades i föregående steg och monteringspunkt av */datadrive*. Lägg till följande rad i slutet av den */etc/fstab* fil:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Tar bort en datadisk senare utan att redigera fstab leda till den virtuella datorn inte startar. De flesta distributioner måste ange den *nofail* och/eller *nobootwait* fstab alternativ. Dessa alternativ kan ett system för att starta även om disken inte montera vid start. Se dokumentationen för din distribution för mer information om dessa parametrar.
> 
> Den *nofail* alternativet ser du till att den virtuella datorn startar även om filsystemet är skadat eller disken finns inte vid start. Utan det här alternativet, du kan stöta på problem som beskrivs i [kan SSH för Linux VM på grund av FSTAB fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIMNING/UNMAP stöd för Linux i Azure
Vissa Linux kärnor stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Den här funktionen är i första hand användbart i standardlagring att meddela Azure som bort sidor som inte längre är giltig och kan tas bort och kan spara pengar om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i Linux-VM. Som vanligt, kontakta din distribution för den rekommenderade metoden:

* Använd den `discard` montera alternativ i */etc/fstab*, till exempel:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* I vissa fall kan den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab att köras regelbundet:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nästa steg
* Kom ihåg att den nya disken inte är tillgänglig för den virtuella datorn om startas om du skriver informationen till din [fstab](http://en.wikipedia.org/wiki/Fstab) fil.
* För att säkerställa ditt Linux VM är korrekt konfigurerad, granska den [optimera prestanda ditt Linux-datorer](optimization.md) rekommendationer.
* Expandera lagringskapaciteten genom att lägga till ytterligare diskar och [konfigurera RAID](configure-raid.md) för ytterligare prestanda.

