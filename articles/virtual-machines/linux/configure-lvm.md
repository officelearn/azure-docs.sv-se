---
title: Konfigurera LVM på en virtuell dator som kör Linux
description: Lär dig hur du konfigurerar LVM i Linux i Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 9a3498939ddf57e2520a140ff693a30de913fae0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658289"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurera LVM på en virtuell Linux-dator i Azure
Det här dokumentet beskriver hur du konfigurerar LVM (Logical Volume Manager) på din virtuella Azure-dator. LVM kan användas på operativ system disken eller data diskarna på virtuella Azure-datorer, men som standard har de flesta moln avbildningarna inte LVM konfigurerade på OS-disken. Stegen nedan fokuserar på att konfigurera LVM för dina data diskar.

## <a name="linear-vs-striped-logical-volumes"></a>Linjära eller stripe logiska volymer
LVM kan användas för att kombinera ett antal fysiska diskar till en enda lagrings volym. Som standard skapar LVM linjära logiska volymer, vilket innebär att den fysiska lagringen sammanfogas tillsammans. I det här fallet skickas endast Läs-och skriv åtgärder till en enda disk. Vi kan däremot också skapa Striped logiska volymer där läsningar och skrivningar distribueras till flera diskar i volym gruppen (liknar RAID0). Av prestanda skäl är det troligt att du vill ta bort dina logiska volymer så att läsningar och skrivningar använder alla dina anslutna data diskar.

Det här dokumentet beskriver hur du kombinerar flera data diskar till en enda volym grupp och sedan skapar en stripe-logisk volym. Stegen nedan är generaliserade för att fungera med de flesta distributioner. I de flesta fall är verktygen och arbets flödena för att hantera LVM i Azure inte grundläggande annorlunda än andra miljöer. Som vanligt kan du också kontakta din Linux-leverantör för dokumentation och bästa praxis för att använda LVM med din specifika distribution.

## <a name="attaching-data-disks"></a>Kopplar data diskar
En är vanligt vis att börja med två eller flera tomma data diskar när du använder LVM. Utifrån dina IO-behov kan du välja att koppla diskar som är lagrade i vår standard lagring, med upp till 500 IO/PS per disk eller vår Premium-lagring med upp till 5000 IO/PS per disk. Den här artikeln går inte närmare om hur du etablerar och ansluter data diskar till en virtuell Linux-dator. Mer information om hur du ansluter en tom datadisk till en virtuell Linux-dator på Azure finns i Microsoft Azure artikeln [bifoga en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

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

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    På SLES11 måste du också redigera `/etc/sysconfig/lvm` och ange `LVM_ACTIVATED_ON_DISCOVERED` till "Aktivera":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurera LVM
I den här guiden kommer vi att anta att du har kopplat tre data diskar, som vi ska referera till som `/dev/sdc` `/dev/sdd` och `/dev/sde` . Dessa sökvägar stämmer inte överens med disk Sök vägens namn i den virtuella datorn. Du kan köra `sudo fdisk -l` kommandot eller liknande för att visa en lista över tillgängliga diskar.

1. Förbered de fysiska volymerna:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Skapa en volym grupp. I det här exemplet anropar vi volym gruppen `data-vg01` :

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Skapa den eller de logiska volymerna. Kommandot nedan skapar en logisk volym `data-lv01` som kallas för att omfatta hela volym gruppen, men Observera att det också är möjligt att skapa flera logiska volymer i volym gruppen.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatera den logiska volymen

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Med SLES11 använder du `-t ext3` i stället för ext4. SLES11 stöder endast skrivskyddad åtkomst till ext4-filsystem.

## <a name="add-the-new-file-system-to-etcfstab"></a>Lägg till det nya fil systemet i/etc/fstab
> [!IMPORTANT]
> Felaktig redigering av filen `/etc/fstab` kan leda till att systemet inte kan startas. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Vi rekommenderar också att du skapar en säkerhets kopia av `/etc/fstab` filen innan du redigerar.

1. Skapa önskad monterings punkt för det nya fil systemet, till exempel:

    ```bash  
    sudo mkdir /data
    ```

2. Hitta den logiska volym Sök vägen

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Öppna `/etc/fstab` i en text redigerare och Lägg till en post för det nya fil systemet, till exempel:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Spara och stäng sedan `/etc/fstab` .

4. Testa att `/etc/fstab` posten är korrekt:

    ```bash    
    sudo mount -a
    ```

    Om det här kommandot resulterar i ett fel meddelande kontrollerar du syntaxen i `/etc/fstab` filen.
   
    Kör sedan `mount` kommandot för att se till att fil systemet är monterat:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Valfritt Failsafe start parametrar i`/etc/fstab`
   
    Många distributioner omfattar antingen `nobootwait` parametrarna eller `nofail` som kan läggas till i `/etc/fstab` filen. Dessa parametrar tillåter fel vid montering av ett visst fil system och gör att Linux-systemet kan fortsätta att starta även om det inte går att montera RAID-filsystemet på rätt sätt. Mer information om dessa parametrar finns i distributionens dokumentation.
   
    Exempel (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Stöd för trimning/MAPPNING
Vissa Linux-Kernels stöder TRIMNINGs-/MAPPNINGs åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbara i standard lagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan tas bort. Om du tar bort sidor kan du spara pengar om du skapar stora filer och sedan tar bort dem.

Det finns två sätt att aktivera TRIMNINGs stöd i din virtuella Linux-dator. Som vanligt kan du kontakta din distribution för den rekommenderade metoden:

- Använd `discard` monterings alternativet i `/etc/fstab` , till exempel:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- I vissa fall `discard` kan alternativet påverka prestandan. Du kan också köra `fstrim` kommandot manuellt från kommando raden eller lägga till det i crontab för att köra regelbundet:

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
