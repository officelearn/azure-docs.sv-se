---
title: Ändra storlek på en OS-disk som har en GPT-partition | Microsoft Docs
description: Den här artikeln innehåller anvisningar om hur du ändrar storlek på en OS-disk som har en GPT-partition.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: baa260e911673ea99b292ab5dc9895840d0098ef
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340342"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ändra storlek på en OS-disk som har en GPT-partition

> [!NOTE]
> Det här scenariot gäller endast för OS-diskar som har GPT-partitioner (GUID Partition Table).

Den här artikeln beskriver hur du ökar storleken på en OS-disk som har en GPT-partition i Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identifiera om OS-disken har en MBR-eller GPT-partition

Använd `parted` kommandot för att identifiera om diskpartitionen har skapats med antingen en Master Boot Record-partition (MBR) eller en GPT-partition.

### <a name="mbr-partition"></a>MBR-partition

I följande utdata visar **partitionstabellen** värdet **MSDOS**. Det här värdet identifierar en MBR-partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-partition

I följande utdata visar **partitionstabellen** ett **GPT** -värde. Det här värdet identifierar en GPT-partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Om din virtuella dator har en GPT-partition på din OS-disk ökar du storleken på operativ system disken.

## <a name="increase-the-size-of-the-os-disk"></a>Öka storleken på operativ system disken

Följande instruktioner gäller för Linux-godkända distributioner.

> [!NOTE]
> Innan du fortsätter kan du göra en säkerhets kopia av den virtuella datorn eller ta en ögonblicks bild av din OS-disk.

### <a name="ubuntu"></a>Ubuntu

Öka storleken på operativ system disken i Ubuntu 16. x och 18. x:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta om den virtuella datorn och logga sedan in på den virtuella datorn som en **rot** användare.
1. Kontrol lera att operativ system disken nu visar en större fil system storlek.

Som du ser i följande exempel har OS-disken ändrat storlek från portalen till 100 GB. **/Dev/sda1** -filsystemet som är monterat **/** nu visar 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

För att öka storleken på operativ system disken i SUSE 12 SP4, SUSE SLES 12 för SAP, SUSE SLES 15 och SUSE SLES 15 för SAP:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta om den virtuella datorn.

Utför följande steg när den virtuella datorn har startats om:

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av följande kommando:

   ```
   # sudo -i
   ```

1. Använd följande kommando för att installera **growpart** -paketet som ska användas för att ändra storlek på partitionen:

   ```
   # zypper install growpart
   ```

1. Använd `lsblk` kommandot för att hitta partitionen som är monterad i fil roten ("/"). I det här fallet ser vi att partition 4 av enhets SDA är monterad på/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Ändra storlek på den nödvändiga partitionen med `growpart` kommandot med hjälp av partitionsnumret som finns i föregående steg.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Kör `lsblk` kommandot igen för att kontrol lera om partitionen har ökat.

   Följande utdata visar att **/dev/sda4** -partitionen har storleksändrats till 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifiera typ av fil system på OS-disken genom att använda `lsblk` kommandot med `-f` flaggan:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Använd lämpliga kommandon för att ändra storlek på fil systemet baserat på typ av fil system.
   
   Använd följande kommando för **xfs** :
   
   ```
   #xfs_growfs /
   ```
   
   Exempel på utdata:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Använd följande kommando för **ext4** :
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Kontrol lera den ökade fil system storleken för **DF** genom att använda följande kommando:
   
   ```
   #df -Thl
   ```
   
   Exempel på utdata:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   I föregående exempel kan vi se att fil system storleken för operativ system disken har ökat.

### <a name="rhel-lvm"></a>RHEL LVM

Öka storleken på operativ system disken i RHEL 7. x med LVM:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta den virtuella datorn.

Utför följande steg när den virtuella datorn har startats om:

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av följande kommando:
 
   ```
   #sudo su
   ```

1. Installera **gptfdisk** -paketet, vilket krävs för att öka storleken på operativ system disken.

   ```
   #yum install gdisk -y
   ```

1. Kör följande kommando för att se den största tillgängliga sektorn på disken:

   ```
   #sgdisk -e /dev/sda
   ```

1. Ändra storlek på partitionen utan att ta bort den med hjälp av följande kommando. Det **delade** kommandot har ett alternativ med namnet **resizepart** för att ändra storlek på partitionen utan att ta bort den. Talet 4 efter **resizepart** indikerar storleks ändring av den fjärde partitionen.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Kör följande kommando för att kontrol lera att partitionen har ökat:

   ```
   #lsblk
   ```

   Följande utdata visar att **/dev/sda4** -partitionen har storleksändrats till 99 GB.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Använd följande kommando för att ändra storlek på den fysiska volymen (PV):

   ```
   #pvresize /dev/sda4
   ```

   Följande utdata visar att PV har storleksändrats till 99,02 GB.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. I följande exempel ändras storleken på **/dev/mapper/rootvg-rootlv** från 2 GB till 12 GB (en ökning på 10 GB) genom följande kommando. Det här kommandot ändrar även storlek på fil systemet.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Exempel på utdata:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Kontrol lera om **/dev/mapper/rootvg-rootlv** har en större fil system storlek genom att använda följande kommando:

   ```
   #df -Th /
   ```

   Exempel på utdata:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Om du vill använda samma procedur för att ändra storlek på en annan logisk volym ändrar du namnet på **LV** i steg 7.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>Ta alltid en ögonblicks bild av den virtuella datorn innan du ökar storleken på operativ system disken.

Öka storleken på operativ system disken i RHEL med RAW-partition:

Stoppa den virtuella datorn.
Öka storleken på operativ system disken från portalen.
Starta den virtuella datorn.
Utför följande steg när den virtuella datorn har startats om:

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av följande kommando:
 
   ```
   sudo su
   ```

1. Installera **gptfdisk** -paketet, vilket krävs för att öka storleken på operativ system disken.

   ```
   yum install gdisk -y
   ```

1.  Kör följande kommando för att se alla sektorer som är tillgängliga på disken:
    ```
    gdisk -l /dev/sda
    ```

1. Du kommer att se informationen som informerar partitionstypen. Se till att det är GPT. Identifiera rotnoden. Ändra inte eller ta bort startpartitionen (BIOS-startpartitionen) och systempartitionen (EFI system partition)

1. Använd följande kommando för att starta partitionering för första gången. 
    ```
    gdisk /dev/sda
    ```

1. Nu ska du se ett meddelande som frågar efter nästa kommando ("kommando:? för hjälp "). 

   ```
   w
   ```

1. Du får en varning om att "varning! Sekundärt huvud har placerats för tidigt på disken! Vill du korrigera det här problemet? (J/N): ". Du måste trycka på "Y"

   ```
   Y
   ```

1. Du bör se ett meddelande som talar om att de slutliga kontrollerna har slutförts och att du uppmanas att bekräfta. Tryck på "Y"

   ```
   Y
   ```

1. Kontrol lera om allt hände korrekt med kommandot partprobe

   ```
   partprobe
   ```

1. Ovanstående steg har säkerställt att det sekundära GPT-huvudet placeras i slutet. Nästa steg är att starta processen med att ändra storlek genom att använda verktyget gdisk igen. Använd följande kommando.

   ```
   gdisk /dev/sda
   ```
1. I menyn kommando trycker du på "p" för att se en lista över partitioner. Identifiera rotnoden (i stegen är sda2 betraktas som rotnod) och startpartitionen (i stegen är sda3 betraktas som startpartition) 

   ```
   p
   ```
    ![Rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Tryck på "d" för att ta bort partitionen och välj det partitionsnummer som är tilldelat till Start (i det här exemplet är det "3")
   ```
   d
   3
   ```
1. Tryck på "d" för att ta bort partitionen och välj det partitionsnummer som är tilldelat till Start (i det här exemplet är det ' 2 ')
   ```
   d
   2
   ```
    ![Ta bort rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Om du vill skapa en ny rotnod med större storlek trycker du på "n", anger det partitionsnummer som du tog bort tidigare för roten ("2" i det här exemplet) och väljer den första sektorn som standardvärde, sista sektorn som sista sektor värde – start storleks sektor (' 4096 i det här fallet motsvarar 2 MB start) och hex-kod som "8300"
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Om du vill återskapa startpartitionen trycker du på "n", anger det partitionsnummer som du tog bort tidigare för start ("3" i det här exemplet) och väljer den första sektorn som standardvärdet, den sista sektorn som standardvärde och hex-kod som "EF02"
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Skriv ändringarna med kommandot "w" och tryck på "Y" för att bekräfta
   ```
   w
   Y
   ```
1. Kör kommandot ' partprobe ' för att kontrol lera disk stabiliteten
   ```
   partprobe
   ```
1. Starta om den virtuella datorn och rot partitionens storlek skulle ha ökat
   ```
   reboot
   ```

   ![Ny rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Kör kommandot xfs_growfs på partitionen för att ändra storlek på det
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS-tillväxt FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Nästa steg

- [Ändra storlek på disk](expand-disks.md)
