---
title: Det går inte att fjärransluta till virtuella Azure-datorer eftersom den virtuella datorn startas i felsäkert läge | Microsoft-dokument
description: Lär dig hur du felsöker ett problem där det inte går att rdp till en virtuell dator eftersom den virtuella datorn startar i felsäkert läge.| Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918214"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Det går inte att ta rdp till en virtuell dator eftersom den virtuella datorn startar i felsäkert läge

Den här artikeln visar hur du löser ett problem där du inte kan ansluta till Virtuella Azure Windows-datorer eftersom den virtuella datorn är konfigurerad för att starta i felsäkert läge.


## <a name="symptoms"></a>Symtom

Du kan inte göra en RDP-anslutning eller andra anslutningar (till exempel HTTP) till en virtuell dator i Azure eftersom den virtuella datorn är konfigurerad för att starta i felsäkert läge. När du kontrollerar skärmbilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure-portalen kan du se att den virtuella datorn startas normalt, men nätverksgränssnittet är inte tillgängligt:

![Bild om nätverksavslödning i felsäkert läge](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Orsak

RDP-tjänsten är inte tillgänglig i felsäkert läge. Endast viktiga systemprogram och tjänster laddas när den virtuella datorn startar i felsäkert läge. Detta gäller för de två olika versionerna av felsäkert läge som är "Safe Boot minimal" och "Safe Boot with connectivity".


## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

LÃ¶s problemet genom att anse serial control fÃ¶r att konfigurera den virtuella datorn fÃ¶r att starta i normalt läge eller [reparera den virtuella datorn offline](#repair-the-vm-offline) med hjälp av en återställnings-VM.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [seriekonsolen och öppna CMD-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Om seriekonsolen inte är aktiverad på den virtuella datorn läser du [reparera den virtuella datorn offline](#repair-the-vm-offline).
2. Kontrollera startkonfigurationsdata:

        bcdedit /enum

    Om den virtuella datorn är konfigurerad för att starta i felsäkert läge visas en extra flagga under avsnittet **Windows Boot Loader** som kallas **safeboot**. Om du inte ser **safeboot-flaggan** är den virtuella datorn inte i felsäkert läge. Den här artikeln gäller inte för ditt scenario.

    **Safeboot-flaggan** kan visas med följande värden:
   - Minimal
   - Nätverk

     I något av dessa två lägen startas inte RDP. Därför förblir korrigeringen densamma.

     ![Bild om flaggan Felsäkert läge](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Ta bort **safemoade-flaggan** så att den virtuella datorn startar i normalt läge:

        bcdedit /deletevalue {current} safeboot

4. Kontrollera startkonfigurationsdata för att se till att **safeboot-flaggan** tas bort:

        bcdedit /enum

5. Starta om den virtuella datorn och kontrollera sedan om problemet är löst.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en anslutning till återställningsdatorn till återställningsdatorn.
3. Kontrollera att disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Aktivera dumplogg och seriekonsol (valfritt)

Dumploggen och seriekonsolen hjälper oss att göra ytterligare felsökning om problemet inte kan lösas av lösningen i den här artikeln.

Om du vill aktivera dumplogg och seriekonsol kör du följande skript.

1. Öppna en upphöjd kommandotolkssession (**Kör som administratör**).
2. Kör följande skript:

    I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurera Windows för att starta i normalt läge

1. Öppna en upphöjd kommandotolkssession (**Kör som administratör**).
2. Kontrollera startkonfigurationsdata. I följande kommandon antar vi att enhetsbeteckningen som är tilldelad till den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.

        bcdedit /store F:\boot\bcd /enum
    Anteckna identifierarens namn på den partition som har mappen **\windows.** Som standard är identifierarens namn "Standard".

    Om den virtuella datorn är konfigurerad för att starta i felsäkert läge visas en extra flagga under avsnittet **Windows Boot Loader** som kallas **safeboot**. Om du inte ser **safeboot-flaggan** gäller inte den här artikeln för ditt scenario.

    ![Avbildningen om startidentifierare](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Ta bort **safeboot-flaggan,** så att den virtuella datorn startar i normalt läge:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Kontrollera startkonfigurationsdata för att se till att **safeboot-flaggan** tas bort:

        bcdedit /store F:\boot\bcd /enum
5. [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrollera sedan om problemet är löst.
