---
title: Windows stoppa fel -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373367"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop Error - #0x000000EF "Kritisk process dog"

Den här artikeln innehåller steg för att lösa problem där en kritisk process dör under start i en Virtuell Azure.This article provides steps to resolve issues where a critical process dies during boot in an Azure VM.

## <a name="symptom"></a>Symptom

När du använder [Boot diagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa skärmdumpen av den virtuella datorn, kommer du att se att skärmdumpen visar felet *#0x000000EF* med meddelandet Kritisk *process dog*.

!["Datorn stötte på ett problem och måste starta om. Vi samlar bara in lite felinformation, och sedan kan du starta om. (##% klar) Om du vill veta mer kan du söka på nätet senare efter det här felet: 0x000000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Orsak

Vanligtvis beror detta på att en kritisk systemprocess misslyckas under uppstart. Du kan läsa mer om kritiska processproblem på "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Processöversikt:

1. Skapa och komma åt en virtuell reparations-VM.
2. Åtgärda eventuella operativsystem korruption.
3. **Rekommenderas:** Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling.
4. Återskapa den virtuella datorn.

> [!NOTE]
> När du stöter på det här startfelet fungerar inte gästoperativsystemet. Du kommer att felsöka i offlineläge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och komma åt en virtuell reparations-VM

1. Använd [steg 1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en virtuell reparations-VM.
2. Använda Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

### <a name="fix-any-os-corruption"></a>Åtgärda eventuella skador på operativsystemet

1. Öppna en upphöjd kommandotolk.
2. Kör följande SFC-kommando (System File Checker):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Där < BOOT DISK DRIVE > är startvolymen för reparationsdatorn (vanligtvis "C:") och < BROKEN DISK DRIVE > kommer att vara enhetsbeteckningen för den anslutna disken från den trasiga virtuella datorn. Ersätt större än / mindre än symboler samt texten i dem, t.ex > <.

3. Använd sedan [steg 5 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att sätta ihop den virtuella datorn igen och se om den startar.
4. Om den virtuella datorn fortfarande inte startar fortsätter du att samla in minnesdumpfilen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpfilen

Om problemet kvarstår efter att SFC har körts krävs analys av en minnesdumpfil för att fastställa orsaken till problemet. Så här samlar du in minnesdumpfilen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny reparerad virtuell dator

1. Använd [steg 1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en ny reparations-VM.
2. Använda Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka en supportbiljett

3. På den virtuella reparationsdatorn går du till windowsmappen i den anslutna OS-disken. Om drivrutinsbrevet som är tilldelat till den anslutna OS-disken är *F*måste du gå till *F:\Windows*.
4. Leta reda på *filen memory.dmp* och skicka sedan [en supportbiljett](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpfilen.

   > [!NOTE]
   > Om du inte hittar dumpfilen slutför du stegen nedan för att aktivera insamling av minnesdump och seriekonsol, gå sedan tillbaka till det här avsnittet och upprepa stegen i uppgiften ovan för att samla in minnesdumpfilen.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling

Om du vill aktivera insamling av minnesdump och Seriekonsol kör du följande skript:

1. Öppna en upphöjd kommandotolkssession (Kör som administratör).
2. Kör följande kommandon:

   Aktivera seriekonsol

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Ersätt större än eller mindre än symboler samt texten i dem, t.> < ex.

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
