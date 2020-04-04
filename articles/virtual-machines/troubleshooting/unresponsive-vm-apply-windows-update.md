---
title: Azure VM svarar inte med C01A001D-fel vid tillämpning av Windows Update
description: Den här artikeln innehåller steg för att lösa problem där Windows Update genererar ett fel och inte svarar i en Virtuell Azure.This article provides steps to resolve issues where Windows update generates an error and becomes unresponsive in an Azure VM.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633962"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Den virtuella datorn svarar inte med felet "C01A001D" vid tillämpning av Windows Update

Den här artikeln innehåller steg för att lösa problem där Windows Update (KB) genererar ett fel och inte svarar i en Virtuell Azure.This article provides steps to resolve issues where Windows Update (KB) generates an error and becomes unresponsive in an Azure VM.

## <a name="symptoms"></a>Symtom

När du använder [Startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa skärmbilden av den virtuella datorn visas den pågående Windows Update (KB), men misslyckas med felkod: "C01A001D".

![svarar inte med Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Orsak

Det går inte att skapa en kärnfil i filsystemet. Det går inte att skriva filer till disken.

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Översikt över processer

1. [Skapa och komma åt en virtuell reparations-VM](#create-and-access-a-repair-vm).
2. [Frigör utrymme på hårddisken](#free-up-space-on-the-hard-disk).
3. [Rekommenderas: Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Återskapa den virtuella datorn](#rebuild-the-vm).

> [!NOTE]
> När det här felet inträffar fungerar inte gästoperativsystemet. Du måste felsöka i offlineläge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och komma åt en virtuell reparations-VM

1. Följ [steg 1-3 i VM-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) för att förbereda en virtuell reparations-VM.
2. Anslut till den virtuella reparationsdatorn med fjärrskrivbordsanslutning.

### <a name="free-up-space-on-the-hard-disk"></a>Frigör utrymme på hårddisken

Om disken inte redan är 1 Tb måste du ändra storlek på den. När disken är 1 TB utför du en diskrensning och en defragmentering av enheten.

1. Kontrollera om disken är full. Om disken är under 1 Tb [expanderar du den till maximalt 1 tb med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. När disken är 1 Tb utför du en diskrensning.
    - [Koppla bort datadisken från den trasiga virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Koppla datadisken till en fungerande virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Använd [diskrensningsverktyget](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) för att frigöra utrymme.
3. Efter storleksändring och rensning, defragmentera enheten:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Beroende på graden av fragmentering kan detta ta timmar.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: Innan du återskapar den virtuella datorn aktiverar du seriell konsol och minnesdumpsamling

1. Öppna en upphöjd kommandotolkssession (Kör som administratör).
2. Kör följande kommandon:

    Aktivera seriekonsol:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Kontrollera att det lediga utrymmet på OS-disken är minst lika med RAM-minnets storlek .Se till att det lediga utrymmet på OS-disken är minst lika med storleken på VM-minnet (RAM).

    Om det inte finns tillräckligt med utrymme på OS-disken ändrar du platsen där minnesdumpfilen skapas och hänvisar den till en datadisk som är ansluten till den virtuella datorn och med tillräckligt med ledigt utrymme. Om du vill `%SystemRoot%` ändra platsen ersätter du med enhetsbeteckningen (till exempel "F:") på datadisken i kommandona nedan:

    **Aktivera föreslagna konfiguration för OS-dump:**

    Ladda trasig OS-disk:

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

    Ta bort trasig OS-disk:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i vm-reparationskommandona](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) för att sätta ihop den virtuella datorn igen.
