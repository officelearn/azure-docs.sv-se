---
title: Startfel – "detta är inte en startbar disk"
description: Den här artikeln innehåller steg för att lösa problem där disken inte kan startas i en virtuell Azure-dator
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300984"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Startfel – Det här är inte en startbar disk

Den här artikeln innehåller steg för att lösa problem där disken inte kan startas i en virtuell virtuell azure-dator (VM).

## <a name="symptoms"></a>Symtom

När du använder [Boot diagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa skärmdumpen av den virtuella datorn, ser du att skärmdumpen visar en fråga med meddelandet "Detta är inte en startbar disk. Sätt i en startbar diskett och tryck på valfri tangent för att försöka igen...'.

   Bild 1

   ![Bild 1 visar meddelandet *"Detta är inte en startbar disk. Sätt i en startbar diskett och tryck på valfri tangent för att försöka igen..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Orsak

Det här felmeddelandet innebär att operativsystemets startprocess inte kunde hitta en aktiv systempartition. Det här felet kan också innebära att det saknas en referens i arkivet boot configuration data (BCD), vilket hindrar den från att hitta Windows-partitionen.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Översikt över processer

1. Skapa och komma åt en virtuell reparations-VM.
2. Ange partitionsstatus till Aktiv.
3. Åtgärda diskpartitionen.
4. **Rekommenderas:** Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling.
5. Återskapa den ursprungliga virtuella datorn.

   > [!NOTE]
   > När du stöter på det här startfelet fungerar inte gästoperativsystemet. Du kommer att felsöka i offlineläge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och komma åt en virtuell reparations-VM

1. Använd steg 1-3 i [VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en virtuell reparations-VM.
2. Använda Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

### <a name="set-partition-status-to-active"></a>Ange partitionsstatus till aktiv

Virtuella datorer med generation 1 bör först kontrollera att OS-partitionen, som innehåller BCD-arkivet, är markerad som *aktiv*. Om du har en virtuell generation 2 går du vidare till [Fix diskpartitionen](#fix-the-disk-partition), eftersom *statusflaggan* var inaktuell i den senare generationen.

1. Öppna en upphöjd kommandotolk *(cmd.exe).*
2. Ange *diskdel* för att starta DISKPART-verktyget.
3. Ange *listdisken* för att lista diskarna på systemet och identifiera den anslutna virtuella datorn.
4. När den anslutna virtuella os-hårddisken finns anger du *sel disk #* för att välja disken.  Se bild 2, där disk 1 är den anslutna VIRTUELLA OS.Se Bild 2, där disk 1 är den anslutna virtuella os-hårddisken.

   Bild 2

   ![Bild 2 visar *DISKPART*-fönstret som visar utdata från listdiskkommandot, Disk 0 och Disk 1 som visas i tabellen.  Visar också utdata för sel-disk 1-kommandot, Disk 1 är den valda disken](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. När disken har valts anger du *listpartitionen* för att visa partitionerna för den valda disken.
6. När startpartitionen har identifierats anger du *selpartitionen #* för att välja partitionen.  Vanligtvis boot partition kommer att vara runt 350 MB i storlek.  Se bild 3, där Partition 1 är startpartitionen.

   Bild 3

   ![Bild 3 visar *DISKPART*-fönstret med utdata från kommandot *list partition*. Partition 1 och Partition 2 visas i tabellen. Den visar också utdata från kommandot *sel partition 1* när partition 1 är den valda disken.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Ange "detaljpartition" för att kontrollera partitionens status. Se bild 4, där partitionen är *Aktiv: Nej*, eller figur 5, där partitionen är "Aktiv: Ja".

   Bild 4

   ![Bild 4 visar *DISKPART*-fönstret med utdata från kommandot *detaljpartition*, när partition 1 är inställd på *Aktiv: Nej*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Bild 5

   ![Bild 5 visar *DISKPART*-fönstret med utdata från kommandot *detaljpartition*, när partition 1 är inställd på *Aktiv: Ja*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Om partitionen inte är **aktiv**anger du *aktiv* för att ändra flaggan *Aktiv.*
9. Kontrollera att statusändringen gjordes korrekt genom att skriva *detaljpartition*.

   Bild 6

   ![Bild 6 visar diskdelfönstret med utdata från kommandot *detaljpartition*, när partition 1 är inställd på *Aktiv: Ja*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Gå *in för* att stänga DISKPART-verktyget och spara konfigurationsändringarna.

### <a name="fix-the-disk-partition"></a>Åtgärda diskpartitionen

1. Öppna en upphöjd kommandotolk (cmd.exe).
2. Använd följande kommando för att köra *CHKDSK* på diskarna och åtgärda fel:

   `chkdsk <DRIVE LETTER>: /f`

   Om du lägger till kommandoalternativet /f åtgärdar du eventuella fel på disken. Se till <DRIVE LETTER> att ersätta med bokstaven i den bifogade OS VHD.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling

Om du vill aktivera insamling av minnesdump och Seriekonsol kör du följande skript:

1. Öppna en upphöjd kommandotolkssession (Kör som administratör).
2. Kör följande kommandon:

   Aktivera seriekonsol

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Kontrollera att det lediga utrymmet på OS-disken är lika mycket som minnesstorleken (RAM) på den virtuella datorn.

   Om det inte finns tillräckligt med utrymme på OS-disken bör du ändra platsen där minnesdumpfilen skapas och hänvisa den till alla datadiskar som är anslutna till den virtuella datorn och som har tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du %SystemRoot%" med enhetsbeteckningen (till exempel "F:") för datadisken i kommandona nedan.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Föreslagen konfiguration för att aktivera OS-dump

**Ladda broken OS Disk:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Aktivera på ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Aktivera på ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Ta bort trasig OS-disk:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Återskapa den ursprungliga virtuella datorn

Använd [steg 5 i vm-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att sätta ihop den virtuella datorn igen.
