---
title: Det går inte att fjärransluta till Azure Virtual Machines eftersom den virtuella datorn startas i felsäkert läge | Microsoft Docs
description: Lär dig att felsöka ett problem där kan du inte använda RDP till en virtuell dator eftersom den virtuella datorn startas i felsäkert läge. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 097b7efd7643e3b8450284d19e13a428dfd48ac2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138865"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Kan inte använda RDP till en virtuell dator eftersom den virtuella datorn startas i felsäkert läge

Den här artikeln visar hur du löser problem som du inte kan ansluta till Azure Windows Virtual Machines (VM) eftersom den virtuella datorn är konfigurerad att starta i felsäkert läge.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptoms"></a>Symtom

Du kan inte göra en RDP-anslutning eller andra anslutningar (till exempel HTTP) till en virtuell dator i Azure eftersom den virtuella datorn är konfigurerad att starta i felsäkert läge. När du checkar skärmbilden den [Startdiagnostik](../troubleshooting/boot-diagnostics.md) i Azure-portalen kan du se att den virtuella datorn startas normalt, men nätverksgränssnittet är inte tillgänglig:

![Bild som visar nätverk inferce i felsäkert läge](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Orsak

RDP-tjänsten är inte tillgänglig i felsäkert läge. Endast läses viktiga program och tjänster i in när den virtuella datorn startas i felsäkert läge. Detta gäller för två olika versioner av felsäkert läge som är ”säker Start minimal” och ”Safe starta med anslutning”.


## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att konfigurera den virtuella datorn att starta i normalläge eller [reparera den virtuella datorn offline](#repair-the-vm-offline) med hjälp av en virtuell dator för återställning.

### <a name="use-serial-control"></a>Använda seriell kontroll

1. Ansluta till [seriella konsolen och öppna CMD instans](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om Seriekonsolen inte är aktiverad på den virtuella datorn finns i [reparera den virtuella datorn offline](#repair-the-vm-offline).
2. Kontrollera boot configuration data:

        bcdedit /enum

    Om den virtuella datorn är konfigurerad för att starta i felsäkert läge, ser du en extra flagga under den **Windows Boot Loader** avsnitt som heter **safeboot**. Om du inte ser den **safeboot** flagga den virtuella datorn är inte i felsäkert läge. Den här artikeln gäller inte för ditt scenario.

    Den **safeboot** flaggan kan visas med följande värden:
    - Minimalt
    - Nätverk

    I dessa två lägena kan startas inte RDP. Därför förblir korrigeringen densamma.

    ![Bild som visar flaggan felsäkert läge](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Ta bort den **safemoade** flaggan, så att den virtuella datorn startar i normalt läge:

        bcdedit /deletevalue {current} safeboot

4. Kontrollera boot configuration data för att se till att den **safeboot** flaggan tas bort:

        bcdedit /enum

5. Starta om den virtuella datorn och sedan kontrollera om problemet är löst.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Aktivera dump logg- och Seriekonsol (valfritt)

Dump logg- och Seriekonsolen hjälper oss att göra mer felsökningsinformation om problemet inte kan matchas av lösningen i den här artikeln.

Kör följande skript för att aktivera dump logg- och Seriekonsol.

1. Öppna en upphöjd kommandotolk-session (**kör som administratör**).
2. Kör följande skript:

    I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

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

#### Configure the Windows to boot into normal mode

1. Open an elevated command prompt session (**Run as administrator**).
2. Check the boot configuration data. In the following commands, we assume that the drive letter that is assigned to the attached OS disk is F. Replace this drive letter with the appropriate value for your VM.

        bcdedit /store F:\boot\bcd /enum
    Take note of the Identifier name of the partition that has the **\windows** folder. By default, the  Identifier name is "Default".

    If the VM is configured to boot into Safe Mode, you will see an extra flag under the **Windows Boot Loader** section called **safeboot**. If you do not see the **safeboot** flag, this article does not apply to your scenario.

    ![The image about boot Identifier](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remove the **safeboot** flag, so the VM will boot into normal mode:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Check the boot configuration data to make sure that the **safeboot** flag is removed:

        bcdedit /store F:\boot\bcd /enum
5. [Detach the OS disk and recreate the VM](../windows/troubleshoot-recovery-disks-portal.md). Then check whether the issue is resolved.
