---
title: Blå skärm fel när du startar en virtuell Azure-dator | Microsoft Docs
description: Lär dig att felsöka problemet som blå skärm felmeddelandet när du startar | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 00/19/2018
ms.author: genli
ms.openlocfilehash: 6525067ec7a7a3154d1439f15736a1038ab0408c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168056"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows visar blå skärm fel när du startar en virtuell Azure-dator
Den här artikeln beskriver blå skärm fel som kan uppstå när du startar en Windows virtuell dator (VM) i Microsoft Azure. Den innehåller steg som hjälper dig att samla in data för ett supportärende. 

> [!NOTE] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptom"></a>Symtom 

En virtuell Windows-dator startar inte. När du checkar Start skärmbilderna [Startdiagnostik](./boot-diagnostics.md), du ser något av följande felmeddelanden i en blå skärm:

- vår datorn råkade ut för ett problem och måste startas om. Vi samlar in bara viss felinformation och starta sedan.
- Datorn råkade ut för ett problem och måste startas om.

Det här avsnittet innehåller vanliga felmeddelanden som kan uppstå när du hanterar virtuella datorer:

## <a name="cause"></a>Orsak

Det kan finnas flera orsaker som varför skulle du få ett stoppfel. De vanligaste orsakerna är:

- Problem med en drivrutin
- Skadad systemfil eller minne
- Ett program som ansluter till en otillåtna sektor av minne

## <a name="solution"></a>Lösning

För att lösa problemet behöver du först med att samla in dumpfilen för kraschen och kontaktar du supporten med dumpfilen. Följ dessa steg för att samla in dumpfilen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. Ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](snapshot-copy-managed-disk.md).
2. [Koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-portal.md). 
3. Fjärrskrivbord till den Virtuella återställningsdatorn.

### <a name="collect-dump-file-and-submit-a-support-ticket"></a>Samla in dumpfilen och skicka in ett supportärende

1. På den Virtuella återställningsdatorn, gå till windows-mappen i den anslutna OS-disken. Om den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F, måste du gå till F:\Windows.
2. Leta upp th memory.dmp-fil, och sedan [skicka in ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen. 

Om du inte hittar dumpfilen, flytta nästa steg för att aktivera dump logg- och Seriekonsol.

### <a name="enable-dump-log-and-serial-console"></a>Aktivera dump logg- och Seriekonsol

Kör följande skript för att aktivera dump logg- och Seriekonsol.

1. Öppna en upphöjd kommandotolk-session (Kör som administratör).
2. Kör följande skript:

    I det här skriptet kan anta vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F.  Ersätt den med lämpligt värde i den virtuella datorn.

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

    1. Se till att det finns tillräckligt med utrymme på disken för att allokera så mycket minne som RAM-minne, som är beroende av den storlek som du väljer för den här virtuella datorn.
    2. Om det finns inte tillräckligt med utrymme eller om det här är en stor virtuell dator (G, GS eller E-serien), kan du ändra den plats där den här filen kommer att skapas och som avser andra datadisk som är ansluten till den virtuella datorn. Om du vill göra detta måste du ändra följande nyckel:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Koppla från den OS-disken och sedan koppla OS-disken till den berörda virtuella datorn igen](troubleshoot-recovery-disks-portal.md).
4. Starta den virtuella datorn för att återskapa problemet och sedan den här filen kommer att genereras.
5. Koppla OS-disken till en virtuell dator, samla in dumpfilen för återställning och sedan [skicka in ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen.



