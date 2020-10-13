---
title: Felsöka virtuella Linux-datorer med start problem på grund av fstab-fel | Microsoft Docs
description: Förklarar varför virtuella Linux-datorer inte kan starta och lösa problemet.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441671"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Felsöka virtuella Linux-datorer med start problem på grund av fstab-fel

Det går inte att ansluta till en virtuell Azure Linux-dator (VM) med hjälp av en SSH-anslutning (Secure Shell). När du kör funktionen för [startdiagnostik](./boot-diagnostics.md) på [Azure Portal](https://portal.azure.com/), ser du logg poster som liknar följande exempel:

## <a name="examples"></a>Exempel

Följande är exempel på möjliga fel.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exempel 1: en disk monteras av SCSI-ID: t i stället för den universella unika identifieraren (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exempel 2: en ej ansluten enhet saknas på CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Exempel 3: det går inte att starta en virtuell dator på grund av en felaktig fstab eller på grund av att disken inte längre är ansluten

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Exempel 4: en seriell logg post visar felaktig UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Det här problemet kan uppstå om fstab-syntaxen (File System Table) är felaktig eller om en nödvändig datadisk som är mappad till en post i "/etc/fstab"-filen inte är kopplad till den virtuella datorn.

## <a name="resolution"></a>Lösning

Lös problemet genom att starta den virtuella datorn i nödfalls läge med hjälp av serie konsolen för Azure Virtual Machines. Använd sedan verktyget för att reparera fil systemet. Om serie konsolen inte är aktive rad på den virtuella datorn går du till avsnittet [reparera den virtuella datorn offline](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Använda seriekonsolen

### <a name="using-single-user-mode"></a>Använda enanvändarläge

1. Anslut till [serie konsolen](./serial-console-linux.md).
2. Använd [en serie](serial-console-grub-single-user-mode.md) konsol för att ta enanvändarläge för enanvändarläge
3. När den virtuella datorn har startats i enanvändarläge. Använd din text redigerare för att öppna filen fstab. 

   ```
   # nano /etc/fstab
   ```

4. Granska de listade fil systemen. Varje rad i fstab-filen indikerar ett fil system som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstab-filen kör man man The man fstab kommandot. Om du vill felsöka ett startfel granskar du varje rad för att kontrol lera att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med tabbar eller blank steg. Tomma rader ignoreras. Rader som har ett nummer tecken (#) som det första tecknet är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de bearbetas inte. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställa och starta ska fil system partitionerna vara de enda partitioner som krävs. Den virtuella datorn kan uppleva program fel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare ta bort kommentarer till kommenterade rader.
   > * Vi rekommenderar att du monterar data diskar på virtuella Azure-datorer med hjälp av fil system partitionens UUID. Kör till exempel följande kommando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Ta reda på fil systemets UUID genom att köra kommandot blkid. Om du vill ha mer information om syntaxen kan du köra kommandot man blkid.
   > * Alternativet nomisslyckande ser till att den virtuella datorn startar även om fil systemet är skadat eller om fil systemet inte finns vid start. Vi rekommenderar att du använder alternativet nomisslyckande i fstab-filen för att aktivera start för att fortsätta när fel uppstår i partitioner som inte krävs för att den virtuella datorn ska starta.

5. Ändra eller kommentera eventuella felaktiga eller raderade rader i fstab-filen för att göra det möjligt att starta den virtuella datorn korrekt.

6. Spara ändringarna i fstab-filen.

7. Starta om den virtuella datorn med kommandot nedan.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Du kan också använda kommandot "Ctrl + x" som också startar om den virtuella datorn.


8. Om posterna kommentaren eller korrigeringen lyckades, ska systemet komma åt en bash-prompt i portalen. Kontrol lera om du kan ansluta till den virtuella datorn.

### <a name="using-root-password"></a>Använda rot lösen ord

1. Anslut till [serie konsolen](./serial-console-linux.md).
2. Logga in på systemet med hjälp av en lokal användare och ett lösen ord.

   > [!Note]
   > Du kan inte använda en SSH-nyckel för att logga in på systemet i serie konsolen.

3. Leta efter det fel som indikerar att disken inte har monterats. I följande exempel försökte systemet ansluta en disk som inte längre är tillgänglig:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Anslut till den virtuella datorn med hjälp av rot lösen ordet (Red Hat-baserade virtuella datorer).

5. Använd din text redigerare för att öppna filen fstab. När disken har monterats kör du följande kommando för nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Granska de listade fil systemen. Varje rad i fstab-filen indikerar ett fil system som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstab-filen kör man man The man fstab kommandot. Om du vill felsöka ett startfel granskar du varje rad för att kontrol lera att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med tabbar eller blank steg. Tomma rader ignoreras. Rader som har ett nummer tecken (#) som det första tecknet är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de bearbetas inte. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställa och starta ska fil system partitionerna vara de enda partitioner som krävs. Den virtuella datorn kan uppleva program fel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare ta bort kommentarer till kommenterade rader.
   > * Vi rekommenderar att du monterar data diskar på virtuella Azure-datorer med hjälp av fil system partitionens UUID. Kör till exempel följande kommando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Ta reda på fil systemets UUID genom att köra kommandot blkid. Om du vill ha mer information om syntaxen kan du köra kommandot man blkid.
   > * Alternativet nomisslyckande ser till att den virtuella datorn startar även om fil systemet är skadat eller om fil systemet inte finns vid start. Vi rekommenderar att du använder alternativet nomisslyckande i fstab-filen för att aktivera start för att fortsätta när fel uppstår i partitioner som inte krävs för att den virtuella datorn ska starta.

7. Ändra eller kommentera eventuella felaktiga eller raderade rader i fstab-filen för att göra det möjligt att starta den virtuella datorn korrekt.

8. Spara ändringarna i fstab-filen.

9. Starta om den virtuella datorn.

10. Om posterna kommentaren eller korrigeringen lyckades, ska systemet komma åt en bash-prompt i portalen. Kontrol lera om du kan ansluta till den virtuella datorn.

11. Kontrol lera monterings punkterna när du testar en fstab ändring genom att köra kommandot Mount – a. Om det inte finns några fel bör monterings punkterna vara lämpliga.

## <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. Anslut den virtuella datorns system disk till en datadisk till en virtuell dator för återställning (valfri fungerande Linux-dator). Om du vill göra detta kan du använda [CLI-kommandon](./troubleshoot-recovery-disks-linux.md) eller automatisera konfigurationen av den virtuella återställnings datorn med hjälp av [kommandona för reparation av virtuella datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. När du har monterat system disken som en datadisk på den virtuella återställnings datorn säkerhetskopierar du fstab-filen innan du gör några ändringar och följer sedan anvisningarna i nästa steg för att korrigera fstab-filen.

3. Leta efter felet som anger att disken inte har monterats. I följande exempel försökte systemet ansluta en disk som inte längre är tillgänglig:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Anslut till den virtuella datorn med hjälp av rot lösen ordet (Red Hat-baserade virtuella datorer).

5. Använd din text redigerare för att öppna filen fstab. När disken har monterats kör du följande kommando för nano. Se till att du arbetar med den fstab-fil som finns på den monterade disken och inte på den fstab-fil som finns på den virtuella räddnings datorn.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Granska de listade fil systemen. Varje rad i fstab-filen indikerar ett fil system som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstab-filen kör man man The man fstab kommandot. Om du vill felsöka ett startfel granskar du varje rad för att kontrol lera att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med tabbar eller blank steg. Tomma rader ignoreras. Rader som har ett nummer tecken (#) som det första tecknet är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de bearbetas inte. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställa och starta ska fil system partitionerna vara de enda partitioner som krävs. Den virtuella datorn kan uppleva program fel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare ta bort kommentarer till kommenterade rader.
   > * Vi rekommenderar att du monterar data diskar på virtuella Azure-datorer med hjälp av fil system partitionens UUID. Kör till exempel följande kommando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Ta reda på fil systemets UUID genom att köra kommandot blkid. Om du vill ha mer information om syntaxen kan du köra kommandot man blkid. Observera att den disk som du vill återställa nu har monterats på en ny virtuell dator. Även om UUID: na bör vara konsekventa är partitions-ID: n (till exempel "/dev/sda1") olika på den här virtuella datorn. Fil system partitionerna för den ursprungliga virtuella datorn som finns på en icke-systemhårddisk är inte tillgängliga för den virtuella återställnings datorn [med CLI-kommandon](./troubleshoot-recovery-disks-linux.md).
   > * Alternativet nomisslyckande ser till att den virtuella datorn startar även om fil systemet är skadat eller om fil systemet inte finns vid start. Vi rekommenderar att du använder alternativet nomisslyckande i fstab-filen för att aktivera start för att fortsätta när fel uppstår i partitioner som inte krävs för att den virtuella datorn ska starta.

7. Ändra eller kommentera eventuella felaktiga eller raderade rader i fstab-filen för att göra det möjligt att starta den virtuella datorn korrekt.

8. Spara ändringarna i fstab-filen.

9. Starta om den virtuella datorn eller återskapa den ursprungliga virtuella datorn.

10. Om posterna kommentaren eller korrigeringen lyckades, ska systemet komma åt en bash-prompt i portalen. Kontrol lera om du kan ansluta till den virtuella datorn.

11. Kontrol lera monterings punkterna när du testar en fstab ändring genom att köra kommandot Mount – a. Om det inte finns några fel bör monterings punkterna vara lämpliga.

12. Demontera och koppla från den ursprungliga virtuella hård disken och skapa sedan en virtuell dator från den ursprungliga system disken. Om du vill göra detta kan du använda [CLI-kommandon](troubleshoot-recovery-disks-linux.md) eller [reparations kommandon för virtuella datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) om du använde dem för att skapa den virtuella återställnings datorn.

13. När du har skapat den virtuella datorn igen och du kan ansluta till den via SSH, vidta följande åtgärder:
    * Granska alla fstab-rader som har ändrats eller kommenterats ut under återställningen.
    * Kontrol lera att du använder UUID och alternativet nomisslyckande på lämpligt sätt.
    * Testa eventuella fstab ändringar innan du startar om den virtuella datorn. Använd följande kommando för att göra detta: ``$ sudo mount -a``
    * Skapa ytterligare en kopia av den korrigerade fstab-filen och Använd den i framtida återställnings scenarier.

## <a name="next-steps"></a>Nästa steg

* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell dator för återställning med Azure CLI 2,0](./troubleshoot-recovery-disks-linux.md)
* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal](./troubleshoot-recovery-disks-portal-linux.md)
