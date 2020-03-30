---
title: Windows virtuella dator kan inte starta på grund av Windows Boot Manager
description: Den här artikeln innehåller steg för att lösa problem där Windows Boot Manager förhindrar att en virtuell Azure-dator startas.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373354"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM kan inte startas på grund av Windows Boot Manager

Den här artikeln innehåller steg för att lösa problem där Windows Boot Manager förhindrar att en virtuell virtuell dator (Virtuell Dator) startas.

## <a name="symptom"></a>Symptom

Den virtuella datorn har fastnat väntar på en användare fråga och startar inte om inte manuellt instrueras till.

NÃ¤r du [ansÃ¤nder startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) fÃ¶r att visa skärmbilden av den virtuella datorn visas att skärmbilden visar Windows Boot Manager med meddelandet *Välj ett operativsystem som ska startas eller fÃ¶r att klicka pÃ¶ r TAB fÃ¶r att välja ett verktyg:*.

Bild 1
 
![Windows Boot Manager med texten "Välj ett operativsystem att starta eller tryck på TABB för att välja ett verktyg:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Orsak

Felet beror på en BCD-flagga *displaybootmenu* i Windows Boot Manager. När flaggan är aktiverad uppmanas användaren att under uppstartsprocessen välja vilken lastare de vill köra, vilket orsakar en startfördröjning. I Azure kan den här funktionen lägga till den tid det tar att starta en virtuell dator.

## <a name="solution"></a>Lösning

Processöversikt:

1. Konfigurera för snabbare starttid med seriekonsolen.
2. Skapa och komma åt en virtuell reparations-VM.
3. Konfigurera för snabbare starttid på en reparerad virtuell dator.
4. **Rekommenderas:** Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling.
5. Återskapa den virtuella datorn.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurera för snabbare starttid med seriekonsol

Om du har tillgång till seriell konsol finns det två sätt att uppnå snabbare starttider. Antingen minska *displaybootmenu* väntetid, eller ta bort flaggan helt och hållet.

1. Följ anvisningarna för att komma åt [Azure Serial Console för Windows för](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) att få tillgång till den textbaserade konsolen.

   > [!NOTE]
   > Om du inte kan komma åt seriekonsolen går du vidare till [Skapa och få tillgång till en virtuell reparations-VM](#create-and-access-a-repair-vm).

2. **Alternativ A:** Minska väntetiden

   a. Väntetiden är inställd på 30 sekunder som standard men kan ändras till en snabbare tid (t.ex. 5 sekunder).

   b. Använd följande kommando i seriekonsolen för att justera timeout-värdet:

      `bcdedit /set {bootmgr} timeout 5`

3. **Alternativ B**: Ta bort BCD-flaggan

   a. Om du vill förhindra att frågan om startmenyn för bildskärm helt och hållet anger du följande kommando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Om du inte kunde använda seriekonsolen för att konfigurera en snabbare starttid i stegen ovan kan du i stället fortsätta med följande steg. Du kommer nu att felsöka i offlineläge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och komma åt en virtuell reparations-VM

1. Använd [steg 1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en virtuell reparations-VM.
2. Använd Anslutning till fjärrskrivbordsanslutning till den virtuella reparationsdatorn.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurera för snabbare starttid på en reparationsdyst

1. Öppna en upphöjd kommandotolk.
2. Ange följande för att aktivera DisplayBootMenu:

   Använd det här kommandot för **virtuella generation 1-datorer:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Använd det här kommandot för **virtuella generation 2-datorer:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Ersätt större än eller mindre än symboler samt texten i dem, t.> < ex.

3. Ändra timeout-värdet till 5 sekunder:

   Använd det här kommandot för **virtuella generation 1-datorer:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Använd det här kommandot för **virtuella generation 2-datorer:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Ersätt större än eller mindre än symboler samt texten i dem, t.> < ex.

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