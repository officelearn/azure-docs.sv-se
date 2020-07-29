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
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83651088"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ändra storlek på en OS-disk som har en GPT-partition

> [!NOTE]
> Det här scenariot gäller endast för OS-diskar som har GPT-partitioner (GUID Partition Table).

Den här artikeln beskriver hur du ökar storleken på en OS-disk som har en GPT-partition i Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identifiera om OS-disken har en MBR-eller GPT-partition

Använd kommandot **part** för att identifiera om diskpartitionen har skapats med antingen en Master Boot Record-partition (MBR) eller en GPT-partition.

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

I följande utdata visar **partitionstabellen** ett **GPT**-värde. Det här värdet identifierar en GPT-partition.

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
   
      `#sudo su`

   1. Använd följande kommando för att installera **gptfdisk** -paketet, vilket krävs för att öka storleken på operativ system disken:

      `#zypper install gptfdisk -y`

   1. Om du vill visa den största tillgängliga sektorn på disken kör du följande kommando:

      `#sgdisk -e /dev/sda`

   1. Ändra storlek på partitionen utan att ta bort den med hjälp av följande kommando. Det **delade** kommandot har ett alternativ med namnet **resizepart** för att ändra storlek på partitionen utan att ta bort den. Talet 4 efter **resizepart** indikerar storleks ändring av den fjärde partitionen.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Kör kommandot **#lsblk** för att kontrol lera om partitionen har ökat.

      Följande utdata visar att **/dev/sda4** -partitionen har storleksändrats till 98,5 GB.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Identifiera typ av fil system på OS-disken med hjälp av följande kommando:

      `blkid`

      Exempel på utdata:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. Använd lämpliga kommandon för att ändra storlek på fil systemet baserat på typ av fil system.

      Använd följande kommando för **xfs**:

      ` #xfs_growfs /`

      Exempel på utdata:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Använd följande kommando för **ext4**:

      ```#resize2fs /dev/sda4```

   1. Kontrol lera den ökade fil system storleken för **DF**genom att använda följande kommando:

      `#df -Th`

      Exempel på utdata:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

I föregående exempel kan vi se att fil system storleken för operativ system disken har ökat.

### <a name="rhel"></a>RHEL

Öka storleken på operativ system disken i RHEL 7. x med LVM:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta den virtuella datorn.

Utför följande steg när den virtuella datorn har startats om:

   1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av följande kommando:
   
      `#sudo su`

   1. Installera **gptfdisk** -paketet, vilket krävs för att öka storleken på operativ system disken.

      `#yum install gdisk -y`

   1. Kör följande kommando för att se den största tillgängliga sektorn på disken:

      `#sgdisk -e /dev/sda`

   1. Ändra storlek på partitionen utan att ta bort den med hjälp av följande kommando. Det **delade** kommandot har ett alternativ med namnet **resizepart** för att ändra storlek på partitionen utan att ta bort den. Talet 4 efter **resizepart** indikerar storleks ändring av den fjärde partitionen.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Kör följande kommando för att kontrol lera att partitionen har ökat:

      `#lsblk`

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

      `#pvresize /dev/sda4`

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

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

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

      `#df -Th /`

      Exempel på utdata:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > Om du vill använda samma procedur för att ändra storlek på en annan logisk volym ändrar du namnet på **LV** i steg 7.

## <a name="next-steps"></a>Nästa steg

- [Ändra storlek på disk](expand-disks.md)