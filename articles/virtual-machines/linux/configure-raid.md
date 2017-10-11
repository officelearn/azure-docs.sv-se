---
title: "Konfigurera programvarubaserad RAID på en virtuell dator som kör Linux | Microsoft Docs"
description: "Lär dig hur du använder mdadm för att konfigurera RAID på Linux i Azure."
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
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
ms.openlocfilehash: 12f540a700fbf85e579e8aadc9f6def039299ff7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-software-raid-on-linux"></a>Konfigurera programvaru-RAID på Linux
Det är ett vanligt scenario du använder programvarubaserad RAID på Linux virtuella datorer i Azure för att presentera flera diskar i bifogade data som en enkel RAID-enhet. Vanligtvis kan detta användas för att förbättra prestanda och möjliggöra bättre genomflöde jämfört med bara en enda disk.

## <a name="attaching-data-disks"></a>Bifoga datadiskar
Två eller flera tom datadiskar behövs för att konfigurera en RAID-enhet.  Den främsta orsaken till att skapa en RAID-enhet är att förbättra prestanda för disken i/o.  Baserat på i/o-behov kan du välja att koppla diskar som är lagrade i vår standardlagring med upp till 500-i/o/ps per disk eller våra Premium-lagring med upp till 5 000 IO/ps per disk. Den här artikeln går inte in i detalj att etablera och koppla datadiskar till en virtuell Linux-dator.  Finns i Microsoft Azure-artikel [ansluta en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) detaljerade anvisningar om hur du kopplar en tom datadisk till en virtuell Linux-dator på Azure.

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
I det här exemplet skapar vi en enskild diskpartition på /dev/sdc. Den nya diskpartitionen kallas /dev/sdc1.

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

2. Tryck på ”n” i Kommandotolken för att skapa en  **n** y-partition:

    ```bash
    Command (m for help): n
    ```

3. Tryck sedan på p om du vill skapa en **p**rimär partition:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Tryck '1' för att markera partitionsnumret 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Välj startpunkten för den nya partitionen eller tryck på `<enter>` att acceptera standardvärdet om du vill placera partitionen i början av det lediga utrymmet på enheten:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Välj storleken på partitionen, till exempel skriver ”+10G' om du vill skapa en 10 GB-partition. Eller tryck på `<enter>` skapa en enda partition som sträcker sig över hela enheten:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Ändra ID och **t**typ av partitionen från standard-ID '83' (Linux)-ID: t 'fd' (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Slutligen skriva partitionstabellen till enheten och avsluta fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Skapa RAID-matris
1. I följande exempel startas ”stripe” (RAID-nivå 0) tre partitioner finns på tre separata hårddiskar (sdc1, sdd1, sde1).  När du kör detta kommando en ny RAID-enhet kallas **/dev/md127** skapas. Observera också att om dessa data diskar vi tidigare tillhör en annan felaktiga RAID-matris det kan vara nödvändigt att lägga till den `--force` parametern till den `mdadm` kommando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Skapa filsystemet på den nya RAID-enheten
   
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
   > En omstart kan krävas när du har gjort dessa ändringar på SUSE system. Det här steget är *inte* krävs på SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet /etc/fstab
> [!IMPORTANT]
> Redigera filen /etc/fstab felaktigt kan det leda till att ett system som inte kan startas. Om du är osäker, i den distribution dokumentationen finns information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av /etc/fstab-filen har skapats innan du redigerar.

1. Skapa den önskade monteringspunkten för det nya filsystemet, till exempel:

    ```bash
    sudo mkdir /data
    ```
2. När du redigerar /etc/fstab, den **UUID** ska användas för att referera till filsystemet i stället för namnet på en enhet.  Använd den `blkid` verktyg för att fastställa UUID för det nya filsystemet:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Öppna /etc/fstab i en textredigerare och Lägg till en post för det nya filsystemet, till exempel:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Eller på **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Spara och Stäng /etc/fstab.

4. Testa att/etc/fstab posten är korrekt:

    ```bash  
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett felmeddelande, kontrollera syntax i filen /etc/fstab.
   
    Kör nästa gång den `mount` kommando för att se till att filsystemet är monterad:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Valfritt) Felsäker start-parametrar
   
    **fstab konfiguration**
   
    Många distributioner innehålla antingen den `nobootwait` eller `nofail` montera parametrar som kan läggas till i filen/etc/fstab. Dessa parametrar tillåta fel när du monterar en viss filsystemet och Tillåt Linux-datorn och fortsätta att starta även om det inte går att montera korrekt RAID-filsystemet. Finns i dokumentationen för din distribution för mer information om dessa parametrar.
   
    Exempel (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux-Startparametrar**
   
    Förutom ovanstående parametrar, kernel-parametern ”`bootdegraded=true`” kan tillåta systemet att starta även om RAID uppfattas som skadade eller försämrad för exempel om en dataenhet oavsiktligt tas bort från den virtuella datorn. Det kan också leda till ett ej startbar system för som standard.
   
    Se dokumentationen för din distribution om hur du redigerar korrekt kernel-parametrar. Till exempel i många distributioner (CentOS, Oracle Linux, SLES 11) dessa parametrar kan läggas till manuellt till det ”`/boot/grub/menu.lst`” filen.  På Ubuntu den här parametern kan läggas till i `GRUB_CMDLINE_LINUX_DEFAULT` variabeln på ”/ etc/standard/grub”.


## <a name="trimunmap-support"></a>Stöd för TRIM/UNMAP
Vissa Linux kärnor stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som bort sidor som inte längre är giltig och kan tas bort. Ignorera sidor kan du spara kostnader om du skapar stora filer och ta bort dem.

> [!NOTE]
> RAID kan inte utfärda kommandon på Ignorera om segmentstorleken för matrisen anges till mindre än standardvärdet (512KB). Detta beror på att unmap granularitet på värden är också 512KB. Om du har ändrat matrisens segmentstorleken via mdadm's `--chunk=` parameter och sedan ta bort TRIMNING/mappning begäranden ignoreras av kernel.

Det finns två sätt att aktivera TRIMNING stöd i Linux-VM. Som vanligt, kontakta din distribution för den rekommenderade metoden:

- Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab att köras regelbundet:

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
