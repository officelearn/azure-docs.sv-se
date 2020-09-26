---
title: Ändra storlek på krypterade volymer för logisk volym hantering med Azure Disk Encryption
description: Den här artikeln innehåller instruktioner för att ändra storlek på ADE-krypterade diskar med hantering av logiska volymer
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346312"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Ändra storlek på logiska volym hanterings enheter som är krypterade med Azure Disk Encryption

Den här artikeln är en steg-för-steg-process om hur du ändrar storlek på ADE-krypterade data diskar med hantering av logiska volymer (LVM) på Linux, som är tillämpliga på flera olika scenarier.

Processen gäller för följande miljöer:

- Linux-distributioner
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure Disk Encryption tillägg med ett enda pass
- Azure Disk Encryption Dual-pass-tillägg

## <a name="considerations"></a>Överväganden

Det här dokumentet förutsätter att:

1. Det finns en befintlig LVM-konfiguration.
   
   Kontrol lera [Konfigurera LVM på en virtuell Linux-dator](configure-lvm.md) för mer information om hur du konfigurerar LVM på en virtuell Linux-dator.

2. Diskarna är redan krypterade med Azure Disk Encryption kontrol lera [Konfigurera LVM vid kryptering](how-to-configure-lvm-raid-on-crypt.md) för information om hur du konfigurerar LVM-on-Encryption.

3. Du har nödvändig Linux-och LVM-expertis för att följa dessa exempel.

4. Du förstår att rekommendationen att använda data diskar på Azure som anges på [Felsök enhets namns problem](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), är att använda/dev/disk/SCSI1/-sökvägar.

## <a name="scenarios"></a>Scenarier

Procedurerna i den här artikeln gäller för följande scenarier:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>För traditionella LVM-och LVM-crypt-konfigurationer

- Utöka en logisk volym när det finns tillräckligt med utrymme i VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>För traditionell LVM-kryptering (de logiska volymerna är krypterade, inte hela disken)

- Utöka en traditionell LVM-volym genom att lägga till ett nytt PV
- Utöka en traditionell LVM volym ändra storlek på ett befintligt PV

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>För LVM-on-Encryption (rekommenderad metod är hela disken krypterad, inte bara den logiska volymen)

- Utöka en LVM på en krypterings volym som lägger till ett nytt PV
- Utöka en LVM på krypterings volym för att ändra storlek på ett befintligt PV

> [!NOTE]
> Det rekommenderas inte att blanda traditionell LVM-kryptering och LVM-on-Encryption på samma virtuella dator.

> [!NOTE]
> De här exemplen använder befintliga namn för diskar, fysiska volymer, volym grupper, logiska volymer, fil system, UUID: er och mountpoints. du måste ersätta värdena som anges i de här exemplen för att passa din miljö.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Utöka en logisk volym när det finns tillräckligt med utrymme i VG

Traditionell metod som används för att ändra storlek på logiska volymer, den kan tillämpas på icke-krypterade diskar, traditionella LVM-krypterade volymer och LVM-krypterings konfiguration.

1. Kontrol lera den aktuella storleken på det fil system som vi vill öka:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariot-check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Kontrol lera att VG har tillräckligt med utrymme för att öka LV-antalet

    ``` bash
    vgs
    ```

    ![scenariot-check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Du kan också använda "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenariot-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identifiera vilken logisk volym som behöver ändras

    ``` bash
    lsblk
    ```

    ![scenariot-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    För LVM-on-Encryption är skillnaden på denna utdata, som visar att det krypterade lagret finns på det krypterade skiktet som täcker hela disken

    ![scenariot-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Kontrol lera storleken på den logiska volymen

    ``` bash
    lvdisplay lvname
    ```

    ![scenariot-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Öka LV-storleken med "-r" för storleks ändring av fil systemet online

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa – ändra storlek – LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Kontrol lera de nya storlekarna för LV och fil systemet

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Den nya storleken visas, det betyder att storleken på LV och fil systemet har ändrats

7. Du kan kontrol lera LV-informationen igen för att bekräfta ändringarna på LV-nivån

    ``` bash
    lvdisplay lvname
    ```

    ![scenariot-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Utöka en traditionell LVM-volym genom att lägga till ett nytt PV

Gäller när du behöver lägga till en ny disk för att öka volym gruppens storlek.

1. Kontrol lera den aktuella storleken på det fil system som vi vill öka:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verifiera den aktuella konfigurationen för fysisk volym

    ``` bash
    pvs
    ```

    ![scenariob-check-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Kontrol lera den aktuella VG-informationen

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Kontrol lera den aktuella disk listan

    Data diskar ska identifieras genom att kontrol lera enheterna under/dev/disk/Azure/SCSI1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Kontrol lera utdata från lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Koppla den nya disken till den virtuella datorn

    Följ upp till steg 4 i följande dokument

   - [Ansluta en disk till en virtuell dator](attach-disk-portal.md)

7. När disken är ansluten kontrollerar du disk listan och ser den nya disken

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Skapa ett nytt PV ovanpå den nya data disken

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Den här metoden använder hela disken som ett PV-alternativ utan partition, om du vill kan du använda "fdisk" för att skapa en partition och sedan använda den partitionen för "pvcreate".

9. Kontrol lera att PV har lagts till i listan PV.

    ``` bash
    pvs
    ```

    ![scenariob-check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Utöka VG genom att lägga till det nya PV

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-Extend](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Kontrol lera storleken på den nya VG

    ``` bash
    vgs
    ```

    ![scenariob-check-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Använd lsblk för att identifiera vilka LV som måste ändras

    ``` bash
    lsblk
    ```

    ![scenariob-check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Utöka LV-storleken med "-r" för att göra en online-höjning av fil systemet

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verifiera de nya storlekarna för LV och fil systemet

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Är viktigt att veta att när ADE används på traditionella LVM-konfigurationer skapas det krypterade lagret på LV-nivå, inte på disk nivå.

    I det här läget expanderas det krypterade lagret till den nya disken.
    Den faktiska data disken har inga krypterings inställningar på plattforms nivån, så dess krypterings status är inte uppdaterad.

    >[!NOTE]
    >Detta är några av orsakerna till varför LVM-on-Encryption är den rekommenderade metoden. 

15. Kontrol lera krypterings informationen från portalen:

    ![scenariob-check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Du måste lägga till en ny LV och aktivera tillägget på den virtuella datorn för att kunna uppdatera krypterings inställningarna på disken.
    
16. Lägg till en ny LV, skapa ett fil system på det och Lägg till det i/etc/fstab

17. Ange krypterings tillägget igen för att stämpla krypterings inställningarna på den nya data disken på plattforms nivå.

    Exempel:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Kontrol lera krypterings informationen från portalen:

    ![scenariob-check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. När krypterings inställningarna har uppdaterats kan du ta bort det nya LV-värdet. du måste också ta bort posten från/etc/fstab och/etc/crypttab som har skapats för den.

    ![scenariob-Delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Demontera den logiska volymen

    ``` bash
    umount /mountpoint
    ```

21. Stäng volymens krypterade skikt

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Ta bort LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Utöka en traditionell LVM volym ändra storlek på ett befintligt PV

Vissa scenarier eller begränsningar kräver att du ändrar storlek på en befintlig disk.

1. Identifiera dina krypterade diskar

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-check-SCSI1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Kontrol lera PV-informationen

    ``` bash
    pvs
    ```

    ![scenarioc-check-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Allt utrymme på alla PVs används för närvarande

3. Kontrol lera informationen om VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Kontrol lera disk storlekarna, du kan använda fdisk eller lsblk för att visa en lista över enhets storlekarna

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc – check-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Vi har identifierat vilka PVs som är kopplade till vilka LVs med hjälp av lsblk-FS, och du kan även identifiera det genom att köra "lvdisplay"

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    I det här fallet är alla 4 data enheter en del av samma VG och en enda LV, men konfigurationen kan skilja sig från det här exemplet.

5. Kontrol lera den aktuella fil användningen:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Ändra storlek på data diskarna:

    Du kan referera till [Linux Expand diskar](expand-disks.md) (du kan bara se storleken på disken), du kan använda portalen, CLI eller PowerShell för att göra det här steget.

    >[!NOTE]
    >Tänk på att det inte går att utföra åtgärder för att ändra storlek på virtuella diskar med den virtuella datorn som körs. Du måste frigöra den virtuella datorn för det här steget

7. När diskarna ändras till det värde som krävs startar du den virtuella datorn och kontrollerar de nya storlekarna med hjälp av fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    I det här fallet ändrades/dev/SDD storlek från 5G till 20G

8. Kontrol lera aktuell PV-storlek

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Även om storleken på disken har ändrats har PV fortfarande den tidigare storleken.

9. Ändra storlek på PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-check-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Kontrol lera PV-storleken

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Använd samma procedur för alla diskar som du vill ändra storlek på.

11. Kontrol lera informationen om VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG har nu utrymme för att allokeras till LVs

12. Ändra storlek på LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Kontrol lera FS-storlek

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-check-DF3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Utöka en LVM-on-Encryption-volym Lägg till ett nytt PV

Den här metoden följer stegen nedan för att [Konfigurera LVM på crypt](how-to-configure-lvm-raid-on-crypt.md) för att lägga till en ny disk och konfigurera den under en LVM-krypterings konfiguration.

Du kan använda den här metoden för att lägga till utrymme till en redan existerande LV eller så kan du skapa nya VGs eller LVs.

1. Kontrol lera den aktuella storleken på din VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenario-check-VG01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verifiera storleken på den as FS och LV som du vill öka

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario-check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenario-check-FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Lägg till en ny datadisk till den virtuella datorn och identifiera den.

    Kontrol lera diskarna innan du lägger till disken

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Kontrol lera diskarna innan du lägger till den nya disken

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Lägg till en ny disk antingen med PowerShell, Azure CLI eller Azure Portal. Se hur du [kopplar en disk](attach-disk-portal.md) för att referera till att lägga till diskar i en virtuell dator.

    Efter det att enhetens namn schema används för de enheter som den nya enheten vanligt vis tilldelas nästa tillgängliga bokstav, i det här fallet är den nya extra disken SDD.

4. Kontrol lera diskarna när du har lagt till den nya disken

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Skapa ett fil system ovanpå den nyligen tillagda disken

    Matcha den nyligen tillagda disken med de länkade enheterna på/dev/disk/Azure/SCSI1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenario-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenario – mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Skapa en ny temporär monterings punkt för den nya disk som har lagts till

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Lägg till det nyligen skapade fil systemet i/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Montera den nya skapade FS med Mount-a

    ``` bash
    mount -a
    ```

9. Verifiera att den nya tillagda FS-filen är monterad

    ``` bash
    df -h
    ```

    ![ändra storlek – LVM – scenariot-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![ändra storlek – LVM – scenariot-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Starta om den kryptering som har startats tidigare för data enheter

    För LVM-on-Encryption rekommenderar vi att du använder EncryptFormatAll, annars kan en dubbel kryptering uppstå medan du anger ytterligare diskar.

    Information om användning finns i [Konfigurera LVM på crypt](how-to-configure-lvm-raid-on-crypt.md).

    Exempel:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    När krypteringen är klar visas ett krypterings lager på den nyligen tillagda disken

    ``` bash
    lsblk
    ```

    ![scenario – lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Demontera det krypterade lagret på den nya disken

    ``` bash
    umount ${newmount}
    ```

12. Kontrol lera den aktuella PVS-informationen

    ``` bash
    pvs
    ```

    ![scenario – currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Skapa ett PV ovanpå det krypterade lagret på disken

    Hämta enhets namnet från föregående lsblk-kommando och Lägg till/dev/mapper framför enhets namnet för att skapa PV

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenario – pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    En varning visas när den aktuella ext4 FS-signaturen rensas, detta förväntas, svaret y till den här frågan

14. Kontrol lera att det nya PV har lagts till i LVM-konfigurationen

    ``` bash
    pvs
    ```

    ![scenario – newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Lägg till det nya PV-VG som du behöver öka

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenario – vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Kontrol lera den nya storleken och det fria utrymmet på VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenario – vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Observera ökningen av totalt antal PE och kostnads fri PE/storlek

17. Öka storleken på LV och fil systemet med alternativet-r på lvextend (i det här exemplet tar vi upp det totala tillgängliga utrymmet i VG och lägger till det i den aktuella logiska volymen)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenario – lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Verifiera storleken på LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario – lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Kontrol lera storleken på det fil system som du just har ändrat storlek på

    ``` bash
    df -h mountpoint
    ```

    ![scenario – DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Kontrol lera att LVM-skiktet skapas ovanpå det krypterade lagret

    ``` bash
    lsblk
    ```

    ![scenario – lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Om du använder lsblk utan alternativ visas flera gånger eftersom de sorteras efter enhet och logiska volymer, kanske du vill använda lsblk-FS,-s kastar om sorteringen så att mountpoints visas en gång, så visas diskarna flera gånger.

    ``` bash
    lsblk -fs
    ```

    ![scenario – lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Utöka en LVM på krypterings volym för att ändra storlek på ett befintligt PV

1. Identifiera dina krypterade diskar

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Kontrol lera din PV-information

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Kontrol lera din VG-information

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Kontrol lera din LV-information

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Kontrol lera fil Systems användningen

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Kontrol lera disk storlekarna

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Ändra storlek på datadisken

    Du kan referera till [Linux Expand diskar](expand-disks.md) (du kan bara se storleken på disken), du kan använda portalen, CLI eller PowerShell för att utföra det här steget.

    >[!NOTE]
    >Tänk på att det inte går att utföra åtgärder för att ändra storlek på virtuella diskar med den virtuella datorn som körs. Du måste frigöra den virtuella datorn för det här steget

8. Kontrol lera disk storlekarna

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Observera att (i det här fallet) storleken på båda diskarna ändrades från 2 GB till 4 GB, men FS-, LV-och PV-storleken förblir desamma.

9. Kontrol lera aktuell PV-storlek

    Kom ihåg att vid LVM-on-Encryption är PV/dev/mapper/-enheten, inte/dev/SD *-enheten

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Ändra storlek på PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof – ändra storlek – PV](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Kontrol lera PV-storleken efter storleks ändring

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof – PV](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Ändra storlek på det krypterade lagret på PV

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Använd samma procedur för alla diskar som du vill ändra storlek på.

13. Kontrol lera din VG-information

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG har nu utrymme för att allokeras till LVs

14. Kontrol lera LV-informationen

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof – LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Kontrol lera FS-användningen

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof – FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Ändra storlek på LV

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Vi använder alternativet-r för att också utföra FS-ändra storlek

17. Kontrol lera LV-informationen

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Kontrol lera fil Systems användningen

    ``` bash
    df -h /mountpoint
    ```

    ![Skapa fil system](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Tillämpa samma storlek på ändrings proceduren på alla ytterligare LV som kräver det

## <a name="next-steps"></a>Nästa steg

- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
