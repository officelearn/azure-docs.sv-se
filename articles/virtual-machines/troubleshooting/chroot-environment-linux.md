---
title: Chroot-miljö i en virtuell Linux-dator.
description: Den här artikeln beskriver hur du felsöker chroot-miljön i den virtuella datorn för räddnings tjänsten (VM) i Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377946"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Chroot-miljö i en Linux-räddande VM

Den här artikeln beskriver hur du felsöker chroot-miljön i den virtuella datorn för räddnings tjänsten (VM) i Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x med RAW-partitioner

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x med LVM

   > [!NOTE]
   > Om den ursprungliga virtuella datorn innehåller LVM (Logical Volume Manager) på OS-disken skapar du den rädda virtuella datorn med hjälp av avbildningen med RAW-partitioner på OS-disken.

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommando för att aktivera den logiska volym gruppen:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Använd `lsblk` kommandot för att hämta LVM-namnen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x med LVM

   > [!NOTE]
   > Om den ursprungliga virtuella datorn innehåller LVM (Logical Volume Manager) på OS-disken skapar du den rädda virtuella datorn med hjälp av avbildningen med RAW-partitioner på OS-disken.

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommando för att aktivera den logiska volym gruppen:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Använd `lsblk` kommandot för att hämta LVM-namnen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="oracle-7x"></a>Oracle 7. x

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 for SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 för SAP

1. Stoppa eller ta bort den virtuella datorn som påverkas.
1. Skapa en räddnings avbildning av en virtuell dator av samma OS-version, i samma resurs grupp (återställnings LAGRINGS grupp) och plats med hjälp av hanterad disk.
1. Använd Azure Portal för att ta en ögonblicks bild av den berörda virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken och koppla den till den räddande virtuella datorn.
1. När disken har skapats kan du felsöka chroot-miljön i den rädda virtuella datorn.

   1. Få åtkomst till den virtuella datorn som rot användare med följande kommando:

      `sudo su -`

   1. Hitta disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används **dmesg** för att filtrera på **SCSI-** diskar:

      `dmesg | grep SCSI`

      Dina utdata ser ut ungefär som i följande exempel. I det här exemplet vill vi **SDC** -disken:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Använd följande kommandon för att få åtkomst till chroot-miljön:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Felsöka chroot-miljön.

   1. Använd följande kommandon för att avsluta chroot-miljön:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Om du får felet `unable to unmount /rescue` lägger du till alternativet-l i umount-kommandot.
      >
      > Exempel: `umount -l /rescue`

1. Koppla bort disken från den virtuella datorn för räddning och utför en disk växling med den ursprungliga virtuella datorn.
1. Starta den ursprungliga virtuella datorn och kontrol lera anslutningen.

## <a name="next-steps"></a>Nästa steg

- [Felsök SSH-anslutning](troubleshoot-ssh-connection.md)
