---
title: Avstängning av virtuella azure-datorer har fastnat vid omstart, avstängning eller stoppa tjänster | Microsoft-dokument
description: Den här artikeln hjälper dig att felsöka tjänstfel i Virtuella Azure Windows-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371366"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Avstängning av Azure Windows VM har fastnat på "Omstart", "Stänga av" eller "Stoppa tjänster"

Den här artikeln innehåller steg för att lösa problemen med meddelanden om omstart, "Avstängning" eller "Stoppa tjänster" som du kan stöta på när du startar om en virtuell Dator (VM) i Microsoft Azure.

## <a name="symptoms"></a>Symtom

När du använder [Boot diagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att visa skärmdumpen av den virtuella datorn, kan du se att skärmdumpen visar meddelandet "Starta om", "stänga av" eller "Stoppa tjänster".

![Starta om, stänga av och stoppa tjänstskärmar](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Orsak

Windows använder avstängningsprocessen för att utföra systemunderhållsåtgärder och bearbeta ändringar som uppdateringar, roller och funktioner. Det rekommenderas inte att avbryta denna kritiska process tills den är klar. Beroende på antalet uppdateringar/ändringar och den virtuella datorns storlek kan processen ta lång tid. Om processen stoppas är det möjligt för operativsystemet att bli korrupt. Avbryt bara processen om det tar för lång tid.

## <a name="solution"></a>Lösning

### <a name="collect-a-process-memory-dump"></a>Samla in en processminnesdump

1. Hämta [Procdump-verktyget](http://download.sysinternals.com/files/Procdump.zip) till en ny eller befintlig datadisk som är kopplad till en fungerande virtuell dator från samma region.

2. Koppla bort disken som innehåller de filer som behövs från den fungerande virtuella datorn och koppla disken till den trasiga virtuella datorn. Vi kallar den här disken **verktyget disken**.

Använd [Seriekonsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) för att slutföra följande steg:

1. Öppna ett administrativt Powershell och kontrollera tjänsten som hänger vid stopp.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. På en administrativ CMD, få PID av hängde tjänsten.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Hämta ett exempel på minnesdump från den inhängde processen <STOPPING SERVICE>.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Nu döda hängde processen för att låsa upp avstängningsprocessen.

   ``
   taskkill /PID <PID> /t /f
   ``

När operativsystemet startar igen, om det startar normalt, sedan bara se till att OS konsistens är ok. Om skadan rapporteras kör du följande kommando tills disken är skadad gratis:

``
dism /online /cleanup-image /restorehealth
``

Om du inte kan samla in en processminnesdump, eller om problemet är rekursivt och du behöver en grundorsaksanalys, fortsätt med att samla in en OS-minnesdump nedan, fortsätt att öppna en supportbegäran.

### <a name="collect-an-os-memory-dump"></a>Samla in en os-minnesdump

Om problemet inte löser efter att ha väntat på att ändringarna ska bearbetas måste du samla in en minnesdumpfil och kontaktsupport. Så här samlar du in dumpfilen:

**Koppla OS-disken till en återställnings-VM**

1. Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Koppla OS-disken till en återställnings-VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Fjärrskrivbord till återställningsdass.

4. Om OS-disken är krypterad måste du stänga av krypteringen innan du går vidare till nästa steg. Mer information finns [i Dekryptera den krypterade OS-disken i den virtuella datorn som inte kan starta](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Hitta dumpfil och skicka en supportbiljett**

1. På återställningsdatorn går du till windowsmappen i den anslutna OS-disken. Om drivrutinsbrevet som är tilldelat till den anslutna OS-disken är F måste du gå till F:\Windows.

2. Leta reda på filen memory.dmp och skicka sedan [en supportbiljett](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen.

Om du inte hittar dumpfilen flyttar du nästa steg för att aktivera dumplogg och seriekonsol.

**Aktivera dumplogg och seriekonsol**

Om du vill aktivera dumplogg och seriekonsol kör du följande skript.

1. Öppna förhöjd kommandotolkssession (Kör som administratör).

2. Kör följande skript:

   I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den anslutna OS-disken är F. Ersätt den med rätt värde i den virtuella datorn.

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

3. Kontrollera att det finns tillräckligt med utrymme på disken för att allokera lika mycket minne som RAM-minnet, vilket beror på storleken som du väljer för den här virtuella datorn.

4. Om det inte finns tillräckligt med utrymme eller om den virtuella datorn är stor (G, GS eller E-serien) kan du ändra platsen där filen ska skapas och hänvisa den till någon annan datadisk som är kopplad till den virtuella datorn. Om du vill ändra platsen måste du ändra följande nyckel:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Koppla från OS-disken och sätt sedan tillbaka OS-disken till den berörda virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Starta den virtuella datorn och öppna seriekonsolen.

7. Välj Skicka icke-maskerbart avbrott (NMI) för att utlösa minnesdumpen.

   ![Skicka avbrott som inte kan maskeras](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Bifoga OS-disken till en återställnings-VM igen, samla dumpfil.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen kontaktar du Microsoft-supporten för att fastställa orsaken.
