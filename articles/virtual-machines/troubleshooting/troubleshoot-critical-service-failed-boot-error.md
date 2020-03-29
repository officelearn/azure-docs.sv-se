---
title: KRITISK TJÄNST MISSLYCKADES vid start av en virtuell Azure-dator | Microsoft-dokument
description: Lär dig hur du felsöker felet "0x0000005A-CRITICAL SERVICE FAILED" som inträffar vid uppstart | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921461"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows visar "CRITICAL SERVICE FAILED" på blå skärm vid start av en Virtuell Azure-dator
I den här artikeln beskrivs felet "CRITICAL SERVICE FAILED" som kan uppstå när du startar en virtuell Dator (Windows Virtual Machine) i Microsoft Azure. Det innehåller felsökningssteg för att lösa problemen. 


## <a name="symptom"></a>Symptom 

En Virtuell Windows-dator startar inte. NÃ¤1s pÃ¤ ã¤nsning av startskärmarna i [Boot diagnostics](./boot-diagnostics.md)visas nÃ¤ringar på en blå skärm:

- "Datorn stötte på ett problem och måste starta om. Du kan starta om. Mer information om problemet och eventuella https://windows.com/stopcodekorrigeringar finns i . Om du ringer en supportperson, ge dem denna information: Stoppkod: KRITISK TJÄNST MISSLYCKADES" 
- "Datorn stötte på ett problem och måste starta om. Vi samlar bara in lite felinformation, och sedan startar vi om åt dig. Om du vill veta mer kan du söka på nätet senare efter det här felet: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Orsak

Det finns olika orsaker till stoppfel. De vanligaste orsakerna är:
- Problem med en drivrutin
- Skadad systemfil eller minne
- Programmet kommer åt en förbjuden sektor av minnet

## <a name="solution"></a>Lösning 

För att lösa problemet kontaktar [du supporten och skickar in en dumpfil](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), som hjälper oss att diagnostisera problemet snabbare eller prova följande självhjälpslösning.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en återställnings-VM](./troubleshoot-recovery-disks-portal-windows.md). 
3. Upprätta en fjärrskrivbordsanslutning till återställningsdatorn.

### <a name="enable-dump-logs-and-serial-console"></a>Aktivera dumploggar och seriekonsol

Dumploggen och [seriekonsolen](./serial-console-windows.md) hjälper oss att göra ytterligare felsökning.

Om du vill aktivera dumploggar och Seriekonsol kör du följande skript.

1. Öppna en upphöjd kommandotolkssession (kör som administratör).
2. Kör följande skript:

    I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den bifogade OS-disken är F. Du bör ersätta den med rätt värde för den virtuella datorn.

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

### <a name="replace-the-unsigned-drivers"></a>Ersätta de osignerade drivrutinerna

1. På återställningsdatorn kör du följande kommando från en upphöjd kommandotolk. Det här kommandot anger att den berörda OS-disken startar i felsäkert läge vid nästa start:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Om os-disken som du till exempel har anslutit är enhet F kör du följande kommando:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Koppla från OS-disken och anslut sedan OS-disken till den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md). Den virtuella datorn startar i felsäkert läge. Om felet kvarstår går du till det valfria steget.
3. Öppna rutan **Kör** och kör **kontrollören** för att starta verktyget Drivrutinsverifierare.
4. Välj **Välj Automatiskt osignerade drivrutiner**och klicka sedan på **Nästa**.
5. Du får listan över drivrutinsfiler som är osignerade. Kom ihåg filnamnen.
6. Kopiera samma versioner av dessa filer från en fungerande virtuell dator och ersätt sedan dessa osignerade filer. 

7. Ta bort de säkra startinställningarna:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Starta om den virtuella datorn. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Valfritt: Analysera dumploggarna i dumpkraschläge

Så här analyserar du dumploggarna:

1. Koppla OS-disken till en virtuell dator för återställning.
2. På os-disken som du har bifogat bläddrar du till **\windows\system32\config**. Kopiera alla filer som en säkerhetskopia om en återställning krävs.
3. Starta **Registereditorn** (regedit.exe).
4. Välj **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten. Välj > **Filinläsningsdatafil på**menyn . **File**
5. Bläddra till mappen **\windows\system32\config\SYSTEM** på os-disken som du har bifogat. För namnet på registreringsdatafilen anger du **BROKENSYSTEM**. Den nya registreringsdatafilen visas under **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten.
6. Bläddra till **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** och gör följande ändringar:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Välj **BROKENSYSTEM**. Välj > **Filavlastningsdatafil**på menyn . **File**
8.  Ändra BCD-installationen för att starta i felsökningsläge. Kör följande kommandon från en upphöjd kommandotolk:

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
9. [Koppla från OS-disken och anslut sedan OS-disken till den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).
10. Starta den virtuella datorn för att se om den visar dumpanalys. Leta reda på filen som inte kan läsas in. Du måste ersätta den här filen med en fil från den fungerande virtuella datorn. 

    Följande är ett urval av dumpanalys. Du kan se att **felet** finns på filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. När den virtuella datorn fungerar och startar normalt tar du bort inställningarna för Dump Crash:

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
