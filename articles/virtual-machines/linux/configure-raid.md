---
title: Konfigurera programvaru-RAID på en virtuell Linux-dator
description: Lär dig hur du använder mdadm för att konfigurera RAID i Linux i Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 3471ccfa0899f73969c511dea283c2d0d7051af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84659784"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurera programvaru-RAID på Linux
Det är ett vanligt scenario att använda programvaru-RAID på virtuella Linux-datorer i Azure för att presentera flera anslutna data diskar som en enda RAID-enhet. Detta kan användas för att förbättra prestanda och möjliggöra bättre data flöde jämfört med att bara använda en enda disk.

## <a name="attaching-data-disks"></a>Kopplar data diskar
Minst två tomma data diskar krävs för att konfigurera en RAID-enhet.  Den främsta anledningen till att skapa en RAID-enhet är att förbättra prestandan för din disk i/o.  Utifrån dina IO-behov kan du välja att koppla diskar som är lagrade i vår standard lagring, med upp till 500 IO/PS per disk eller vår Premium-lagring med upp till 5000 IO/PS per disk. Den här artikeln beskriver inte detaljerad information om hur du etablerar och ansluter data diskar till en virtuell Linux-dator.  Mer information om hur du ansluter en tom datadisk till en virtuell Linux-dator på Azure finns i Microsoft Azure artikeln [bifoga en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

> [!IMPORTANT]
>Blanda inte diskar av olika storlekar. Detta skulle resultera i att raidset-prestanda begränsas till den långsammaste disken. 

## <a name="install-the-mdadm-utility"></a>Installera mdadm-verktyget
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES och openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Skapa diskpartitioner
I det här exemplet skapar vi en partition med en enda disk på/dev/SDC. Den nya diskpartitionen kommer att kallas/dev/sdc1.

1. Börja `fdisk` skapa partitioner

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Tryck på "n" i prompten för att skapa en **n**ny partition:

    ```bash
    Command (m for help): n
    ```

1. Tryck sedan på "p" för att skapa en **p**rimär-partition:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Tryck på "1" för att välja partition nummer 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Välj Start punkten för den nya partitionen eller tryck på `<enter>` för att acceptera standardvärdet för att placera partitionen i början av det lediga utrymmet på enheten:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Välj partitionens storlek, till exempel typ ' + 10G ' för att skapa en 10 GB-partition. Eller tryck på `<enter>` skapa en enda partition som sträcker sig över hela enheten:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ändra sedan ID och **t**-typ för partitionen från standard-ID: t "83" (Linux) till ID "fd" (Linux RAID Auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Slutligen skriver du partitionstabellen till enheten och avslutar fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Skapa RAID-matrisen
1. I följande exempel blir "rand" (RAID-nivå 0) tre partitioner på tre separata data diskar (sdc1, sdd1, sde1).  När du har kört det här kommandot skapas en ny RAID-enhet med namnet **/dev/md127** . Observera också att om dessa data diskar tidigare var en del av en annan felaktig RAID-matris kan det vara nödvändigt att lägga till `--force` parametern i `mdadm` kommandot:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Skapa fil systemet på den nya RAID-enheten
   
    **CentOS, Oracle Linux, SLES 12, openSUSE och Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** – aktivera Boot.MD och skapa mdadm. conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > En omstart kan krävas när du har gjort ändringarna på SUSE-system. Det här steget krävs *inte* för SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya fil systemet i/etc/fstab
> [!IMPORTANT]
> Felaktig redigering av/etc/fstab-filen kan leda till ett system som inte kan startas. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Vi rekommenderar också att du skapar en säkerhets kopia av/etc/fstab-filen innan du redigerar.

1. Skapa önskad monterings punkt för det nya fil systemet, till exempel:

    ```bash
    sudo mkdir /data
    ```
1. När du redigerar/etc/fstab bör **UUID: n** användas för att referera till fil systemet i stället för enhetens namn.  Använd `blkid` verktyget för att fastställa UUID för det nya fil systemet:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Öppna/etc/fstab i en text redigerare och Lägg till en post för det nya fil systemet, till exempel:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Eller på **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Spara och stäng sedan/etc/fstab.

1. Testa att/etc/fstab-posten är korrekt:

    ```bash  
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett fel meddelande, kontrollerar du syntaxen i/etc/fstab-filen.
   
    Kör sedan `mount` kommandot för att se till att fil systemet är monterat:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Valfritt Failsafe start parametrar
   
    **fstab-konfiguration**
   
    Många distributioner omfattar antingen `nobootwait` parametrarna eller `nofail` som kan läggas till i/etc/fstab-filen. Dessa parametrar tillåter fel vid montering av ett visst fil system och gör att Linux-systemet kan fortsätta att starta även om det inte går att montera RAID-filsystemet på rätt sätt. Mer information om dessa parametrar finns i distributionens dokumentation.
   
    Exempel (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Start parametrar för Linux**
   
    Förutom parametrarna ovan kan kernel-parametern " `bootdegraded=true` " tillåta att systemet startar även om RAID uppfattas som skadat eller försämrat, till exempel om en data enhet oavsiktligt tas bort från den virtuella datorn. Som standard kan detta även resultera i ett icke-startbart system.
   
    Se din distributions dokumentation om hur du redigerar kernel-parametrar på rätt sätt. I många distributioner (CentOS, Oracle Linux, SLES 11) kan dessa parametrar till exempel läggas till manuellt i `/boot/grub/menu.lst` filen "".  På Ubuntu kan du lägga till den här parametern i `GRUB_CMDLINE_LINUX_DEFAULT` variabeln på "/etc/default/grub".


## <a name="trimunmap-support"></a>Stöd för trimning/MAPPNING
Vissa Linux-Kernels stöder TRIMNINGs-/MAPPNINGs åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbara i standard lagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan tas bort. Om du tar bort sidor kan du spara pengar om du skapar stora filer och sedan tar bort dem.

> [!NOTE]
> RAID kan inte utfärda ignorera-kommandon om segment storleken för matrisen har angetts till mindre än standard (512 kB). Detta beror på att mappnings precisionen på värden också är 512 kB. Om du har ändrat matrisens segment storlek via mdadm `--chunk=` -parametern, kan trimnings-eller mappnings begär Anden ignoreras av kerneln.

Det finns två sätt att aktivera TRIMNINGs stöd i din virtuella Linux-dator. Som vanligt kan du kontakta din distribution för den rekommenderade metoden:

- Använd `discard` monterings alternativet i `/etc/fstab` , till exempel:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall `discard` kan alternativet påverka prestandan. Du kan också köra `fstrim` kommandot manuellt från kommando raden eller lägga till det i crontab för att köra regelbundet:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
