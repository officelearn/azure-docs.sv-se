---
title: Felsöka startproblem för virtuella filer på grund av filsystemfel | Microsoft-dokument
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842409"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Felsöka startproblem för Linux-vm på grund av filsystemfel

Du kan inte ansluta till en virtuell Azure Linux-dator (VM) med secure shell (SSH). När du kör funktionen Starta diagnostik på [Azure-portalen](https://portal.azure.com/)visas loggposter som liknar följande exempel.

## <a name="examples"></a>Exempel

Följande är exempel på möjliga fel.

### <a name="example-1"></a>Exempel 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Exempel 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Exempel 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Exempel 4 

Det här exemplet orsakas av en ren fsck. I det här fallet finns det också ytterligare datadiskar som är kopplade till den virtuella datorn (/dev/sdc1 och /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Det hÃ¤r problemet kan uppstÃ¥ om filsystemet inte stängdes av cleanly eller lagringsrelaterade problem. Problemen omfattar maskin- eller programvarufel, problem med drivrutiner eller program, skrivfel etc. Det är alltid viktigt att ha en säkerhetskopia av kritiska data. Verktygen som beskrivs i den här artikeln kan hjälpa till att återställa filsystem, men det är dataförlust kan fortfarande förekomma.

Linux har flera filsystemkontroller tillgängliga. De vanligaste för distributionerna i Azure är: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)och [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Lösning

LÃ¶s problemet genom att starta den virtuella datorn i nödläge med hjälp av [den seriella konsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) och anhÃ¶r verktyget för att reparera filsystemet. Om seriekonsolen inte är aktiverad på den virtuella datorn eller inte fungerar läser du avsnittet [Reparera den virtuella datorn offline](#repair-the-vm-offline) i den här artikeln.

## <a name="use-the-serial-console"></a>Använda seriekonsolen

1. Anslut till seriekonsolen.

   > [!Note]
   > Mer information om hur du använder seriell konsol för Linux finns i:
   > * [Använd seriell konsol för att komma åt GRUB- och enanvändarläge](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Använda seriella konsol för SysRq- och NMI-samtal](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Välj knappen Ström i-ikonen och välj sedan Starta om den virtuella datorn. (Om seriekonsolen inte är aktiverad eller inte ansluten visas inte knappen.)

   ![BILD](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Starta den virtuella datorn i nödläge.

4. Ange lösenordet för ditt rotkonto för att logga in i nödläge.

5. Använd xfs_repair med alternativet -n för att identifiera felen i filsystemet. I följande exempel förutsätter vi att systempartitionen är /dev/sda1. Ersätt den med rätt värde för den virtuella datorn:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Kör följande kommando för att reparera filsystemet:

   ```
   xfs_repair /dev/sda1
   ```

7. Om felmeddelandet "ERROR: Filesystemet har värdefulla metadataändringar i en logg som måste spelas upp igen" skapar du en tillfällig katalog och monterar filsystemet:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Om disken inte monteras kör du kommandot xfs_repair med alternativet -L (force log zeroing):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Försök sedan att montera filsystemet. Om disken är monterad korrekt får du följande utdata:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Starta om den virtuella datorn och kontrollera sedan om problemet är löst.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. Koppla systemdisken för den virtuella datorn som en datadisk till en återställnings-VM (alla fungerande Linux VM). För att göra detta kan du använda [CLI-kommandon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) eller så kan du automatisera inrättandet av återställnings-VM med hjälp av [vm-reparationskommandona](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Leta reda på enhetsetiketten för den systemdisk som du har bifogat. I det här fallet antar vi att etiketten på systemdisken som du har bifogat är /dev/sdc1. Ersätt den med rätt värde för den virtuella datorn.

3. Använd xfs_repair med alternativet -n för att identifiera felen i filsystemet.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Kör följande kommando för att reparera filsystemet:

   ```
   xfs_repair /dev/sdc1
   ```

5. Om felmeddelandet "ERROR: Filesystemet har värdefulla metadataändringar i en logg som måste spelas upp igen" skapar du en tillfällig katalog och monterar filsystemet:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Om disken inte monteras kör du kommandot xfs_repair med alternativet -L (force log zeroing):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Försök sedan att montera filsystemet. Om disken är monterad korrekt får du följande utdata:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Avmontera och koppla från den ursprungliga virtuella hårddisken och skapa sedan en virtuell dator från den ursprungliga systemdisken. För att göra detta kan du använda [CLI-kommandon](troubleshoot-recovery-disks-linux.md) eller [vm-reparationskommandon](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) om du använde dem för att skapa återställningsdass.

8. Kontrollera om problemet är löst.

## <a name="next-steps"></a>Nästa steg

* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en återställnings-VM med Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Använda portalen för att koppla en datadisk till en Virtuell Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

