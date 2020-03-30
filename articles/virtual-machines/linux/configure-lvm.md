---
title: Konfigurera LVM på en virtuell dator som kör Linux
description: Lär dig hur du konfigurerar LVM på Linux i Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066799"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurera LVM på en Virtuell Linux-dator i Azure
Det här dokumentet kommer att diskutera hur du konfigurerar Logisk volymhanterare (LVM) i din virtuella Azure-dator. LVM kan användas på OS-disken eller datadiskarna i virtuella Azure-datorer, men som standard har de flesta molnavbildningar inte LVM konfigurerat på OS-disken. Stegen nedan fokuserar på att konfigurera LVM för dina datadiskar.

## <a name="linear-vs-striped-logical-volumes"></a>Linjära kontra randiga logiska volymer
LVM kan användas för att kombinera ett antal fysiska diskar till en enda lagringsvolym. Som standard skapar LVM vanligtvis linjära logiska volymer, vilket innebär att den fysiska lagringen sammanfogas tillsammans. I det här fallet skickas läs-/skrivåtgärder vanligtvis bara till en enda disk. Däremot kan vi också skapa randiga logiska volymer där läsningar och skrivningar distribueras till flera diskar i volymgruppen (liknande RAID0). Av prestandaskäl är det troligt att du kommer att vilja randa dina logiska volymer så att läser och skriver använder alla dina bifogade datadiskar.

Det här dokumentet beskriver hur du kombinerar flera datadiskar till en enda volymgrupp och skapar sedan en stripe-logisk volym. Stegen nedan är generaliserade för att fungera med de flesta distributioner. I de flesta fall är verktygen och arbetsflödena för hantering av LVM på Azure inte fundamentalt annorlunda än andra miljöer. Som vanligt, också konsultera din Linux-leverantör för dokumentation och bästa praxis för att använda LVM med just din distribution.

## <a name="attaching-data-disks"></a>Koppla datadiskar
Man vill vanligtvis börja med två eller flera tomma datadiskar när man använder LVM. Baserat på dina IO-behov kan du välja att bifoga diskar som lagras i vår standardlagring, med upp till 500 IO/ps per disk eller vår Premium-lagring med upp till 5000 IO/ps per disk. Den här artikeln kommer inte att gå in på detaljer om hur du etablerar och bifogar datadiskar till en virtuell Linux-dator. Se Microsoft Azure-artikeln [bifoga en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för detaljerade instruktioner om hur du bifogar en tom datadisk till en virtuell Linux-dator på Azure.

## <a name="install-the-lvm-utilities"></a>Installera LVM-verktygen
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 och openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11 (PÅ ANDRA)**

    ```bash   
    sudo zypper install lvm2
    ```

    På SLES11 måste du `/etc/sysconfig/lvm` också `LVM_ACTIVATED_ON_DISCOVERED` redigera och ställa in "aktivera":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurera LVM
I den här guiden antar vi att du har bifogat tre `/dev/sdc` `/dev/sdd` datadiskar, som vi kallar , och `/dev/sde`. Dessa sökvägar kanske inte matchar disksökvägsnamnen i den virtuella datorn. Du kan`sudo fdisk -l`köra ' ' eller liknande kommando för att lista dina tillgängliga diskar.

1. Förbered de fysiska volymerna:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Skapa en volymgrupp. I det här exemplet ringer `data-vg01`vi volymgruppen:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Skapa de logiska volymerna. Kommandot nedan skapar vi en enda `data-lv01` logisk volym som kallas att spänna över hela volymgruppen, men observera att det också är möjligt att skapa flera logiska volymer i volymgruppen.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatera den logiska volymen

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Med SLES11 `-t ext3` användning istället för ext4. SLES11 stöder endast skrivskyddad åtkomst till ext4-filsystem.

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya filsystemet i /etc/fstab
> [!IMPORTANT]
> Felaktig redigering av filen `/etc/fstab` kan leda till att systemet inte kan startas. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Vi rekommenderar också att en `/etc/fstab` säkerhetskopia av filen skapas före redigering.

1. Skapa önskad monteringspunkt för det nya filsystemet, till exempel:

    ```bash  
    sudo mkdir /data
    ```

2. Leta reda på sökvägen till logisk volym

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Öppna `/etc/fstab` i en textredigerare och lägg till en post för det nya filsystemet, till exempel:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Spara och stäng `/etc/fstab`sedan .

4. Testa att `/etc/fstab` posten är korrekt:

    ```bash    
    sudo mount -a
    ```

    Om det här kommandot resulterar i `/etc/fstab` ett felmeddelande kontrollera syntaxen i filen.
   
    Nästa kör `mount` kommandot för att säkerställa att filsystemet är monterat:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Valfritt) Felsäkra startparametrar i`/etc/fstab`
   
    Många distributioner innehåller `nobootwait` `nofail` antingen parametrarna eller monteringsparametrarna som kan läggas till i `/etc/fstab` filen. Dessa parametrar möjliggör fel vid montering av ett visst filsystem och gör det möjligt för Linux-systemet att fortsätta att starta även om det inte kan montera RAID-filsystemet på rätt sätt. Mer information om dessa parametrar finns i dokumentationen till distributionen.
   
    Exempel (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>SUPPORT FÖR TRIM/UNMAP
Vissa Linux-kärnor stöder TRIM/UNMAP-åtgärder för att ignorera oanvända block på disken. Dessa åtgärder är främst användbara i standardlagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan ignoreras. Om du tar bort sidor kan du spara kostnader om du skapar stora filer och sedan tar bort dem.

Det finns två sätt att aktivera TRIM-stöd i din Virtuella Linux-dator. Som vanligt, konsultera din distribution för den rekommenderade metoden:

- Använd `discard` monteringsalternativet `/etc/fstab`i , till exempel:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- I vissa `discard` fall kan alternativet få prestandakonsekvenser. Du kan också köra `fstrim` kommandot manuellt från kommandoraden eller lägga till det i crontab för att köras regelbundet:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
