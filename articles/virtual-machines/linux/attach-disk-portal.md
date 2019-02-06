---
title: Anslut en datadisk till en Linux VM | Microsoft Docs
description: Använda portalen för att bifoga disken för nya eller befintliga data till en Linux-VM.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 5995c896f02720d82862895795e1e8d43f6bb226
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756477"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Använda portalen för att koppla en datadisk till en Linux VM 
Den här artikeln visar hur du kopplar både nya och befintliga diskar till en Linux-dator via Azure portal. Du kan också [ansluter en datadisk till en virtuell Windows-dator i Azure-portalen](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Innan du kopplar diskar till din virtuella dator, granskar du de här tipsen:

* Storleken på den virtuella datorn styr hur många datadiskar som du kan koppla. Mer information finns i [storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du vill använda Premium storage behöver du en virtuell dator DS-serien eller GS-serien. Du kan använda både Premium och Standard disks med dessa virtuella datorer. Premium storage är tillgängligt i vissa regioner. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar för virtuella Azure-datorer](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Diskar som är anslutna till virtuella datorer är faktiskt .vhd-filer som är lagrade i Azure. Mer information finns i [om diskar och virtuella hårddiskar för virtuella datorer](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* När du bifoga disken, måste du [ansluta till Linux VM att montera den nya disken](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Hitta den virtuella datorn
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På menyn till vänster klickar du på **virtuella datorer**.
3. Välj den virtuella datorn i listan.
4. Till virtuellt datorer kan i **Essentials**, klickar du på **diskar**.
   
    ![Öppna Diskinställningar](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Koppla en ny disk

1. På den **diskar** fönstret klickar du på **+ Lägg till datadisk**.
2. Klicka på den nedrullningsbara menyn för **namn** och välj **skapa disk**:

    ![Skapa Azure hanterade diskar](./media/attach-disk-portal/create-new-md.png)

3. Ange ett namn för din hanterade disk. Granska standardinställningarna, uppdatera efter behov och klicka sedan på **skapa**.
   
   ![Granska Diskinställningar](./media/attach-disk-portal/create-new-md-settings.png)

4. Klicka på **spara** att skapa den hantera disken och uppdatera VM-konfigurationen:

   ![Spara nya Azure Managed Disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. När Azure skapar disken och kopplar den till den virtuella datorn, den nya disken visas i den virtuella datorns diskinställningarna under **Datadiskar**. Eftersom hanterade diskar är en resurs på toppnivå, visas disken i roten för resursgruppen:

   ![Azure Managed Disk i resursgrupp](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
1. På den **diskar** fönstret klickar du på **+ Lägg till datadisk**.
2. Klicka på den nedrullningsbara menyn för **namn** att visa en lista över befintliga hanterade diskar som är tillgängliga för din Azure-prenumeration. Välj den hantera disken att ansluta:

   ![Bifoga befintlig Azure Managed Disk](./media/attach-disk-portal/select-existing-md.png)

3. Klicka på **spara** att bifoga den befintliga hantera disken och uppdatera VM-konfigurationen:
   
   ![Spara uppdateringar för Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. När Azure ansluter disken till den virtuella datorn, visas den i den virtuella datorns diskinställningarna under **Datadiskar**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ansluta till Linux VM att montera den nya disken
Att partitionera, formatera och montera den nya disken så att din Linux-VM kan använda den, SSH till den virtuella datorn. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). I följande exempel ansluter till en virtuell dator med offentliga DNS-posten för *mypublicdns.westus.cloudapp.azure.com* med användarnamnet *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

När du är ansluten till den virtuella datorn, är du redo att ansluta en disk. Ta först reda disk med hjälp av `dmesg` (vilken metod du använder för att identifiera den nya disken kan variera). I följande exempel används dmesg för att filtrera på *SCSI* diskar:

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

Här kan *sdc* är den disk som vi vill. 

### <a name="partition-a-new-disk"></a>Partitionera en ny disk
Om du använder en befintlig disk som innehåller data, kan du hoppa över att montera disken. Om du kopplar en ny disk kan behöva du partitionera disken.

Partitionera disken med `parted`om disken är 2 tebibyte (TiB) eller större måste du använda GPT partitionering, om det är under 2TiB, kan du använda antingen MBR eller GPT partitionering. Gör det primär disk på partition 1 och Godkänn andra standardinställningar. Följande exempel startar den `parted` på */dev/sdc*:

```bash
sudo parted /dev/sdc
```

Använd den `n` att lägga till en ny partition. I det här exemplet vi också välja `p` för en primär partition av och accepterar du resten av standardinställningarna. Utdata ska vara detsamma som i följande exempel:

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

Skriva ut partitionstabellen genom att skriva `p` och sedan använda `w` att skriva tabellen till hårddisken och avsluta. Utdata bör se ut ungefär så här:

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

Nu kan skapa ett filsystem på partitionen med den `mkfs` kommando. Ange din typ av filsystem och namnet på enheten. I följande exempel skapas en *ext4* filsystem på den */dev/sdc1* partition som skapades i föregående steg:

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
### <a name="mount-the-disk"></a>Montera disken
Skapa en katalog för att montera filsystemet med `mkdir`. I följande exempel skapas en katalog på */datadrive*:

```bash
sudo mkdir /datadrive
```

Använd `mount` att montera filsystemet. I följande exempel monterar den */dev/sdc1* partitionera till den */datadrive* monteringspunkt:

```bash
sudo mount /dev/sdc1 /datadrive
```

För att säkerställa att disken monteras automatiskt efter en omstart, måste du lägga till den */etc/fstab* fil. Det rekommenderas också starkt att UUID (universellt Unik identifierare) används i */etc/fstab* att referera till enheten i stället för bara namnet på enheten (t.ex, */dev/sdc1*). Om Operativsystemet upptäcker ett diskfel under Start, undviker med hjälp av UUID den felaktiga disken monteras på en viss plats. Återstående datadiskar sedan tilldelas dessa samma enhets-ID. Använd för att hitta UUID för den nya enheten i `blkid` verktyget:

```bash
sudo -i blkid
```

Utdata ser ut ungefär så här:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Felaktigt redigerar den **/etc/fstab** filen kan resultera i ett system som inte kan startas. Om du är osäker, finns det distribution dokumentationen för information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av/etc/fstab-filen har skapats innan du redigerar.

Därefter öppnar den */etc/fstab* filen i en textredigerare på följande sätt:

```bash
sudo vi /etc/fstab
```

I det här exemplet använder UUID-värdet för den */dev/sdc1* enheten som skapades i föregående steg och monteringspunkten */datadrive*. Lägg till följande rad i slutet av den */etc/fstab* fil:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Ta bort en datadisk senare utan att redigera fstab leda till den virtuella datorn inte kan starta. De flesta distributioner måste ange den *nofail* och/eller *nobootwait* fstab alternativ. De här alternativen kan ett system att starta även om disken inte kan montera när datorn startas. Mer information om dessa parametrar finns i dokumentationen för din distribution.
> 
> Den *nofail* alternativet ser du till att den virtuella datorn startar även om filsystemet är skadat eller disken finns inte vid start. Utan det här alternativet kan du stöta på beteende enligt beskrivningen i [det går inte att SSH till Linux VM på grund av FSTAB-fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP stöd för Linux i Azure
Vissa Linux-kernel stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Den här funktionen är främst användbart för standardlagring att meddela Azure som tagits bort sidor som inte längre är giltig och kan bara ta bort och kan spara pengar, om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i din Linux-VM. Som vanligt finns distributionen för den rekommenderade metoden:

* Använd den `discard` montera alternativ i */etc/fstab*, till exempel:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* I vissa fall kan den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab ska köras regelbundet:
  
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

## <a name="next-steps"></a>Nästa steg
Du kan också [ansluter en datadisk](add-disk.md) med Azure CLI.
