---
title: Felsöka startproblem för virtuella linux-datorer på grund av fstab-fel | Microsoft-dokument
description: Förklarar varför Linux VM inte kan starta och hur man löser problemet.
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351155"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Felsöka startproblem för Linux-vm på grund av fstab-fel

Du kan inte ansluta till en virtuell Azure Linux-dator (VM) med hjälp av en SSH-anslutning (Secure Shell). När du kör funktionen [Starta diagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) på [Azure-portalen](https://portal.azure.com/)visas loggposter som liknar följande exempel:

## <a name="examples"></a>Exempel

Följande är exempel på möjliga fel.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exempel 1: En disk monteras av SCSI-ID:t i stället för den universellt unika identifieraren (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exempel 2: En obunden enhet saknas på CentOS

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Exempel 3: En virtuell dator kan inte starta på grund av en felkonfiguration av fstab eller på grund av att disken inte längre är ansluten

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Exempel 4: En seriell loggpost visar en felaktig UUID

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

Det här problemet kan uppstå om syntaxen för filsystemtabellen (fstab) är felaktig eller om en nödvändig datadisk som mappas till en post i filen "/etc/fstab" inte är kopplad till den virtuella datorn.

## <a name="resolution"></a>Lösning

LÃ¶s problemet genom att starta den virtuella datorn i nödläge med hjälp av den seriella konsolen för Virtuella Azure-datorer. Använd sedan verktyget för att reparera filsystemet. Om seriekonsolen inte är aktiverad på den virtuella datorn går du till avsnittet [Reparera den virtuella datorn offline.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Använda seriekonsolen

### <a name="using-single-user-mode"></a>Använda enanvändarläge

1. Anslut till [seriekonsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Använd seriekonsol för att ta [enanvändarläge med en användare](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. När den virtuella datorn har startats upp i enanvändarläge. Använd din favorittextredigerare för att öppna fstab-filen. 

   ```
   # nano /etc/fstab
   ```

4. Granska de listade filsystemen. Varje rad i fstab-filen anger ett filsystem som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstabfilen kör du kommandot man fstab. Om du vill felsöka ett startfel granskar du varje rad för att se till att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med flikar eller blanksteg. Tomma rader ignoreras. Rader som har ett taltecken (#) som första tecken är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de kommer inte att bearbetas. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställas och starta bör filsystempartitionerna vara de enda nödvändiga partitionerna. Den virtuella datorn kan uppstå programfel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare avkommenta några kommenterade rader.
   > * Vi rekommenderar att du monterar datadiskar på virtuella Azure-datorer med hjälp av UUID för filsystempartitionen. Kör till exempel följande kommando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Om du vill ta reda på filsystemets UUID kör du kommandot blkid. Om du vill ha mer information om syntaxen kör du kommandot man blkid.
   > * Alternativet inget fel hjälper till att se till att den virtuella datorn startar även om filsystemet är skadat eller om filsystemet inte finns vid start. Vi rekommenderar att du använder alternativet nofail i fstab-filen för att aktivera start för att fortsätta när fel har inträffat i partitioner som inte krävs för att den virtuella datorn ska starta.

5. Ändra eller kommentera felaktiga eller onödiga rader i fstab-filen så att den virtuella datorn kan starta korrekt.

6. Spara ändringarna i fstab-filen.

7. Starta om den virtuella datorn med kommandot nedan.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Du kan också använda kommandot "ctrl+x" som också skulle starta om den virtuella datorn.


8. Om postkommentaren eller korrigeringen lyckades bör systemet nå en bash-prompt i portalen. Kontrollera om du kan ansluta till den virtuella datorn.

### <a name="using-root-password"></a>Använda root-lösenord

1. Anslut till [seriekonsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Logga in på systemet med hjälp av en lokal användare och ett lokalt lösenord.

   > [!Note]
   > Du kan inte använda en SSH-nyckel för att logga in på systemet i seriekonsolen.

3. Leta efter felet som indikerar att disken inte var monterad. I följande exempel försökte systemet ansluta en disk som inte längre fanns:

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

4. Anslut till den virtuella datorn med hjälp av rotlösenordet (Red Hat-baserade virtuella datorer).

5. Använd din favorittextredigerare för att öppna fstab-filen. När disken har monterats kör du följande kommando för Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Granska de listade filsystemen. Varje rad i fstab-filen anger ett filsystem som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstabfilen kör du kommandot man fstab. Om du vill felsöka ett startfel granskar du varje rad för att se till att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med flikar eller blanksteg. Tomma rader ignoreras. Rader som har ett taltecken (#) som första tecken är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de kommer inte att bearbetas. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställas och starta bör filsystempartitionerna vara de enda nödvändiga partitionerna. Den virtuella datorn kan uppstå programfel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare avkommenta några kommenterade rader.
   > * Vi rekommenderar att du monterar datadiskar på virtuella Azure-datorer med hjälp av UUID för filsystempartitionen. Kör till exempel följande kommando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Om du vill ta reda på filsystemets UUID kör du kommandot blkid. Om du vill ha mer information om syntaxen kör du kommandot man blkid.
   > * Alternativet inget fel hjälper till att se till att den virtuella datorn startar även om filsystemet är skadat eller om filsystemet inte finns vid start. Vi rekommenderar att du använder alternativet nofail i fstab-filen för att aktivera start för att fortsätta när fel har inträffat i partitioner som inte krävs för att den virtuella datorn ska starta.

7. Ändra eller kommentera felaktiga eller onödiga rader i fstab-filen så att den virtuella datorn kan starta korrekt.

8. Spara ändringarna i fstab-filen.

9. Starta om den virtuella datorn.

10. Om postkommentaren eller korrigeringen lyckades bör systemet nå en bash-prompt i portalen. Kontrollera om du kan ansluta till den virtuella datorn.

11. Kontrollera dina monteringspunkter när du testar någon fstab förändring genom att köra montera-ett kommando. Om det inte finns några fel, bör dina monteringspunkter vara bra.

## <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. Koppla systemdisken för den virtuella datorn som en datadisk till en återställnings-VM (alla fungerande Linux VM). För att göra detta kan du använda [CLI-kommandon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) eller så kan du automatisera inrättandet av återställnings-VM med hjälp av [vm-reparationskommandona](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. När du har monterat systemdisken som en datadisk på återställningsdatorn säkerhetskopierar du fstab-filen innan du gör ändringar och följer sedan nästa steg för att korrigera fstab-filen.

3.    Leta efter felet som anger att disken inte var monterad. I följande exempel försökte systemet ansluta en disk som inte längre fanns:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Anslut till den virtuella datorn med hjälp av rotlösenordet (Red Hat-baserade virtuella datorer).

5. Använd din favorittextredigerare för att öppna fstab-filen. När disken har monterats kör du följande kommando för Nano. Kontrollera att du arbetar med fstab-filen som finns på den monterade disken och inte fstab-filen som finns på den virtuella räddningsdatorn.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Granska de listade filsystemen. Varje rad i fstab-filen anger ett filsystem som är monterat när den virtuella datorn startar. Om du vill ha mer information om syntaxen för fstabfilen kör du kommandot man fstab. Om du vill felsöka ett startfel granskar du varje rad för att se till att den är korrekt i både struktur och innehåll.

   > [!Note]
   > * Fält på varje rad avgränsas med flikar eller blanksteg. Tomma rader ignoreras. Rader som har ett taltecken (#) som första tecken är kommentarer. Kommenterade rader kan finnas kvar i fstab-filen, men de kommer inte att bearbetas. Vi rekommenderar att du kommenterar fstab-rader som du är osäker på i stället för att ta bort raderna.
   > * För att den virtuella datorn ska kunna återställas och starta bör filsystempartitionerna vara de enda nödvändiga partitionerna. Den virtuella datorn kan uppstå programfel om ytterligare kommenterade partitioner. Den virtuella datorn bör dock starta utan ytterligare partitioner. Du kan senare avkommenta några kommenterade rader.
   > * Vi rekommenderar att du monterar datadiskar på virtuella Azure-datorer med hjälp av UUID för filsystempartitionen. Kör till exempel följande kommando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Om du vill ta reda på filsystemets UUID kör du kommandot blkid. Om du vill ha mer information om syntaxen kör du kommandot man blkid. Observera att den disk som du vill återställa nu är monterad på en ny virtuell dator. Även om UUID:erna bör vara konsekventa, är enhetspartitions-ID:na (till exempel "/dev/sda1") olika på den här virtuella datorn. Filsystempartitionerna för den ursprungliga virtuella datorn som inte fungerar som den ska vara den som inte är system-VHD:er är inte tillgängliga för återställningsdatorn [med CLI-kommandon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * Alternativet inget fel hjälper till att se till att den virtuella datorn startar även om filsystemet är skadat eller om filsystemet inte finns vid start. Vi rekommenderar att du använder alternativet nofail i fstab-filen för att aktivera start för att fortsätta när fel har inträffat i partitioner som inte krävs för att den virtuella datorn ska starta.

7. Ändra eller kommentera felaktiga eller onödiga rader i fstab-filen så att den virtuella datorn kan starta korrekt.

8. Spara ändringarna i fstab-filen.

9. Starta om den virtuella datorn eller återskapa den ursprungliga virtuella datorn.

10. Om postkommentaren eller korrigeringen lyckades bör systemet nå en bash-prompt i portalen. Kontrollera om du kan ansluta till den virtuella datorn.

11. Kontrollera dina monteringspunkter när du testar någon fstab förändring genom att köra montera-ett kommando. Om det inte finns några fel, bör dina monteringspunkter vara bra.

12. Avmontera och koppla från den ursprungliga virtuella hårddisken och skapa sedan en virtuell dator från den ursprungliga systemdisken. För att göra detta kan du använda [CLI-kommandon](troubleshoot-recovery-disks-linux.md) eller [vm-reparationskommandon](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) om du använde dem för att skapa återställningsdass.

13. När du har skapat den virtuella datorn igen och du kan ansluta till den via SSH vidtar du följande åtgärder:
    * Granska någon av de fstab linjer som ändrats eller kommenterades ut under återhämtningen.
    * Kontrollera att du använder UUID och alternativet inget svikligt på rätt sätt.
    * Testa eventuella fstab ändringar innan du startar om den virtuella datorn. Det gör du genom att använda följande kommando:``$ sudo mount -a``
    * Skapa ytterligare en kopia av den korrigerade fstab-filen för användning i framtida återställningsscenarier.

## <a name="next-steps"></a>Nästa steg

* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en återställnings-VM med Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell återställnings-DATOR med Azure-portalen](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

