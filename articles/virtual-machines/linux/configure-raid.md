---
title: Konfigurera programvaru-RAID på en virtuell dator som kör Linux | Microsoft Docs
description: Lär dig hur du använder mdadm för att konfigurera RAID på Linux i Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: e773fdcb031f0f8f896ea40d76231fd54a603dc4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089037"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurera programvaru-RAID på Linux
Det är ett vanligt scenario du använder programvaru-RAID på Linux-datorer i Azure för att presentera flera anslutna datadiskar som en enkel RAID-enhet. Vanligtvis kan detta användas för att förbättra prestanda och tillåter bättre genomflöde jämfört med användningen av bara en enda disk.

## <a name="attaching-data-disks"></a>Koppla datadiskar
Två eller flera tomma datadiskar behövs för att konfigurera en RAID-enhet.  Den främsta orsaken för att skapa en RAID-enhet är att förbättra prestanda av din i/o.  Baserat på dina i/o-behov kan välja du att lägga till diskar som lagras i våra Standard-lagring, med upp till 500-i/o/ps per disk eller vår Premium-lagring med upp till 5 000 i/o/ps per disk. Den här artikeln går inte in i detalj på hur du etablerar och koppla datadiskar till en Linux-dator.  Finns i Microsoft Azure-artikeln [ansluta en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) detaljerade anvisningar om hur du kopplar en tom datadisk till en Linux-dator på Azure.

## <a name="install-the-mdadm-utility"></a>Installera verktyget mdadm
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
I det här exemplet skapar vi en enskild disk-partition på /dev/sdc. Den nya diskpartitionen kallas /dev/sdc1.

1. Starta `fdisk` att börja skapa partitioner

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

1. Tryck på ”n” i Kommandotolken för att skapa en **n**TT-partition:

    ```bash
    Command (m for help): n
    ```

1. Tryck sedan på ”p” för att skapa en **p**rimär partition:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Tryck på '1' och välj partitionsnumret 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Välj startpunkten för den nya partitionen eller tryck på `<enter>` att acceptera standardinställningarna om du vill placera partitionen i början av det lediga utrymmet på enheten:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Välj storleken på partitionen, till exempel skriva ”+10G” om du vill skapa en partition med 10 GB. Eller tryck på `<enter>` skapa en enda partition som sträcker sig över hela enheten:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Sedan ändra-ID och **t**typ av partitionen från standard-ID '83 ”(Linux) till ID: T 'fd” (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Slutligen skriver partitionstabellen till enheten och avsluta fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Skapa RAID-matris
1. Följande exempel som ”stripe” (RAID-nivå 0) tre partitioner som finns på tre separata datadiskar (sdc1, sdd1, sde1).  När du kör det här kommandot en ny RAID-enhet kallas **/dev/md127** har skapats. Observera också att om dessa data diskar vi tidigare en del av en annan RAID-matris som inte gäller längre det kan vara nödvändigt att lägga till den `--force` parametern till den `mdadm` kommando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Skapa filsystemet på den nya RAID-enheten
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE och Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** – aktivera boot.md och skapa mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > En omstart kan krävas när du har gjort dessa ändringar på SUSE-system. Det här steget är *inte* krävs på SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet i/etc/fstab
> [!IMPORTANT]
> Felaktigt redigerar/etc/fstab-filen kan resultera i ett system som inte kan startas. Om du är osäker, finns det distribution dokumentationen för information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av/etc/fstab-filen har skapats innan du redigerar.

1. Skapa den önskade monteringspunkten för ditt nya filsystem, till exempel:

    ```bash
    sudo mkdir /data
    ```
1. När du redigerar/etc/fstab, den **UUID** bör användas för att referera till filsystemet i stället för namnet på enheten.  Använd den `blkid` verktyg för att fastställa UUID för det nya filsystemet:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Öppna/etc/fstab i en textredigerare och Lägg till en post för det nya filsystemet, till exempel:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Eller på **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Spara och Stäng/etc/fstab.

1. Testa att/etc/fstab posten är korrekt:

    ```bash  
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett felmeddelande, kontrollera syntax i filen/etc/fstab.
   
    Nästa körning i `mount` kommandot för att säkerställa att filsystemet är monterad:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Valfritt) Felsäker start-parametrar
   
    **fstab-konfiguration**
   
    Många distributioner innehålla antingen den `nobootwait` eller `nofail` montera parametrar som kan läggas till i/etc/fstab-filen. Dessa parametrar Tillåt för fel när du monterar en viss fil-system och att Linux-datorn och fortsätta att starta även om det inte går att montera filsystemet RAID korrekt. I din distribution dokumentationen för mer information om dessa parametrar.
   
    Exempel (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux Start-parametrar**
   
    Förutom ovanstående parametrar, kernel-parameter ”`bootdegraded=true`” kan göra att systemet att starta även om RAID uppfattas som skadad eller försämrad för exempel om en dataenhet oavsiktligt tas bort från den virtuella datorn. Detta kan också resultera i ett icke startbart system för som standard.
   
    Läs dokumentationen för din distribution om hur du redigerar korrekt kernel parametrar. Till exempel i många distributioner (CentOS, Oracle Linux, SLES 11) dessa parametrar kan läggas till manuellt till den ”`/boot/grub/menu.lst`” fil.  I Ubuntu den här parametern kan läggas till i `GRUB_CMDLINE_LINUX_DEFAULT` variabeln på ”/ etc/standard/grub”.


## <a name="trimunmap-support"></a>Stöd för TRIM/UNMAP
Vissa Linux-kernel stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som tagits bort sidor som inte längre är giltiga och kan ignoreras. Tar bort sidor kan minska kostnaderna om du skapar stora filer och ta bort dem.

> [!NOTE]
> RAID kan inte skicka kommandon för borttagna objekt om segmentstorleken för matrisen är inställt på mindre än standardvärdet (512KB). Det beror på att unmap kornighet på värden är också 512KB. Om du har ändrat matrisens segmentstorleken via mdadm's `--chunk=` parametern och sedan rensa/Avmappa begäranden ignoreras av kernel.

Det finns två sätt att aktivera TRIMNING stöd i din Linux-VM. Som vanligt finns distributionen för den rekommenderade metoden:

- Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab ska köras regelbundet:

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
