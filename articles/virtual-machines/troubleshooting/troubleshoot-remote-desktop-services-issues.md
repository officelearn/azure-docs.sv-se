---
title: Fjärrskrivbordstjänster startar inte på en virtuell Azure-dator | Microsoft-dokument
description: Lär dig hur du felsöker problem med Fjärrskrivbordstjänster när du ansluter till en virtuell dator | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919472"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Fjärrskrivbordstjänster startar inte på en virtuell Azure-dator

I den här artikeln beskrivs felsÃ¶kning av problem nÃ¤r du ansluter till en virtuell Azure-dator (VM) och FJÃ¤rrskrivbordstjänster, eller TermService, inte startar eller misslyckas med att starta.


## <a name="symptoms"></a>Symtom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till en virtuell dator visas fÃ¶nde:

- Skärmbilden för den virtuella datorn visar att operativsystemet är fullständigt laddat och väntar på autentiseringsuppgifter.

    ![Skärmbild av vm-status](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Du kan fjärrvisa händelseloggarna i den virtuella datorn med hjälp av Loggboken. Du ser att Fjärrskrivbordstjänster, TermService, inte startar eller inte startar. Följande logg är ett exempel:

    **Loggnamn**: System </br>
    **Källa**: Service Control Manager </br>
    **Datum**: 2017-12-16 11:19:36</br>
    **Händelse-ID**: 7022</br>
    **Uppgiftskategori:** Ingen</br>
    **Nivå:** Fel</br>
    **Nyckelord:** Classic</br>
    **Användare**: Ej</br>
    **Dator**: vm.contoso.com</br>
    **Beskrivning**: Tjänsten Fjärrskrivbordstjänster hängde på start. 

    Du kan också använda funktionen Serial Access Console för att leta efter dessa fel genom att köra följande fråga: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Orsak
 
Det här problemet beror på att Fjärrskrivbordstjänster inte körs på den virtuella datorn. Orsaken kan bero på följande scenarier: 

- Tjänsten TermService är **inaktiverad**. 
- TermService-tjänsten kraschar eller svarar inte. 
- TermService startar inte på grund av en felaktig konfiguration.

## <a name="solution"></a>Lösning

Använd seriekonsolen för att felsöka problemet. Eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla OS-disken för den virtuella datorn till en återställnings-VM.

### <a name="use-serial-console"></a>Använda seriell konsol

1. Öppna [seriekonsolen](serial-console-windows.md) genom att välja **Support & Felsökning av** > **seriekonsol**. Om funktionen är aktiverad på den virtuella datorn kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Ange **CMD** för att starta kanalen och hämta kanalnamnet.

3. Växla till kanalen som kör CMD-instansen. I detta fall bör det vara kanal 1:

   ```
   ch -si 1
   ```

4. Välj **Ange** igen och ange ett giltigt användarnamn och lösenord, lokalt eller domän-ID, för den virtuella datorn.

5. Fråga status för TermService-tjänsten:

   ```
   sc query TermService
   ```

6. Om tjänststatusen visar **Stoppad**försöker du starta tjänsten:

    ```
    sc start TermService
     ``` 

7. Fråga tjänsten igen för att kontrollera att tjänsten har startats:

   ```
   sc query TermService
   ```
8. Om tjänsten inte startar följer du lösningen baserat på felet du fick:

    |  Fel |  Förslag |
    |---|---|
    |5- NEKAD ÅTKOMST |Se [TermService-tjänsten stoppas på grund av ett åtkomst nekat fel](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Se [TermService-tjänsten är inaktiverad](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.|
    |1067 - ERROR_PROCESS_ABORTED  |Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Se [TermService-tjänsten misslyckas på grund av inloggningsfel](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Se [TermService-tjänsten är inaktiverad](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt. |
    |1753   |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få problemet löst snabbt.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService-tjänsten stoppas på grund av ett problem med åtkomst nekad

1. Anslut till [seriekonsolen](serial-console-windows.md) och öppna en PowerShell-instans.
2. Hämta verktyget Process monitor genom att köra följande skript:

   ```
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

4. Återskapa problemet genom att starta tjänsten som ger **Åtkomst nekad:** 

   ```
   sc start TermService 
   ```

   När det misslyckas avslutar du processövervakarspårningen:

   ```   
   procmon /Terminate 
   ```

5. Samla in filen **c:\temp\ProcMonTrace.PML**:

    1. [Koppla en datadisk till den virtuella datorn](../windows/attach-managed-disk-portal.md
).
    2. Använd Seriekonsolen, du kan kopiera filen till den nya enheten. Till exempel `copy C:\temp\ProcMonTrace.PML F:\`. I det här kommandot är F drivrutinsbrevet för den bifogade datadisken.
    3. Koppla bort dataenheten och bifoga den på en fungerande virtuell dator som har Process Monitor ubstakke installerat.

6. Öppna **ProcMonTrace.PML** med hjälp av Process Monitor den fungerande virtuella datorn. Sedan filtrera efter **resultat är ACCESS NEKAD**, som visas i följande skärmdump:

    ![Filtrera efter resultat i Processövervakaren](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Åtgärda registernycklarna, mapparna eller filerna som finns på utdata. Det här problemet uppstår vanligtvis när inloggningskontot som används på tjänsten inte har ACL-behörighet att komma åt dessa objekt. Om du vill veta rätt behörighet för inloggningskontot kan du kontrollera en felfri virtuell dator. 

#### <a name="termservice-service-is-disabled"></a>TermService-tjänsten är inaktiverad

1. Återställa tjänsten till standardstartvärdet:

   ```
   sc config TermService start= demand 
   ```

2. Starta tjänsten:

   ```
   sc start TermService
   ```

3. Fråga om dess status igen för att se till att tjänsten körs:

   ```
   sc query TermService 
   ```

4. Försök att ansluta till vm med fjärrskrivbord.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>TermService-tjänsten misslyckas på grund av inloggningsfel

1. Det här problemet uppstår om startkontot för den här tjänsten har ändrats. Ändrade detta tillbaka till dess standard: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Starta tjänsten:

        sc start TermService
3. Försök att ansluta till vm med fjärrskrivbord.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService-tjänsten kraschar eller låser sig
1. Om tjänststatusen har fastnat i **Starta** eller **Stoppa**försöker du stoppa tjänsten: 

        sc stop TermService
2. Isolera tjänsten på sin egen "svchost"-behållare:

        sc config TermService type= own
3. Starta tjänsten:

        sc start TermService
4. Om tjänsten fortfarande inte startar [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en anslutning till återställningsdatorn till återställningsdatorn. Kontrollera att den anslutna disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.
3. Öppna en upphöjd kommandotolksinstans (**Kör som administratör**). Kör sedan följande skript. Vi antar att enhetsbeteckningen som är tilldelad den bifogade OS-disken är **F**. Ersätt den med rätt värde i den virtuella datorn. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrollera sedan om problemet är löst.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.
