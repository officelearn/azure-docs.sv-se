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
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 9a22426d0422585714cb78d541a84d55d2fce6e0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurera LVM på en virtuell Linux-dator i Azure
Det här dokumentet innehåller information om hur du konfigurerar logiska volymen Manager (LVM) i ditt virtuella Azure-datorn. Det är möjligt att konfigurera LVM på en disk ansluten till den virtuella datorn, som standard de flesta molnet bilder inte LVM som konfigurerats på OS-disk. Detta är att förhindra problem med dubbla volym grupper om OS-disken någonsin kopplas till en annan virtuell dator med samma distribution och typ, dvs. under en återställningsscenario. Därför rekommenderas endast för att använda LVM på datadiskar med.

## <a name="linear-vs-striped-logical-volumes"></a>Linjär kontra logiska stripe-volymer
LVM kan användas för att kombinera flera fysiska diskar till en enda lagringsvolym. Som standard skapar LVM vanligtvis linjär logiska volymer, vilket innebär att den fysiska lagringsplatsen sammanfogas tillsammans. I det här fallet skickas läs-/ skrivåtgärder vanligtvis bara till en enskild disk. Däremot kan vi skapa stripe-logiska volymer där läsningar och skrivningar distribueras till flera diskar som ingår i gruppen volym (d.v.s. liknar RAID0). Av prestandaskäl troligtvis kommer du vilja stripe-dina logiska volymer så att använda alla anslutna datadiskar läsningar och skrivningar.

Det här dokumentet beskrivs hur du kombinera flera datadiskar till en enda volym-grupp och sedan skapa en logisk stripe-volym. De här stegen är ganska generaliserade för att fungera med de flesta distributioner. I de flesta fall är verktyg och arbetsflöden för att hantera LVM i Azure inte helt annorlunda än andra miljöer. Som vanligt också kontakta leverantören av Linux för dokumentation och bästa praxis för att använda LVM med en viss distribution.

## <a name="attaching-data-disks"></a>Bifoga datadiskar
En vill vanligtvis börja med minst två tomma datadiskar när du använder LVM. Baserat på i/o-behov kan du välja att koppla diskar som är lagrade i vår standardlagring med upp till 500-i/o/ps per disk eller våra Premium-lagring med upp till 5 000 IO/ps per disk. Den här artikeln kommer inte gå in i detalj att etablera och koppla datadiskar till en virtuell Linux-dator. Finns i Microsoft Azure-artikeln [ansluta en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) detaljerade anvisningar om hur du kopplar en tom datadisk till en virtuell Linux-dator på Azure.

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

    På SLES11 måste du också redigera `/etc/sysconfig/lvm` och ange `LVM_ACTIVATED_ON_DISCOVERED` ”aktivera”:

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurera LVM
I den här guiden antar vi att du har kopplat tre datadiskar som vi ska kallar `/dev/sdc`, `/dev/sdd` och `/dev/sde`. Observera att de inte alltid samma sökvägsnamn i den virtuella datorn. Du kan köra '`sudo fdisk -l`' eller liknande kommando för att visa en lista över tillgängliga diskar.

1. Förbereda fysiska volymer:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Skapa en grupp för volymen. I det här exemplet vi ringer gruppen volym `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Skapa logiska volymerna. Kommandot nedan vi skapar en logisk volym kallas `data-lv01` span gruppen hela volymen, men Observera att det är också möjligt att skapa flera logiska volymer i gruppen volym.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatera den logiska

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Med SLES11 använder `-t ext3` i stället för ext4. SLES11 stöder endast skrivskyddad åtkomst till ext4 filsystem.

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet /etc/fstab
> [!IMPORTANT]
> Felaktigt redigerar den `/etc/fstab` filen kan resultera i ett system som inte kan startas. Om du är osäker, se den fördelningen dokumentationen för information om hur du ska redigera den här filen. Det är också rekommenderar att en säkerhetskopia av den `/etc/fstab` filen har skapats innan du redigerar.

1. Skapa den önskade monteringspunkten för det nya filsystemet, till exempel:

    ```bash  
    sudo mkdir /data
    ```

2. Leta upp de logiska volymsökväg

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

    Om det här kommandot resulterar i ett felmeddelande Kontrollera syntax den `/etc/fstab` filen.
   
    Kör nästa gång den `mount` kommando för att se till att filsystemet är monterad:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Valfritt) Felsäker Startparametrar i `/etc/fstab`
   
    Många distributioner innehålla antingen den `nobootwait` eller `nofail` montera parametrar som kan läggas till i `/etc/fstab` fil. Dessa parametrar tillåta fel när du monterar en viss filsystemet och Tillåt Linux-datorn och fortsätta att starta även om det inte går att montera korrekt RAID-filsystemet. Se dokumentationen för din distribution för mer information om dessa parametrar.
   
    Exempel (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Stöd för TRIM/UNMAP
Vissa Linux kärnor stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som bort sidor som inte längre är giltig och kan tas bort. Ignorera sidor kan du spara kostnader om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i Linux-VM. Som vanligt, kontakta din distribution för den rekommenderade metoden:

- Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab att köras regelbundet:

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
