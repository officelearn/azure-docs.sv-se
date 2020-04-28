---
title: Starten av den virtuella datorn har fastnat i "komma igång med Windows. Stäng inte av datorn i Azure | Microsoft Docs
description: Introducera stegen för att felsöka problemet där VM-start fastnar på "komma igång med Windows. Stäng inte av datorn”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73749635"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Starten av den virtuella datorn har fastnat i "komma igång med Windows. Stäng inte av datorn i Azure

Den här artikeln beskriver skärmarna "komma igång" och "komma igång med Windows" som du kan stöta på när du startar en virtuell Windows-dator (VM) i Microsoft Azure. Den innehåller steg som hjälper dig att samla in data för ett support ärende.

 

## <a name="symptoms"></a>Symtom

En virtuell Windows-dator startar inte. När du använder **startdiagnostik** för att hämta skärm bilden av den virtuella datorn kan du se att den virtuella datorn visar meddelandet "förbereder redo" eller "komma igång med Windows".

![Meddelande exempel för Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Meddelande exempel](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Orsak

Det här problemet uppstår vanligt vis när servern utför den slutliga omstarten när konfigurationen har ändrats. Konfigurations ändringen kan initieras av Windows-uppdateringar eller av ändringarna på serverns roller/funktion. För Windows Update, om Uppdateringens storlek var stor, behöver operativ systemet mer tid för att omkonfigurera ändringarna.

## <a name="collect-an-os-memory-dump"></a>Samla in en minnesdump för operativ system

Om problemet inte löses när du väntar på att ändringarna ska bearbetas måste du samla in en minnesdumpfil och kontakta supporten. Samla in dumpfilen genom att följa dessa steg:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en virtuell dator för återställning](../windows/troubleshoot-recovery-disks-portal.md).
3. Fjärr skrivbord till den virtuella återställnings datorn. 
4. Om operativ system disken är krypterad måste du stänga av krypteringen innan du går vidare till nästa steg. Mer information finns i [dekryptera den krypterade OS-disken på den virtuella datorn som inte kan starta](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Hitta en dumpfil och skicka ett support ärende

1. På den virtuella datorn för återställning går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som har tilldelats till den anslutna OS-disken är F, måste du gå till F:\Windows.
2. Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen. 

Om du inte hittar dumpfilen går du vidare till nästa steg för att aktivera dumpa logg och seriell konsol.

### <a name="enable-dump-log-and-serial-console"></a>Aktivera dumpa logg och seriell konsol

Kör följande skript för att aktivera dumpa logg och seriell konsol.

1. Öppna en upphöjd kommando tolk session (kör som administratör).
2. Kör följande skript:

    I det här skriptet antar vi att enhets beteckningen som är kopplad till den anslutna OS-disken är F.  Ersätt det med lämpligt värde i den virtuella datorn.

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

    1. Kontrol lera att det finns tillräckligt med utrymme på disken för att allokera så mycket minne som RAM-minnet, vilket beror på den storlek som du väljer för den här virtuella datorn.
    2. Om det inte finns tillräckligt med utrymme eller om det här är en stor virtuell dator (G, GS eller E-serien) kan du sedan ändra den plats där filen kommer att skapas och se om det finns någon annan datadisk som är kopplad till den virtuella datorn. För att göra detta måste du ändra följande nyckel:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Koppla från OS-disken och återanslut sedan OS-disken till den berörda virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md).
4. Starta den virtuella datorn och få åtkomst till serie konsolen.
5. Välj **skicka icke-Maskbart avbrott (NMI)** för att utlösa minnesdumpen.
    ![bilden om var det inte går att maskera avbrott](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Koppla OS-disken till en återställnings-VM igen och samla in dump-filen.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen kontaktar du [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att analysera rotor saken.