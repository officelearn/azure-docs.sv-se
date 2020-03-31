---
title: Blåskärmsfel vid start av en virtuell Azure-dator| Microsoft-dokument
description: Läs om felsÃ¶kning av problemet att det blå skärmfelet tas emot vid uppstart| Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266941"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows visar blåskärmsfel vid start av en Virtuell Azure-dator
I den här artikeln beskrivs blåskärmsfel som kan uppstå när du startar en virtuell dator (Virtuell Dator i Windows) i Microsoft Azure. Det innehåller steg som hjälper dig att samla in data för en supportbiljett. 


## <a name="symptom"></a>Symptom 

En Virtuell Windows-dator startar inte. NÃ¤5 pÃ¤ ã¤nder startskärmarna i [Boot diagnostics](./boot-diagnostics.md)visas nÃ¤ringar av fÃ¶ringar av fÃ¶1ringar i en blå skärm:

- vår dator stötte på ett problem och måste starta om. Vi samlar bara in lite felinformation, och sedan kan du starta om.
- Datorn stötte på ett problem och måste starta om.

I det här avsnittet visas de vanliga felmeddelanden som kan uppstå när du hanterar virtuella datorer:

## <a name="cause"></a>Orsak

Det kan finnas flera skäl till varför du skulle få ett stoppfel. De vanligaste orsakerna är:

- Problem med en drivrutin
- Skadad systemfil eller minne
- Ett program kommer åt en förbjuden sektor av minnet

## <a name="collect-memory-dump-file"></a>Samla in minnesdumpfil

För att lösa problemet måste du först samla in dumpfil för krasch- och kontaktsupporten med dumpfilen. Så här samlar du in dumpfilen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md). 
3. Fjärrskrivbord till återställningsdass.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Hitta dumpfil och skicka en supportbiljett

1. På återställningsdatorn går du till windowsmappen i den anslutna OS-disken. Om drivrutinsbrevet som är tilldelat till den anslutna OS-disken är F måste du gå till F:\Windows.
2. Leta reda på filen memory.dmp och skicka sedan [en supportbiljett](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen. 

Om du inte hittar dumpfilen flyttar du nästa steg för att aktivera dumplogg och seriekonsol.

### <a name="enable-dump-log-and-serial-console"></a>Aktivera dumplogg och seriekonsol

Om du vill aktivera dumplogg och seriekonsol kör du följande skript.

1. Öppna förhöjd kommandotolkssession (Kör som administratör).
2. Kör följande skript:

    I det här skriptet antar vi att enhetsbeteckningen som är tilldelad den bifogade OS-disken är F.  Ersätt den med rätt värde i den virtuella datorn.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Se till att det finns tillräckligt med utrymme på disken för att allokera lika mycket minne som RAM-minnet, vilket beror på storleken som du väljer för den här virtuella datorn.
    2. Om det inte finns tillräckligt med utrymme eller om det är en virtuell dator med stor storlek (G, GS eller E-serien) kan du sedan ändra platsen där filen skapas och hänvisa den till alla andra datadiskar som är kopplade till den virtuella datorn. För att göra detta måste du ändra följande nyckel:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Koppla från OS-disken och anslut sedan OS-disken igen till den berörda virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md).
4. Starta den virtuella datorn för att återskapa problemet, sedan en dumpfil kommer att genereras.
5. Bifoga OS-disken till en återställnings-VM, samla in dumpfil och skicka sedan [en supportbiljett](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen.



