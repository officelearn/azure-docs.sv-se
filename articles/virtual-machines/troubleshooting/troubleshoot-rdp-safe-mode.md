---
title: Det går inte att ansluta till Azure Virtual Machines via fjärr anslutning eftersom den virtuella datorn startar i fel säkert läge | Microsoft Docs
description: Lär dig hur du felsöker ett problem där det inte går att RDP till en virtuell dator eftersom den virtuella datorn startar i fel säkert läge. | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918214"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Det går inte att RDP till en virtuell dator eftersom den virtuella datorn startar i fel säkert läge

Den här artikeln visar hur du löser ett problem där du inte kan ansluta till Azure Virtuella Windows-datorer (VM) eftersom den virtuella datorn är konfigurerad för att starta i fel säkert läge.


## <a name="symptoms"></a>Symtom

Du kan inte göra en RDP-anslutning eller andra anslutningar (till exempel HTTP) till en virtuell dator i Azure eftersom den virtuella datorn är konfigurerad för att starta i fel säkert läge. När du kontrollerar skärm bilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure Portal kan du se att den virtuella datorn startar normalt, men att nätverks gränssnittet inte är tillgängligt:

![Bild om nätverks inferce i fel säkert läge](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Orsak

RDP-tjänsten är inte tillgänglig i fel säkert läge. Endast viktiga system program och-tjänster läses in när den virtuella datorn startas i fel säkert läge. Detta gäller för de två olika versionerna av fel säkert läge som är "säker start minimal" och "säker start med anslutning".


## <a name="solution"></a>Lösning

Innan du följer de här stegen ska du ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att konfigurera den virtuella datorn för att starta i normalt läge eller [reparera den virtuella datorn offline](#repair-the-vm-offline) med hjälp av en virtuell återställnings dator.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [serie konsolen och öppna cmd-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Om serie konsolen inte är aktive rad på den virtuella datorn, se [reparera den virtuella datorn offline](#repair-the-vm-offline).
2. Kontrol lera start konfigurations data:

        bcdedit /enum

    Om den virtuella datorn har kon figurer ATS för att starta i fel säkert läge visas en extra flagga under **Windows Boot Loader** -avsnittet som heter **safeboot**. Om du inte ser flaggan **safeboot** är den virtuella datorn inte i fel säkert läge. Den här artikeln gäller inte för ditt scenario.

    **Safeboot** -flaggan kan visas med följande värden:
   - Minimal
   - Nätverk

     I något av dessa två lägen kommer RDP inte att starta. Därför förblir korrigeringen densamma.

     ![Bild av flaggan för fel säkert läge](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Ta bort flaggan **safemoade** så att den virtuella datorn startar i normalt läge:

        bcdedit /deletevalue {current} safeboot

4. Kontrol lera start konfigurations data för att se till att flaggan **safeboot** har tagits bort:

        bcdedit /enum

5. Starta om den virtuella datorn och kontrol lera om problemet är löst.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disken till en virtuell dator för återställning](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn.
3. Kontrol lera att disken är flaggad som **online** i disk hanterings konsolen. Anteckna enhets beteckningen som tilldelas till den anslutna OS-disken.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Aktivera dumpnings logg och serie konsol (valfritt)

Med dump-loggen och serie konsolen kan vi göra ytterligare fel sökning om problemet inte kan lösas av lösningen i den här artikeln.

Kör följande skript för att aktivera dumpa logg och seriell konsol.

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kör följande skript:

    I det här skriptet antar vi att den enhets beteckning som är kopplad till den anslutna OS-disken är F. Ersätt enhets beteckningen med lämpligt värde för den virtuella datorn.

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

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kontrol lera start konfigurations data. I följande kommandon antar vi att den enhets beteckning som är kopplad till den anslutna OS-disken är F. Ersätt enhets beteckningen med lämpligt värde för den virtuella datorn.

        bcdedit /store F:\boot\bcd /enum
    Anteckna ID-namnet för den partition som innehåller mappen **\Windows** . Som standard är Identifierarens namn "default".

    Om den virtuella datorn har kon figurer ATS för att starta i fel säkert läge visas en extra flagga under **Windows Boot Loader** -avsnittet som heter **safeboot**. Om du inte ser flaggan **safeboot** gäller inte den här artikeln för ditt scenario.

    ![Avbildningen om start-ID](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Ta bort flaggan **safeboot** så att den virtuella datorn startar i normalt läge:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Kontrol lera start konfigurations data för att se till att flaggan **safeboot** har tagits bort:

        bcdedit /store F:\boot\bcd /enum
5. [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrol lera sedan om problemet är löst.
