---
title: Konfigurera programvaru-RAID på en virtuell Linux-dator
description: Lär dig hur du använder mdadm för att konfigurera RAID på Linux i Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250262"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurera Software RAID på Linux
Det är ett vanligt scenario att använda programvara RAID på Linux virtuella datorer i Azure för att presentera flera anslutna datadiskar som en enda RAID-enhet. Vanligtvis kan detta användas för att förbättra prestanda och möjliggöra förbättrat dataflöde jämfört med att bara använda en enda disk.

## <a name="attaching-data-disks"></a>Koppla datadiskar
Två eller flera tomma datadiskar behövs för att konfigurera en RAID-enhet.  Den främsta orsaken till att skapa en RAID-enhet är att förbättra prestanda för disk-IO.  Baserat på dina IO-behov kan du välja att bifoga diskar som lagras i vår standardlagring, med upp till 500 IO/ps per disk eller vår Premium-lagring med upp till 5000 IO/ps per disk. Den här artikeln går inte in på detaljer om hur du etablerar och bifogar datadiskar till en virtuell Linux-dator.  Se Microsoft Azure-artikeln [bifoga en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för detaljerade instruktioner om hur du bifogar en tom datadisk till en virtuell Linux-dator på Azure.

> [!IMPORTANT]
>Blanda inte diskar av olika storlekar, gör det skulle resultera i prestanda raidset begränsas till den långsammaste disken. 

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
I det här exemplet skapar vi en enda diskpartition på /dev/sdc. Den nya diskpartitionen anropas /dev/sdc1.

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

1. Tryck på 'n' vid uppmaningen för att skapa en **n**ew partition:

    ```bash
    Command (m for help): n
    ```

1. Tryck sedan på "p" för att skapa en **p**rimary partition:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Tryck på "1" för att välja partitionsnummer 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Välj startpunkten för den nya `<enter>` partitionen eller tryck för att acceptera standardinställningen för att placera partitionen i början av det lediga utrymmet på enheten:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Välj storleken på partitionen, till exempel skriv "+10G" för att skapa en 10 gigabyte partition. Du kan `<enter>` också trycka på Skapa en enskild partition som sträcker sig över hela enheten:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ändra sedan ID och **t**ype av partitionen från standard-ID "83" (Linux) till ID "fd" (Linux raid auto):

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
1. Följande exempel kommer att "rand" (RAID nivå 0) tre partitioner som finns på tre separata datadiskar (sdc1, sdd1, sde1).  När du har kört det här kommandot skapas en ny RAID-enhet som heter **/dev/md127.** Observera också att om dessa datadiskar vi tidigare en del av en `--force` annan nedlagd `mdadm` RAID-matris kan det vara nödvändigt att lägga till parametern i kommandot:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Skapa filsystemet på den nya RAID-enheten
   
    **CentOS, Oracle Linux, SLES 12, openSUSE och Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11 (PÅ ANDRA)**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - aktivera boot.md och skapa mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > En omstart kan krävas efter att ha gjort dessa ändringar på SUSE-system. Det här steget krävs *inte* på SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet i /etc/fstab
> [!IMPORTANT]
> Felaktig redigering av /etc/fstab-filen kan resultera i ett system som inte kan booterbara. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Det rekommenderas också att en säkerhetskopia av filen /etc/fstab skapas före redigering.

1. Skapa önskad monteringspunkt för det nya filsystemet, till exempel:

    ```bash
    sudo mkdir /data
    ```
1. Vid redigering /etc/fstab bör **UUID** användas för att referera till filsystemet i stället för enhetsnamnet.  Använd `blkid` verktyget för att fastställa UUID för det nya filsystemet:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Öppna /etc/fstab i en textredigerare och lägg till en post för det nya filsystemet, till exempel:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Eller på **SLES 11:**

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Spara sedan och stäng /etc/fstab.

1. Testa att /etc/fstab-posten är korrekt:

    ```bash  
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett felmeddelande, kontrollera syntaxen i filen /etc/fstab.
   
    Nästa kör `mount` kommandot för att säkerställa att filsystemet är monterat:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Valfritt) Parametrar för felsäker start
   
    **fstab konfiguration**
   
    Många distributioner inkluderar `nobootwait` `nofail` antingen eller montera parametrar som kan läggas till i /etc/fstab filen. Dessa parametrar möjliggör fel vid montering av ett visst filsystem och gör det möjligt för Linux-systemet att fortsätta att starta även om det inte kan montera RAID-filsystemet på rätt sätt. Mer information om dessa parametrar finns i dokumentationen till distributionen.
   
    Exempel (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux startparametrar**
   
    Utöver ovanstående parametrar kan kärnparametern "`bootdegraded=true`tillåta att systemet startas även om RAID-enheten uppfattas som skadad eller försämrad, till exempel om en dataenhet oavsiktligt tas bort från den virtuella datorn. Som standard kan detta också resultera i ett system som inte kan startas.
   
    Se dokumentationen för distributionen om hur du redigerar kärnparametrar korrekt. I många distributioner (CentOS, Oracle Linux, SLES 11) kan till exempel`/boot/grub/menu.lst`dessa parametrar läggas till manuellt i " " filen.  På Ubuntu kan denna parameter `GRUB_CMDLINE_LINUX_DEFAULT` läggas till variabeln på "/etc/default/grub".


## <a name="trimunmap-support"></a>SUPPORT FÖR TRIM/UNMAP
Vissa Linux-kärnor stöder TRIM/UNMAP-åtgärder för att ignorera oanvända block på disken. Dessa åtgärder är främst användbara i standardlagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan ignoreras. Om du tar bort sidor kan du spara kostnader om du skapar stora filer och sedan tar bort dem.

> [!NOTE]
> RAID får inte utfärda ignorera kommandon om segmentstorleken för matrisen är inställd på mindre än standard (512KB). Detta beror på att unmap-granulariteten på värden också är 512KB. Om du har ändrat matrisens segmentstorlek `--chunk=` via mdadms parameter kan TRIM/unmap-begäranden ignoreras av kärnan.

Det finns två sätt att aktivera TRIM-stöd i din Virtuella Linux-dator. Som vanligt, konsultera din distribution för den rekommenderade metoden:

- Använd `discard` monteringsalternativet `/etc/fstab`i , till exempel:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- I vissa `discard` fall kan alternativet få prestandakonsekvenser. Du kan också köra `fstrim` kommandot manuellt från kommandoraden eller lägga till det i crontab för att köras regelbundet:

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
