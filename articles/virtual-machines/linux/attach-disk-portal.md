---
title: Koppla en datadisk till en Virtuell Linux-dator
description: Använd portalen för att koppla ny eller befintlig datadisk till en Virtuell Linux-dator.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969362"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Använda portalen för att koppla en datadisk till en Virtuell Linux-dator 
Den här artikeln visar hur du ansluter både nya och befintliga diskar till en virtuell Linux-dator via Azure-portalen. Du kan också [koppla en datadisk till en Windows-virtuell dator i Azure-portalen](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Innan du bifogar diskar till den virtuella datorn läser du följande tips:

* Storleken på den virtuella datorn styr hur många datadiskar du kan koppla. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Diskar som är anslutna till virtuella datorer är i själva verket VHD-filer som lagras i Azure. Mer information finns i vår [Introduktion till hanterade diskar](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* När du har anslutit disken måste du [ansluta till Linux-vm för att montera den nya disken](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Hitta den virtuella datorn
1. Gå till [Azure-portalen](https://portal.azure.com/) för att hitta den virtuella datorn. Sök efter och välj **Virtuella datorer**.
2. Välj den virtuella datorn i listan.
3. Välj **Diskar**under **Inställningar**i sidpanelen **För virtuella datorer** .
   
    ![Öppna diskinställningar](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Bifoga en ny disk

1. Klicka på **+ Lägg till datadisk i**fönstret **Diskar** .
2. Klicka på den nedrullningsbara menyn för **Namn** och välj **Skapa disk:**

    ![Skapa Azure-hanterad disk](./media/attach-disk-portal/create-new-md.png)

3. Ange ett namn på den hanterade disken. Granska standardinställningarna, uppdatera efter behov och klicka sedan på **Skapa**.
   
   ![Granska diskinställningar](./media/attach-disk-portal/create-new-md-settings.png)

4. Klicka på **Spara** om du vill skapa den hanterade disken och uppdatera vm-konfigurationen:

   ![Spara ny Hanterad Azure-disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. När Azure har skapat disken och bifogat den till den virtuella datorn visas den nya disken i den virtuella datorns diskinställningar under **Datadiskar**. Eftersom hanterade diskar är en resurs på den högsta nivån visas disken i resursgruppens rot:

   ![Hanterad azure-disk i resursgrupp](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
1. Klicka på **+ Lägg till datadisk i**fönstret **Diskar** .
2. Klicka på den nedrullningsliga menyn för **Namn** om du vill visa en lista över befintliga hanterade diskar som är tillgängliga för din Azure-prenumeration. Välj den hanterade disken som ska bifogas:

   ![Koppla befintlig Azure-hanterad disk](./media/attach-disk-portal/select-existing-md.png)

3. Klicka på **Spara** om du vill bifoga den befintliga hanterade disken och uppdatera vm-konfigurationen:
   
   ![Spara Uppdateringar av Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. När Azure har bifogat disken till den virtuella datorn visas den i den virtuella datorns diskinställningar under **Datadiskar**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Anslut till Linux-vm för att montera den nya disken
För att partitionera, formatera och montera din nya disk så att din Linux VM kan använda den, SSH i din virtuella dator. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). Följande exempel ansluter till en virtuell dator med den offentliga DNS-posten *för mypublicdns.westus.cloudapp.azure.com* med användarnamnet *azureuser:* 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

När du är ansluten till den virtuella datorn är du redo att ansluta en disk. Först, hitta disken med `dmesg` (den metod du använder för att upptäcka din nya disk kan variera). I följande exempel används dmesg för att filtrera på *SCSI-diskar:*

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

Här är *SDC* den disk som vi vill ha. 

### <a name="partition-a-new-disk"></a>Partitionera en ny disk
Om du använder en befintlig disk som innehåller data går du till att montera disken. Om du ansluter en ny disk måste du partitionera disken.

> [!NOTE]
> Vi rekommenderar att du använder de senaste versionerna av fdisk eller parted som är tillgängliga för din distribution.

Partitionera disken med `fdisk`. Om diskstorleken är 2 tebibyte (TiB) eller större måste du `parted` använda GPT-partitionering, du kan använda för att utföra GPT-partitionering. Om diskstorleken är under 2TiB kan du använda antingen MBR- eller GPT-partitionering. Gör den till en primär disk på partition 1 och acceptera de andra standardinställningarna. I följande exempel `fdisk` startar processen på */dev/sdc:*

```bash
sudo fdisk /dev/sdc
```

Använd kommandot `n` för att lägga till en ny partition. I det här exemplet `p` väljer vi också för en primär partition och accepterar resten av standardvärdena. Utdatan blir något som liknar följande exempel:

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

Skriv ut partitionstabellen genom att `p` skriva och använd `w` sedan för att skriva tabellen till disk och avsluta. Utdata bör se ut ungefär som följande exempel:

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

Nu skriver du ett filsystem `mkfs` till partitionen med kommandot. Ange filsystemtypen och enhetsnamnet. I följande exempel skapas ett *ext4-filsystem* på partitionen */dev/sdc1* som skapades i föregående steg:

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

#### <a name="alternate-method-using-parted"></a>Alternativ metod med hjälp av skildes
Den fdisk verktyget behöver interaktiv input och därmed är inte idealisk för användning inom automation skript. Det [skildes](https://www.gnu.org/software/parted/) verktyget kan skript och därmed lämpar sig bättre i automatisering scenarier. Det avdelade verktyget kan användas för att partitionera och formatera en datadisk. För genomgången nedan använder vi en ny datadisk /dev/sdc [XFS](https://xfs.wiki.kernel.org/) och formaterar den med XFS-filsystemet.
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Som vi ser ovan använder vi [verktyget partprobe](https://linux.die.net/man/8/partprobe) för att se till att kärnan omedelbart är medveten om den nya partitionen och filsystemet. Om du inte använder partprobe kan kommandona blkid eller lslbk inte returnera UUID för det nya filsystemet omedelbart.

### <a name="mount-the-disk"></a>Montera disken
Skapa en katalog för att `mkdir`montera filsystemet med . I följande exempel skapas en katalog på */datadrive:*

```bash
sudo mkdir /datadrive
```

Används `mount` för att sedan montera filsystemet. I följande exempel monteras *partitionen /dev/sdc1* på *monteringspunkten /datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

För att säkerställa att enheten monteras automatiskt efter en omstart måste den läggas till i filen */etc/fstab.* Det rekommenderas också starkt att UUID (Universally Unique IDentifier) används i */etc/fstab* för att referera till enheten i stället för bara enhetsnamnet (till exempel */dev/sdc1*). Om operativsystemet upptäcker ett diskfel vid start och använder UUID undviker du att den felaktiga disken monteras på en viss plats. Återstående datadiskar tilldelas sedan samma enhets-ID:n. Du kan hitta UUID för den nya enheten med verktyget `blkid`:

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
> Felaktig redigering av **/etc/fstab-filen** kan resultera i ett system som inte kan booterbara. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Det rekommenderas också att en säkerhetskopia av filen /etc/fstab skapas före redigering.

Öppna sedan filen */etc/fstab* i en textredigerare enligt följande:

```bash
sudo vi /etc/fstab
```

I det här exemplet använder du UUID-värdet för enheten */dev/sdc1* som skapades i föregående steg och monteringspunkten för */datadrive*. Lägg till följande rad i slutet av *filen /etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
När du är klar sparar du filen */etc/fstab* och startar om systemet.
> [!NOTE]
> Senare tar du bort en datadisk utan att redigera fstab kan orsaka att den virtuella datorn inte startar. De flesta distributioner ger antingen *nofail* och / eller *nobootwait* fstab alternativ. Dessa alternativ gör det möjligt för ett system att starta även om disken inte kan monteras vid uppstart. Mer information om dessa parametrar finns i dokumentationen till distributionen.
> 
> Alternativet *nofail* säkerställer att den virtuella datorn startar även om filsystemet är skadat eller om disken inte finns vid start. Utan det här alternativet kan du stöta på beteende som beskrivs i [Kan inte SSH till Linux VM på grund av FSTAB-fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP-stöd för Linux i Azure
Vissa Linux-kärnor stöder TRIM/UNMAP-åtgärder för att ignorera oanvända block på disken. Den här funktionen är främst användbar i standardlagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan ignoreras och kan spara pengar om du skapar stora filer och sedan ta bort dem.

Det finns två sätt att aktivera TRIM-stöd i din Virtuella Linux-dator. Som vanligt, konsultera din distribution för den rekommenderade metoden:

* Använd `discard` monteringsalternativet i */etc/fstab,* till exempel:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* I vissa fall `discard` kan alternativet få prestandakonsekvenser. Du kan också köra `fstrim` kommandot manuellt från kommandoraden eller lägga till det i crontab för att köras regelbundet:
  
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
Du kan också [bifoga en datadisk](add-disk.md) med Azure CLI.
