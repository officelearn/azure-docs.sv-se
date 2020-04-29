---
title: Azure Virtual Machines avstängning har fastnat vid omstart, avstängning eller avstängning av tjänster | Microsoft Docs
description: Den här artikeln hjälper dig att felsöka tjänst fel i Azure Virtuella Windows-datorer.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77371366"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM-avstängning har fastnat på "starta om", "stänga av" eller "stoppa tjänster"

Den här artikeln innehåller steg för att lösa problemen med "starta om", "stänga av" eller "stoppa tjänster" som du kan stöta på när du startar om en virtuell Windows-dator (VM) i Microsoft Azure.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att Visa skärm bilden för den virtuella datorn kan det hända att skärm bilden visar meddelandet "omstart", "avstängning" eller "stoppa tjänster".

![Starta om, stänga av och stoppa tjänst skärmar](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Orsak

Windows använder avstängnings processen för att utföra system underhålls åtgärder och bearbeta ändringar som uppdateringar, roller och funktioner. Vi rekommenderar inte att du avbryter den här kritiska processen förrän den är klar. Beroende på antalet uppdateringar/ändringar och storleken på den virtuella datorn kan processen ta lång tid. Om processen har stoppats är det möjligt att operativ systemet skadas. Avbryt bara processen om den tar för lång tid.

## <a name="solution"></a>Lösning

### <a name="collect-a-process-memory-dump"></a>Samla in en process minnes dumpning

1. Ladda ned [ProcDump-verktyget](http://download.sysinternals.com/files/Procdump.zip) till en ny eller befintlig datadisk som är ansluten till en fungerande virtuell dator från samma region.

2. Koppla bort disken som innehåller de filer som behövs från den virtuella datorn och koppla disken till den trasiga virtuella datorn. Vi ringer den här disken till **verktygs disken**.

Använd [serie konsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) för att utföra följande steg:

1. Öppna en administrativ PowerShell och kontrol lera att tjänsten är låst när den stoppas.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Hämta PID för den avstannade tjänsten i en administrativ kommando tolk.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Hämta ett exempel på en minnesdump från den avstannade processen <STOPPING SERVICE>.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Stoppa den avstannade processen för att låsa upp avstängnings processen.

   ``
   taskkill /PID <PID> /t /f
   ``

När operativ systemet har startat igen, om det startar normalt, ser du till att operativ systemets konsekvens är OK. Om ett fel rapporteras kör du följande kommando tills disken är skadad.

``
dism /online /cleanup-image /restorehealth
``

Om du inte kan samla in en minnes dumpning av processen, eller om det här problemet är rekursivt och du behöver en rotor Saks analys, fortsätter du med att samla in en dumpning av operativ systemet nedan. Fortsätt sedan med att öppna en supportbegäran.

### <a name="collect-an-os-memory-dump"></a>Samla in en minnesdump för operativ system

Om problemet inte löses när du väntar på att ändringarna ska bearbetas måste du samla in en minnesdumpfil och kontakta supporten. Samla in dumpfilen genom att följa dessa steg:

**Koppla OS-disken till en virtuell dator för återställning**

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Koppla OS-disken till en virtuell dator för återställning](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Fjärr skrivbord till den virtuella återställnings datorn.

4. Om operativ system disken är krypterad måste du stänga av krypteringen innan du går vidare till nästa steg. Mer information finns i [dekryptera den krypterade OS-disken på den virtuella datorn som inte kan starta](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Hitta en dumpfil och skicka ett support ärende**

1. På den virtuella datorn för återställning går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som har tilldelats till den anslutna OS-disken är F, måste du gå till F:\Windows.

2. Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen.

Om du inte hittar dumpfilen går du vidare till nästa steg för att aktivera dumpa logg och seriell konsol.

**Aktivera dumpa logg och seriell konsol**

Kör följande skript för att aktivera dumpa logg och seriell konsol.

1. Öppna en upphöjd kommando tolk session (kör som administratör).

2. Kör följande skript:

   I det här skriptet antar vi att den enhets beteckning som är kopplad till den anslutna OS-disken är F. Ersätt den med rätt värde i den virtuella datorn.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Kontrol lera att det finns tillräckligt med utrymme på disken för att allokera så mycket minne som RAM-minnet, vilket beror på den storlek som du väljer för den här virtuella datorn.

4. Om det inte finns tillräckligt med utrymme eller om den virtuella datorn är stor (G, GS eller E-serien) kan du ändra den plats där filen kommer att skapas och se om det finns andra data diskar som är kopplade till den virtuella datorn. Om du vill ändra plats måste du ändra följande nyckel:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Koppla från OS-disken och återanslut sedan OS-disken till den berörda virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Starta den virtuella datorn och få åtkomst till serie konsolen.

7. Välj Skicka icke-Maskbart avbrott (NMI) för att utlösa minnesdumpen.

   ![Skicka icke-Maskbart avbrott](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Koppla OS-disken till en återställnings-VM igen och samla in dump-filen.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen kontaktar du Microsoft-supporten för att ta reda på rotor saken.
