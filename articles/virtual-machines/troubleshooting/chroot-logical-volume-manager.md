---
title: Återställa virtuella Linux-datorer med chroot där LVM (Logisk volymhanterare) används - Virtuella Azure-datorer
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
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684131"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Felsöka en Virtuell Linux-dator när det inte finns åtkomst till Azure-seriekonsolen och disklayouten använder LVM (Logisk volymhanterare)

Den här felsökningsguiden är till nytta för scenarier där en Virtuell Linux-dator inte startar, ssh inte är möjlig och den underliggande filsystemlayouten är konfigurerad med LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Ta ögonblicksbild av den felaktiga virtuella datorn

Ta en ögonblicksbild av den berörda virtuella datorn. 

Ögonblicksbilden kommer sedan att **rescue** kopplas till en räddnings-VM. Följ instruktionerna [här](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) om hur du tar en **ögonblicksbild**.

## <a name="create-a-rescue-vm"></a>Skapa en räddnings-VM
Vanligtvis rekommenderas en räddnings-VM med samma eller liknande operativsystemversion. Använda samma **region** och **resursgrupp** för den berörda virtuella datorn

## <a name="connect-to-the-rescue-vm"></a>Anslut till räddnings-VM
Anslut med ssh i **rädda** VM. Höj privilegierna och bli superanvändare med

`sudo su -`

## <a name="attach-the-disk"></a>Koppla disken
Koppla en disk till **den virtuella räddningsdatorn** som gjorts från ögonblicksbilden som tagits tidigare.

Azure portal -> välja **räddnings-VM** -> **Diskar** 

![Skapa disk](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Fyll i fälten. Tilldela ett namn till den nya disken, välj samma resursgrupp som ögonblicksbilden, den berörda virtuella datorn och den virtuella datorn för räddningsinsatser.

**Källtypen** är **Snapshot** .
**Ögonblicksbilden Källa** är namnet på den **ögonblicksbild** som tidigare skapats.

![skapa disk 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Skapa en monteringspunkt för den bifogade disken.

`mkdir /rescue`

Kör **fdisk -l-kommandot** för att verifiera att ögonblicksbilddisken har anslutits och lista alla enheter och partitioner som är tillgängliga

`fdisk -l`

De flesta scenarier, den bifogade ögonblicksbild disken kommer att ses som **/dev/sdc** visar två partitioner **/dev/sdc1** och **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Anger **\*** en startpartition, båda partitionerna ska monteras.

Kör kommandot **lsblk** för att se LVM:erna för den berörda virtuella datorn

`lsblk`

![Kör lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Kontrollera om LVM från den berörda virtuella datorn visas.
Om inte, använd nedanstående kommandon för att aktivera dem och köra **lsblk**igen .
Se till att LVM från den anslutna disken är synlig innan du fortsätter.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Leta reda på sökvägen för att montera den logiska volymen som innehåller / (rot) partitionen. Den har konfigurationsfiler som / etc / default / grub

I det här exemplet är det rätt **rot** LV att montera och kan användas i nästa kommando om du tar utdata från det tidigare **kommandot lsblk.** **rootvg-rootlv**

Utdata från nästa kommando visar sökvägen som ska monteras för **roten** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv (rot)](./media/chroot-logical-volume-manager/locate-rootlv.png)

Fortsätt att montera den här enheten på katalogen /rescue

`mount /dev/rootvg/rootlv /rescue`

Montera partitionen som har **flaggan Boot** inställd på /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Kontrollera att filsystemen på den anslutna disken nu är korrekt monterade med **kommandot lsblk**

![Kör lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

eller **kommandot df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Få chroot-åtkomst

Få **chroot-åtkomst,** vilket gör att du kan utföra olika korrigeringar, små variationer finns för varje Linux-distribution.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Om ett fel uppstår, till exempel:

**chroot: misslyckades med att köra kommandot "/bin/bash": Ingen sådan fil eller katalog**

försök att montera **usr** logisk volym

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> När du kör kommandon i en **chroot-miljö,** observera att de körs mot den anslutna OS-disken och inte den lokala **räddningsdatorn.** 

Kommandon kan användas för att installera, ta bort och uppdatera programvara. Felsöka virtuella datorer för att åtgärda fel.


Utför kommandot lsblk och /rescue är nu / och ![/rescue/boot is /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Utför korrigeringar

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exempel 1 - konfigurera den virtuella datorn så att den startas från en annan kärna

Ett vanligt scenario är att tvinga en virtuell dator att starta från en tidigare kärna eftersom den aktuella installerade kärnan kan ha skadats eller en uppgradering inte slutfördes korrekt.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

Kommandot **grep** listar de kärnor som **grub.cfg** är medvetna om.
![Kernels](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv lista** visar vilken kärna som ![kommer att laddas vid nästa boot Kernel standard](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** används för att ![ändra till en annan kärna Grub2 set](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** lista visar vilken kärna som ![kommer att laddas vid nästa start Ny kärna](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** återuppbygger grub.cfg ![med hjälp av de versioner som krävs Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exempel 2 - uppgraderingspaket

En misslyckad kernel-uppgradering kan göra den virtuella datorn inte startbar.
Montera alla logiska volymer så att paket kan tas bort eller installeras om

Kör **kommandot lvs** för att verifiera vilka **LVs som** är tillgängliga för montering, varje virtuell dator, som har migrerats eller kommer från en annan molnleverantör, varierar i konfiguration.

Avsluta **chroot-miljön** montera önskad **LV**

![Avancerat](./media/chroot-logical-volume-manager/advanced.png)

Nu tillgång till **chroot** miljön igen genom att köra

`chroot /rescue`

Alla LVs ska vara synliga som monterade partitioner

![Avancerat](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Fråga den installerade **kärnan**

![Avancerat](./media/chroot-logical-volume-manager/rpm-kernel.png)

Vid behov ta bort eller uppgradera **kärnan**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exempel 3 - aktivera seriekonsol
Om åtkomst inte har varit möjlig till Azure-seriekonsolen kontrollerar du GRUB-konfigurationsparametrar för din Virtuella Linux-dator och korrigerar dem. Detaljerad information finns [i detta dokument](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Exempel 4 - kärnladdning med problematisk LVM-swapvolym

En virtuell dator kan misslyckas **dracut** med att starta helt och sjunker in i dracut-prompten.
Mer information om felet kan finnas från antingen Azure seriell konsol eller navigera till Azure Portal -> startdiagnostik -> Seriell logg


Ett liknande fel kan förekomma:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub.cfg är konfigurerad i det här exemplet för att läsa in en LV med namnet **rd.lvm.lv=VG/SwapVol** och den virtuella datorn kan inte hitta detta. Den här raden visar hur kärnan läses in med hänvisning till LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Ta bort den felande LV från / etc / default / grub konfiguration och återuppbygga grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Avsluta chroot och byta OS-disken

När du har reparerat problemet fortsätter du att avmontera och ta bort disken från den virtuella räddningsdatorn så att den kan bytas ut mot den berörda VM OS-disken.

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

Koppla bort disken från den virtuella räddningsdatorn och utför en diskswat.

Välj den virtuella datorn från **portaldiskarna** och välj **koppla från**
![från disk](./media/chroot-logical-volume-manager/detach-disk.png) 

Spara ändringarna ![Spara löste](./media/chroot-logical-volume-manager/save-detach.png) 

Disken kommer nu att bli tillgänglig så att den kan bytas med den ursprungliga OS-disken för den berörda virtuella datorn.

Navigera i Azure-portalen till den felaktiga virtuella datorn och välj **Disks** -> **Swap OS Disk**
![Swap disk disk](./media/chroot-logical-volume-manager/swap-disk.png) 

Slutför fälten **Välj disken** är ögonblicksbild disken just lossnat i föregående steg. Vm-namnet på den berörda virtuella datorn krävs också och **välj** OK

![Ny os-disk](./media/chroot-logical-volume-manager/new-osdisk.png) 

Om den virtuella datorn kör diskbytet kommer att stänga av den, starta om den virtuella datorn när diskswatåtgärden har slutförts.


## <a name="next-steps"></a>Nästa steg
Lär dig mer om

 [Seriell konsol i Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Läget För en användare](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
