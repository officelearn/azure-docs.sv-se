---
title: Felsöka virtuella Linux-datorer med start problem på grund av fel i fil systemet | Microsoft Docs
description: Lär dig hur du felsöker problem med att starta virtuella Linux-datorer på grund av fil system fel
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
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325954"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Felsöka virtuella Linux-datorer med start problem på grund av fel i fil systemet

Det går inte att ansluta till en virtuell Azure Linux-dator (VM) med hjälp av SSH (Secure Shell). När du kör funktionen för startdiagnostik på [Azure Portal](https://portal.azure.com/)visas logg poster som liknar följande exempel.

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

Det här exemplet orsakas av en ren fsck. I det här fallet finns det också ytterligare data diskar som är anslutna till den virtuella datorn (/dev/sdc1 och/dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Det här problemet kan inträffa om fil systemet inte har stängts av på ett säkert sätt eller om det är problem med lagringen. Problemen omfattar maskinvaru-eller program varu fel, problem med driv rutiner eller program, skriv fel osv. Det är alltid viktigt att ha en säkerhets kopia av kritiska data. De verktyg som beskrivs i den här artikeln kan hjälpa till att återställa fil system, men det kan fortfarande uppstå data förlust.

Linux har flera kontroll program för fil system tillgängliga. De vanligaste för distributionerna i Azure är: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)och [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Lösning

Lös problemet genom att starta den virtuella datorn i nödfalls läge med hjälp av [serie konsolen](./serial-console-linux.md) och använda verktyget för att reparera fil systemet. Om serie konsolen inte är aktive rad på den virtuella datorn eller inte fungerar, kan du läsa avsnittet [reparera den virtuella datorn offline](#repair-the-vm-offline) i den här artikeln.

## <a name="use-the-serial-console"></a>Använda seriekonsolen

1. Anslut till serie konsolen.

   > [!Note]
   > Mer information om hur du använder en serie konsol för Linux finns i:
   > * [Använd serie konsolen för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md)
   > * [Använda en serie konsol för SysRq-och NMI-anrop](./serial-console-nmi-sysrq.md)

2. Välj ikonen för strömbrytaren och välj sedan starta om virtuell dator. (Om serie konsolen inte är aktive rad eller inte ansluten visas inte knappen.)

   ![BILD](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Starta den virtuella datorn i nödfalls läge.

4. Ange lösen ordet för rot kontot för att logga in i nödfalls läge.

5. Använd xfs_repair med alternativet-n för att identifiera felen i fil systemet. I följande exempel förutsätter vi att systempartitionen är/dev/sda1. Ersätt det med lämpligt värde för din virtuella dator:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Reparera fil systemet genom att köra följande kommando:

   ```
   xfs_repair /dev/sda1
   ```

7. Om du får fel meddelandet "fel: fil systemet har värdefulla metadata ändringar i en logg som måste spelas upp", skapa en tillfällig katalog och montera fil systemet:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Om disken inte kan monteras kör du kommandot xfs_repair med alternativet-L (tvinga logg nollor):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Försök sedan att montera fil systemet. Om disken har monterats visas följande utdata:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Starta om den virtuella datorn och kontrol lera sedan om problemet är löst.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. Anslut den virtuella datorns system disk till en datadisk till en virtuell dator för återställning (valfri fungerande Linux-dator). Om du vill göra detta kan du använda [CLI-kommandon](./troubleshoot-recovery-disks-linux.md) eller automatisera konfigurationen av den virtuella återställnings datorn med hjälp av [kommandona för reparation av virtuella datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Leta upp enhets etiketten för system disken som du har anslutit. I det här fallet förutsätter vi att etiketten på system disken som du har anslutit är/dev/sdc1. Ersätt det med lämpligt värde för den virtuella datorn.

3. Använd xfs_repair med alternativet-n för att identifiera felen i fil systemet.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Reparera fil systemet genom att köra följande kommando:

   ```
   xfs_repair /dev/sdc1
   ```

5. Om du får fel meddelandet "fel: fil systemet har värdefulla metadata ändringar i en logg som måste spelas upp", skapa en tillfällig katalog och montera fil systemet:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Om disken inte kan monteras kör du kommandot xfs_repair med alternativet-L (tvinga logg nollor):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Försök sedan att montera fil systemet. Om disken har monterats visas följande utdata:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Demontera och koppla från den ursprungliga virtuella hård disken och skapa sedan en virtuell dator från den ursprungliga system disken. Om du vill göra detta kan du använda [CLI-kommandon](troubleshoot-recovery-disks-linux.md) eller [reparations kommandon för virtuella datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) om du använde dem för att skapa den virtuella återställnings datorn.

8. Kontrol lera om problemet är löst.

## <a name="next-steps"></a>Nästa steg

* [Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell dator för återställning med Azure CLI 2,0](./troubleshoot-recovery-disks-linux.md)
* [Använd portalen för att koppla en datadisk till en virtuell Linux-dator](../linux/attach-disk-portal.md)
