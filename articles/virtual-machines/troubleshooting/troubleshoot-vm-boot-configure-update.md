---
title: VM-start har fastnat på "Getting Windows ready. Stäng inte av datorn" i Azure | Microsoft-dokument
description: Introducera stegen för att felsöka problemet där VM-start har fastnat på "Förbereda Windows. Stäng inte av datorn”.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749635"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM-start har fastnat på "Getting Windows ready. Stäng inte av datorn" i Azure

I den här artikeln beskrivs skärmarna "Förbereda" och "Förbereda Windows" som du kan stöta på när du startar en virtuell Dator (VM) i Microsoft Azure. Det innehåller steg som hjälper dig att samla in data för en supportbiljett.

 

## <a name="symptoms"></a>Symtom

En Windows VM startar inte. När du använder **Boot diagnostik** för att få skärmdump av den virtuella datorn, kan du se att den virtuella datorn visar meddelandet "Redo" eller "Förbereda Windows".

![Meddelandeexempel för Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exempel på meddelande](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Orsak

Vanligtvis uppstår det här problemet när servern gör den slutliga omstarten efter att konfigurationen har ändrats. Konfigurationsändringen kan initieras av Windows-uppdateringar eller av ändringarna i serverns roller/funktion. Om storleken på uppdateringarna var stor för Windows Update behöver operativsystemet mer tid för att konfigurera om ändringarna.

## <a name="collect-an-os-memory-dump"></a>Samla in en os-minnesdump

Om problemet inte löser efter att ha väntat på att ändringarna ska bearbetas måste du samla in en minnesdumpfil och kontaktsupport. Så här samlar du in dumpfilen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
3. Fjärrskrivbord till återställningsdass. 
4. Om OS-disken är krypterad måste du stänga av krypteringen innan du går vidare till nästa steg. Mer information finns [i Dekryptera den krypterade OS-disken i den virtuella datorn som inte kan starta](troubleshoot-bitlocker-boot-error.md#solution).

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
4. Starta den virtuella datorn och öppna seriekonsolen.
5. Välj **Skicka icke-maskerbara avbrott(NMI)** för att utlösa minnesdumpen.
    ![bilden om var du ska skicka icke-maskerbart avbrott](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Bifoga OS-disken till en återställnings-VM igen, samla dumpfil.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen kontaktar du [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att analysera grundorsaken.