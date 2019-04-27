---
title: Konfigurera LVM på en virtuell dator som kör Linux | Microsoft Docs
description: Lär dig hur du konfigurerar LVM på Linux i Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 08f98775360b8c0a82f68f322053cb71f0e79af3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739088"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurera LVM på en Linux-dator i Azure
Det här dokumentet innehåller information om hur du konfigurerar logiska Volume Manager (LVM) i din Azure-dator. LVM kan användas för OS-disk eller datadiskar i virtuella Azure-datorer, men som standard de flesta molnbilder har inte LVM som konfigurerats på OS-disken. Stegen nedan fokuserar på att konfigurera LVM för dina data-diskar.

## <a name="linear-vs-striped-logical-volumes"></a>Linjär jämfört med logiska stripe-volymer
LVM kan användas för att kombinera flera fysiska diskar till en enda lagringsvolym. Som standard skapar LVM vanligtvis linjär logiska volymer, vilket innebär att det fysiska lagringsutrymmet som tillsammans. I det här fallet skickas läs-/ skrivåtgärder vanligtvis bara till en enskild disk. Däremot kan vi skapa stripe-logiska volymer där läsningar och skrivningar distribueras till flera diskar som ingår i gruppen volym (liknar RAID0). Av prestandaskäl är det troligt att du vill stripe-dina logiska volymer så att läsningar och skrivningar kan du använda alla dina anslutna datadiskar.

Det här dokumentet beskriver hur du kombinerar flera datadiskar i en enskild volym-grupp och sedan skapa en logisk stripe-volym. Stegen nedan är generaliserad för att fungera med de flesta distributioner. I de flesta fall är verktyg och arbetsflöden för att hantera LVM på Azure inte helt olika än andra miljöer. Som vanligt också kontakta leverantören Linux för dokumentation och bästa praxis för att använda LVM med din specifika distribution.

## <a name="attaching-data-disks"></a>Koppla datadiskar
En vill vanligtvis börja med två eller flera tomma datadiskar när du använder LVM. Baserat på dina i/o-behov kan välja du att lägga till diskar som lagras i våra Standard-lagring, med upp till 500-i/o/ps per disk eller vår Premium-lagring med upp till 5 000 i/o/ps per disk. Den här artikeln kommer inte gå in i detalj på hur du etablerar och koppla datadiskar till en Linux-dator. Finns i Microsoft Azure-artikeln [ansluta en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) detaljerade anvisningar om hur du kopplar en tom datadisk till en Linux-dator på Azure.

## <a name="install-the-lvm-utilities"></a>Installera LVM-verktyg
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS och Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 och openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    På SLES11, måste du även redigera `/etc/sysconfig/lvm` och ange `LVM_ACTIVATED_ON_DISCOVERED` ”aktivera”:

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurera LVM
I den här guiden förutsätter vi att du har kopplat tre datadiskar som vi ska referera till som `/dev/sdc`, `/dev/sdd` och `/dev/sde`. Dessa sökvägar kanske inte matchar sökvägsnamn disk i den virtuella datorn. Du kan köra ”`sudo fdisk -l`” eller liknande kommando för att lista dina tillgängliga diskar.

1. Förbereda fysiska volymer:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Skapa en volym-grupp. I det här exemplet vi ringer upp gruppen volym `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Skapa logiska volymer. Kommandot nedan vi skapar en enskild logisk volym som kallas `data-lv01` sträcker sig över hela volymen-grupp, men Observera att det också möjligt att skapa flera logiska volymer i gruppen volym.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatera den logiska volymen

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Med SLES11 användning `-t ext3` i stället för ext4. SLES11 har endast stöd för skrivskyddad åtkomst till ext4 filsystem.

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet i/etc/fstab
> [!IMPORTANT]
> Felaktigt redigerar den `/etc/fstab` filen kan resultera i ett system som inte kan startas. Om du är osäker, finns det distribution dokumentationen för information om hur du ska redigera den här filen. Det är också rekommenderas som en säkerhetskopia av den `/etc/fstab` filen har skapats innan du redigerar.

1. Skapa den önskade monteringspunkten för ditt nya filsystem, till exempel:

    ```bash  
    sudo mkdir /data
    ```

2. Leta upp den logiska volymsökväg

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Öppna `/etc/fstab` i en textredigerare och Lägg till en post för det nya filsystemet, till exempel:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Spara och Stäng `/etc/fstab`.

4. Testa den `/etc/fstab` posten är korrekt:

    ```bash    
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett felmeddelande Kontrollera syntaxen i den `/etc/fstab` filen.
   
    Nästa körning i `mount` kommandot för att säkerställa att filsystemet är monterad:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Valfritt) Felsäker boot parametrar i `/etc/fstab`
   
    Många distributioner innehålla antingen den `nobootwait` eller `nofail` montera parametrar som kan läggas till i `/etc/fstab` fil. Dessa parametrar Tillåt för fel när du monterar en viss fil-system och att Linux-datorn och fortsätta att starta även om det inte går att montera filsystemet RAID korrekt. I din distribution dokumentationen för mer information om dessa parametrar.
   
    Exempel (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Stöd för TRIM/UNMAP
Vissa Linux-kernel stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som tagits bort sidor som inte längre är giltiga och kan ignoreras. Tar bort sidor kan minska kostnaderna om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i din Linux-VM. Som vanligt finns distributionen för den rekommenderade metoden:

- Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab ska köras regelbundet:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
