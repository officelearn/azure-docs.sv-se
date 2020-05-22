---
title: Den virtuella Azure-datorn svarar inte när principen tillämpas
description: Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen fastnar när en princip tillämpas vid start i en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749279"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Den virtuella datorn slutar svara vid tillämpning av principen grupprincip lokala användare & grupper

Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen fastnar när en princip tillämpas vid start i en virtuell Azure-dator.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa en skärm bild av den virtuella datorn, fastnar skärmen i inläsningen med meddelandet: '*tillämpar Grupprincip lokala användare och grupper princip*'.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Skärm bild av hur du tillämpar grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Skärm bild av hur du tillämpar grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012).":::

## <a name="cause"></a>Orsak

Det finns motstridiga Lås när principen försöker rensa gamla användar profiler.

> [!NOTE]
> Detta gäller endast för Windows Server 2012 och Windows Server 2012 R2.

Här är en problematisk princip:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Process översikt

1. [Skapa och få åtkomst till en virtuell reparations dator](#step-1-create-and-access-a-repair-vm)
2. [Inaktivera principen](#step-2-disable-the-policy)
3. [Aktivera serie konsol och samling av minnes dum par](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Återskapa den virtuella datorn](#step-4-rebuild-the-vm)

> [!NOTE]
> Om det här start felet uppstår fungerar inte gäst operativ systemet. Du måste felsöka i offline-läge.

### <a name="step-1-create-and-access-a-repair-vm"></a>Steg 1: skapa och få åtkomst till en reparations-VM

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att förbereda en reparations-VM.
2. Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="step-2-disable-the-policy"></a>Steg 2: inaktivera principen

1. Öppna Registereditorn på den virtuella datorn för reparation.
2. Leta upp nyckeln **HKEY_LOCAL_MACHINE** och välj **fil**  >  **inläsning Hive...** från menyn.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Skärm bild som visar markerade HKEY_LOCAL_MACHINE och den meny som innehåller load Hive.":::

    - Med läsa in Hive kan du läsa in register nycklar från ett offline-system, i det här fallet den skadade disken som är kopplad till den virtuella reparations datorn.
    - Systemomfattande inställningar lagras på `HKEY_LOCAL_MACHINE` och kan förkortas som "HKLM".
3. I den anslutna disken går du till `\windows\system32\config\SOFTWARE` filen och öppnar den.

    1. Du kommer att uppmanas att ange ett namn. Ange BROKENSOFTWARE.<br/>
    2. Kontrol lera att BROKENSOFTWARE har lästs in genom att expandera **HKEY_LOCAL_MACHINE** och leta efter den tillagda BROKENSOFTWARE-nyckeln.
4. Gå till BROKENSOFTWARE och kontrol lera om CleanupProfile-nyckeln finns i den inlästa Hive.

    1. Om nyckeln finns anges CleanupProfile-principen, dess värde representerar bevarande principen i dagar. Fortsätt att ta bort nyckeln.<br/>
    2. Om nyckeln inte finns anges inte CleanupProfile-principen. [Skicka ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), inklusive filen Memory. dmp som finns i Windows-katalogen på den anslutna OS-disken.

5. Ta bort CleanupProfiles-nyckeln med det här kommandot:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Ta bort BROKENSOFTWARE Hive med följande kommando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Steg 3: Aktivera insamling av serie konsol och minnes dum par

Kör det här skriptet om du vill aktivera samling av minnes dum par och seriella konsoler:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör dessa kommandon:

    **Aktivera serie konsol**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Kontrol lera om det lediga utrymmet på OS-disken är minst lika med den virtuella datorns minnes storlek (RAM).

    Om det inte finns tillräckligt med utrymme på OS-disken ändrar du platsen för minnes dumpning och hänvisar till en ansluten datadisk med tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du "% SystemRoot%" med enhets beteckningen (t. ex. "F:") för data disken i kommandona nedan.

    **Föreslagen konfiguration för att aktivera OS-dump**:

    Läs in trasig OS-disk:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Aktivera på ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Aktivera på ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Ta bort skadad OS-disk:
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Steg 4: återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att bygga upp den virtuella datorn igen.

Om problemet är löst har principen inaktiverats lokalt. Använd inte CleanupProfiles-princip på virtuella datorer för en permanent lösning. Använd en annan metod för att utföra profil rensningar.

Använd inte den här principen:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem när du använder Windows Update kan du läsa [den virtuella datorn svarar inte med "C01A001D"-fel när du använder Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).