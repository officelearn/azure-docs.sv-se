---
title: Återställa virtuella Linux-datorer med chroot där LVM (Logical Volume Manager) används – virtuella Azure-datorer
description: Återställning av virtuella Linux-datorer med LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 390443874ea63a8661ef8baea627015fcf679719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002705"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Felsöka en virtuell Linux-dator när det inte finns någon åtkomst till Azures serie konsol och disklayouten använder LVM (Logical Volume Manager)

Den här fel söknings guiden är en del av fördelarna med att en virtuell Linux-dator inte startar, SSH inte är möjligt och den underliggande fil systemets layout är konfigurerad med LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Ta ögonblicks bilder av den virtuella datorn som slutar fungera

Ta en ögonblicks bild av den berörda virtuella datorn. 

Ögonblicks bilden kopplas sedan till en **räddnings** dator. Följ anvisningarna [här](../linux/snapshot-copy-managed-disk.md#use-azure-portal) om hur du tar en **ögonblicks bild**.

## <a name="create-a-rescue-vm"></a>Skapa en räddnings dator
Vanligt vis rekommenderas en räddad virtuell dator av samma eller liknande operativ system version. Använd samma **region** och **resurs grupp** för den berörda virtuella datorn

## <a name="connect-to-the-rescue-vm"></a>Anslut till den räddande virtuella datorn
Anslut med SSH till den **räddande** virtuella datorn. Höj behörigheten och bli super user med

`sudo su -`

## <a name="attach-the-disk"></a>Anslut disken
Anslut en disk till den **räddande** virtuella datorn från ögonblicks bilden som togs tidigare.

Azure Portal-> väljer du den virtuella datorn för **räddnings** tjänsten – > **diskar** 

![Skapa disk](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Fyll i fälten. Tilldela en ny disk ett namn, Välj samma resurs grupp som ögonblicks bilden, virtuell dator som påverkas och rädda VM.

**Käll typen** är **ögonblicks bild** .
**Käll ögonblicks bilden** är namnet på **ögonblicks bilden** som skapades tidigare.

![Skapa disk 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Skapa en monterings punkt för den anslutna disken.

`mkdir /rescue`

Kör kommandot **fdisk-l** för att kontrol lera att ögonblicks bild disken har kopplats och visar en lista över alla enheter och partitioner som är tillgängliga

`fdisk -l`

I de flesta fall visas den anslutna ögonblicks bild disken som **/dev/SDC** som visar två partitioner **/dev/sdc1** och **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

* *\** _ Anger en startpartition, båda partitionerna ska monteras.

Kör kommandot _ *lsblk** för att se LVMs för den berörda virtuella datorn

`lsblk`

![Skärm bild som visar utdata från kommandot lsblk.](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Kontrol lera om LVMs från den berörda virtuella datorn visas.
Annars kan du använda nedanstående kommandon för att aktivera dem och köra **lsblk** igen.
Se till att ha LVMs från den anslutna disken synlig innan du fortsätter.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Leta upp sökvägen för att montera den logiska volym som innehåller partitionen/(roten). Den har konfigurationsfiler som/etc/default/grub

I det här exemplet är det rätt **rot** -LV att montera och kan användas i nästa kommando för att ta utdata från föregående **lsblk** **-kommando rootvg-rootlv** .

Utdata från nästa kommando visar sökvägen för att montera för **rot** -LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Fortsätt att montera enheten på katalogen/Rescue

`mount /dev/rootvg/rootlv /rescue`

Montera partitionen som har **Start flaggan** inställd på/Rescue/Boot

`
mount /dev/sdc1 /rescue/boot
`

Verifiera att fil systemen för den anslutna disken nu är korrekt monterade med kommandot **lsblk**

![Kör lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

eller det **DF** kommandot

![Beräknas](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Få åtkomst till chroot

Få åtkomst till **chroot** , vilket gör att du kan utföra olika korrigeringar, det finns små variationer för varje Linux-distribution.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Om ett fel uppstår, till exempel:

**chroot: det gick inte att köra kommandot '/bin/bash ': ingen sådan fil eller katalog**

försök att montera den logiska **usr** -volymen

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Observera att de körs mot den anslutna OS-disken och inte den lokala **räddnings** datorn när du kör kommandon i en **chroot** -miljö. 

Kommandon kan användas för att installera, ta bort och uppdatera program vara. Felsök virtuella datorer för att åtgärda fel.


Kör kommandot lsblk och/Rescue är nu/och/Rescue/boot är/boot ![ skärm bild visar ett konsol fönster med kommandot l s BLK och dess utmatnings träd.](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Utför korrigeringar

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exempel 1 – Konfigurera den virtuella datorn för att starta från en annan kernel

Ett vanligt scenario är att tvinga en virtuell dator att starta från en tidigare kernel eftersom den aktuella installerade kerneln kan ha skadats eller att uppgraderingen inte slutfördes korrekt.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*genom gång*

**Grep** -kommandot visar de kärnor som **grub. cfg** är medvetna om.
![Skärm bild visar ett konsol fönster som visar resultatet av en grep-sökning för kernels.](./media/chroot-logical-volume-manager/kernels.png)

**grub2 – editenv** visar vilken kernel som ska läsas in vid nästa start- ![ kernel standard](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** används för att ändra till en annan kernel ![ grub2-uppsättning](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2 – editenv** visar vilken kernel som ska läsas in vid nästa start ![ nya kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** återkonstruerar grub. cfg med de versioner som krävs ![ grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exempel 2 – uppgraderings paket

En misslyckad kernel-uppgradering kan återge den virtuella datorn som inte är startbar.
Montera alla logiska volymer så att paket kan tas bort eller installeras om

Kör kommandot **LVS** för att kontrol lera vilka **LVS** som är tillgängliga för montering, varje virtuell dator som har migrerats eller kommer från en annan moln leverantör varierar i konfigurationen.

Avsluta **chroot** -miljön och montera den nödvändiga **LV**

![Skärm bild som visar ett-konsol fönster med kommandot l v s och sedan monterar en L V.](./media/chroot-logical-volume-manager/advanced.png)

Öppna nu **chroot** -miljön igen genom att köra

`chroot /rescue`

Alla LVs ska vara synliga som monterade partitioner

![Skärm bild som visar LVs som visas som monterade partitioner.](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Fråga den installerade **kerneln**

![Skärm bild som visar hur du frågar den installerade kärnan.](./media/chroot-logical-volume-manager/rpm-kernel.png)

Om det behövs tar du bort eller uppgraderar **kerneln** 
 ![ Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exempel 3 – Aktivera serie konsol
Om det inte går att få åtkomst till Azures serie konsol kontrollerar du GRUB konfigurations parametrar för den virtuella Linux-datorn och korrigerar dem. Detaljerad information finns [i det här dokumentet](./serial-console-grub-proactive-configuration.md)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Exempel 4 – kernel-inläsning med problematisk LVM växlings volym

En virtuell dator kanske inte kan starta helt och hållet i **dracut** -prompten.
Mer information om felen finns i Azures serie konsol eller navigera till Azure Portal-> startdiagnostik-> serie logg


Ett fel som liknar detta kan finnas:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub. cfg konfigureras i det här exemplet för att läsa in en LV med namnet **Rd. LVM. lv = VG/SwapVol** och den virtuella datorn kan inte hitta detta. Den här raden visar hur kärnan läses in som hänvisar till LV-SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Ta bort den felaktiga LV-konfigurationen från/etc/default/grub-konfigurationen och återskapa grub2. cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Avsluta chroot och Byt OS-disk

När du har reparerat problemet fortsätter du med att demontera och koppla bort disken från den rädda virtuella datorn så att den kan växlas med den berörda virtuella datorns OS-disk.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Koppla bort disken från den virtuella datorn för räddning och utför en disk växling.

Välj den virtuella datorn från Portal **diskarna** och välj **koppla** 
 ![ från från kopplings disk](./media/chroot-logical-volume-manager/detach-disk.png) 

Spara ändringarna ![ Spara från koppling](./media/chroot-logical-volume-manager/save-detach.png) 

Disken blir nu tillgänglig så att den kan växlas med den ursprungliga OS-disken för den berörda virtuella datorn.

Navigera i Azure Portal till den virtuella datorn som inte går att återställa och välj **diskar**  ->  **Växla disk för operativ system disk** 
 ![ växling](./media/chroot-logical-volume-manager/swap-disk.png) 

Fyll i fälten **Välj disk** är den ögonblicks bild disk som du just har frånkopplat i föregående steg. Det virtuella dator namnet för den berörda virtuella datorn måste också Välj **OK** .

![Ny OS-disk](./media/chroot-logical-volume-manager/new-osdisk.png) 

Om den virtuella datorn kör disk växlingen stängs den av, startar om den virtuella datorn när disk växlings åtgärden har slutförts.


## <a name="next-steps"></a>Nästa steg
Lär dig mer om

 [Azures serie konsol]( ./serial-console-linux.md)

[Läge för enkel användare](./serial-console-grub-single-user-mode.md)