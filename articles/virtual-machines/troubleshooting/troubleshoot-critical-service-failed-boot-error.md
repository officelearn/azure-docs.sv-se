---
title: KRITISKA tjänsten misslyckades när du startar en virtuell Azure-dator | Microsoft Docs
description: Lär dig att felsöka ”0x0000005A VERKSAMHETSKRITISKA tjänsten misslyckades”-fel som uppstår vid start | Microsoft Docs
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
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: d8140966f3ba8674938a4e21b0990371390d3516
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071298"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows visar ”kritiska tjänsten misslyckades” på blå skärm när du startar en virtuell Azure-dator
Den här artikeln beskriver felet ”kritiska tjänsten misslyckades” som kan uppstå när du startar en Windows virtuell dator (VM) i Microsoft Azure. Det ger felsökningssteg för att hjälpa dig att lösa problemen. 

> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptom"></a>Symtom 

En virtuell Windows-dator startar inte. När du checkar Start skärmbilderna [Startdiagnostik](./boot-diagnostics.md), du ser något av följande felmeddelanden på en blå skärm:

- ”Datorn råkade ut för ett problem och måste startas om. Du kan starta om. Mer information om det här problemet och möjliga korrigeringar på http://windows.com/stopcode. Om du anropar en Supportkontakt och be dem ge den här informationen: stoppkod: kritiska tjänsten misslyckades ” 
- ”Datorn råkade ut för ett problem och måste startas om. Vi samlar in bara viss felinformation och vi ska starta om för dig. Om du vill veta mer kan du söka online senare för det här felet: CRITICAL_SERVICE_FAILED ”

## <a name="cause"></a>Orsak

Det finns olika orsaker till stoppfel. De vanligaste orsakerna är:
- Problem med en drivrutin
- Skadad systemfil eller minne
- Programmet får åtkomst till en otillåtna sektor av minne

## <a name="solution"></a>Lösning 

Du löser problemet, [kontakta supporten och skicka den här filen](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), som hjälper oss att diagnostisera problemet snabbare eller försök att följande självhjälp-lösning.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. Ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disk till virtuell återställningsdator](./troubleshoot-recovery-disks-portal-windows.md). 
3. Upprätta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

### <a name="enable-dump-logs-and-serial-console"></a>Aktivera dump loggar och Seriekonsol

Dump-loggen och [Seriekonsolen](./serial-console-windows.md) hjälper oss att göra ytterligare felsökning.

Kör följande skript för att aktivera dump loggar och Seriekonsol.

1. Öppna en upphöjd kommandotolk-session (Kör som administratör).
2. Kör följande skript:

    I det här skriptet kan anta vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Du bör ersätta det med lämpligt värde för den virtuella datorn.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Ersätt osignerade drivrutiner

1. Kör följande kommando från en upphöjd kommandotolk på den Virtuella återställningsdatorn. Det här kommandot anger berörda OS-disken för att starta i felsäkert läge vid nästa start:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Till exempel OS-disken bifogade är enhet F, kör du följande kommando:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Koppla från den OS-disken och sedan koppla OS-disken till den berörda virtuella datorn igen](troubleshoot-recovery-disks-portal-windows.md). Den virtuella datorn startas i felsäkert läge. Om felet kvarstår går du till den [valfritt steg](#optional-analysis-the-dump-logs-in-boot-debug-mode).
3. Öppna den **kör** rutan och kör **verifierare** att starta verktyget verifierare drivrutinshanteraren.
4. Välj **automatiskt välja osignerade drivrutiner**, och klicka sedan på **nästa**.
5. Du får listan över de drivrutinsfiler som är osignerade. Kom ihåg filnamnen.
6. Kopiera samma versioner av dessa filer från en fungerande virtuell dator och Ersätt sedan dessa osignerade filer. 

7. Ta bort inställningarna för säker start:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Starta om den virtuella datorn. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Valfritt: Analysera dump-loggar i Dump krascha läge

Följ dessa steg om du vill analysera loggarna dump själv:

1. Koppla OS-disken till en virtuell dator för återställning.
2. Bläddra till på OS-disken bifogade **\windows\system32\config**. Kopiera alla filer som en säkerhetskopia om en återställning.
3. Starta **Registereditorn** (regedit.exe).
4. Välj den **HKEY_LOCAL_MACHINE** nyckel. På menyn, Välj **filen** > **Läs in registreringsdatafil**.
5. Bläddra till den **\windows\system32\config\SYSTEM** mapp på den OS-disken bifogade. Namnet på hive, ange **BROKENSYSTEM**. Den nya registreringsdatafilen visas under den **HKEY_LOCAL_MACHINE** nyckel.
6. Bläddra till **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** och gör följande ändringar:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Välj **BROKENSYSTEM**. På menyn, väljer **filen** > **ta bort registreringsdata**.
8.  Ändra BCD-installationsprogrammet för att starta i felsökningsläge. Kör följande kommandon från en upphöjd kommandotolk:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Koppla från den OS-disken och sedan koppla OS-disken till den berörda virtuella datorn igen](troubleshoot-recovery-disks-portal-windows.md).
10. Starta den virtuella datorn för att se om det visar kraschdumpfiler. Hitta den fil som inte kan läsas in. Du måste ersätta den här filen med en fil fungerande virtuell dator. 

    Följande är exempel på kraschdumpfiler. Du kan se att den **fel** på filecrypt.sys ”: FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys”.

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: http://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. När den virtuella datorn fungerar och starta normalt, tar du bort dumpa krascha-inställningar:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```