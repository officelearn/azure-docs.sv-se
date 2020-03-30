---
title: Det går inte att fjärransluta till virtuella Azure-datorer eftersom DHCP är inaktiverat| Microsoft-dokument
description: Lär dig hur du felsöker RDP-problem som orsakas av DHCP-klienttjänsten är inaktiverat i Microsoft Azure.| Microsoft-dokument
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
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749888"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Det går inte att rdp till virtuella Azure-datorer eftersom DHCP-klienttjänsten är inaktiverad

I den hÃ¤r artikeln beskrivs ett problem som gÃ¶r att fJÃ¤rrskrivbord fÃ¶r Azure Windows Virtual Machines (VM) efter att DHCP-klienttjänsten har inaktiverats i den virtuella datorn.


## <a name="symptoms"></a>Symtom
Du kan inte göra en RDP-anslutning till en virtuell dator i Azure eftersom DHCP-klienttjänsten är inaktiverad i den virtuella datorn. När du kontrollerar skärmbilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure-portalen ser du vm-stövlarna normalt och väntar på autentiseringsuppgifter på inloggningsskärmen. Du kan fjärrvisa händelseloggarna i den virtuella datorn med hjälp av Loggboken. Du ser att DHCP-klienttjänsten inte har startats eller inte startar. Följande exempellogg:

**Loggnamn**: System </br>
**Källa**: Service Control Manager </br>
**Datum**: 2015-12-16 11:19:36 </br>
**Händelse-ID**: 7022 </br>
**Uppgiftskategori:** Ingen </br>
**Nivå:** Fel </br>
**Nyckelord:** Classic</br>
**Användare**: Ej </br>
**Dator**: myvm.cosotos.com</br>
**Beskrivning**: DHCP-klienttjänsten hängde på start.</br>

För virtuella resource manager-datorer kan du använda funktionen Serial Access Console för att fråga efter händelseloggarna 7022 med följande kommando:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

För klassiska virtuella datorer måste du arbeta i offlineläge och samla in loggarna manuellt.

## <a name="cause"></a>Orsak

DHCP-klienttjänsten körs inte på den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller endast för DHCP-klienttjänsten och inte DHCP-servern.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

LÃ¶s problemet genom att anse serial control to enable DHCP or [reset network interface](reset-network-interface.md) for the VM.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [seriekonsolen och öppna CMD-instansen](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Om seriekonsolen inte är aktiverad på den virtuella datorn läser du [Återställa nätverksgränssnittet](reset-network-interface.md).
2. Kontrollera om DHCP är inaktiverat i nätverksgränssnittet:

        sc query DHCP
3. Om DHCP stoppas försöker du starta tjänsten

        sc start DHCP

4. Fråga tjänsten igen för att kontrollera att tjänsten startas.

        sc query DHCP

    Försök att ansluta till den virtuella datorn och se om problemet är löst.
5. Om tjänsten inte startar använder du följande lösning, baserat på felmeddelandet som du fick:

    | Fel  |  Lösning |
    |---|---|
    | 5- NEKAD ÅTKOMST  | Se [DHCP-klienttjänsten stoppas på grund av ett åtkomst nekat fel](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Se [DHCP-klienttjänsten är inaktiverad](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.   |
    | 1067 - ERROR_PROCESS_ABORTED |Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Se [DHCP-klienttjänsten misslyckas på grund av inloggningsfel](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Se [DHCP-klienttjänsten är inaktiverad](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.  |
    |1053 | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-klienttjänsten stoppas på grund av ett åtkomst nekat fel

1. Anslut till [seriekonsolen](serial-console-windows.md) och öppna en PowerShell-instans.
2. Hämta verktyget Process monitor genom att köra följande skript:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Nu starta en **procmon** spår:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Återskapa problemet genom att starta tjänsten som genererar meddelandet **Nekad åtkomst:**

   ```
   sc start DHCP
   ```

   När det misslyckas avslutar du processövervakarspårningen:

   ```
   procmon /Terminate
   ```
5. Samla in filen **c:\temp\ProcMonTrace.PML:**

    1. [Koppla en datadisk till den virtuella datorn](../windows/attach-managed-disk-portal.md
).
    2. Använd Seriekonsolen, du kan kopiera filen till den nya enheten. Till exempel `copy C:\temp\ProcMonTrace.PML F:\`. I det här kommandot är F drivrutinsbrevet för den bifogade datadisken. Ersätt bokstaven efter behov med rätt värde.
    3. Koppla bort dataenheten och bifoga den sedan till en fungerande virtuell dator som har Process Monitor ubstakke installerat.

6. Öppna **ProcMonTrace.PML** med hjälp av Process Monitor på den fungerande virtuella datorn. Sedan filtrera efter **resultat är ACCESS NEKAD**, som visas i följande skärmdump:

    ![Filtrera efter resultat i Processövervakaren](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Åtgärda registernycklarna, mapparna eller filerna som finns på utdata. Det här problemet uppstår vanligtvis när inloggningskontot som används på tjänsten inte har ACL-behörighet att komma åt dessa objekt. Om du vill ta reda på rätt behörighet för inloggningskontot kan du kontrollera en felfri virtuell dator.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-klienttjänsten är inaktiverad

1. Återställa tjänsten till standardstartvärdet:

   ```
   sc config DHCP start= auto
   ```

2. Starta tjänsten:

   ```
   sc start DHCP
   ```

3. Fråga tjänststatusen igen för att kontrollera att den körs:

   ```
   sc query DHCP
   ```

4. Försök att ansluta till den virtuella datorn med fjärrskrivbord.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP-klienttjänsten misslyckas på grund av inloggningsfel

1. Eftersom det här problemet uppstår om startkontot för den här tjänsten har ändrats återställer du kontot till standardstatus:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Starta tjänsten:

        sc start DHCP
3. Försök att ansluta till den virtuella datorn med fjärrskrivbord.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-klienttjänsten kraschar eller låser sig

1. Om tjänststatusen har fastnat i **läget Start** **eller Stopp** försöker du stoppa tjänsten:

        sc stop DHCP
2. Isolera tjänsten på sin egen "svchost"-behållare:

        sc config DHCP type= own
3. Starta tjänsten:

        sc start DHCP
4. Om tjänsten fortfarande inte startar [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en anslutning till återställningsdatorn till återställningsdatorn. Kontrollera att den anslutna disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.
3.  Öppna en upphöjd kommandotolksinstans (**Kör som administratör**). Kör sedan följande skript. Det här skriptet förutsätter att enhetsbeteckningen som är tilldelad den anslutna OS-disken är **F**. Ersätt bokstaven efter behov med värdet i den virtuella datorn.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrollera sedan om problemet är löst.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.