---
title: Fjärrskrivbordstjänster som inte startar på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker problem med Fjärrskrivbordstjänster när du ansluter till en virtuell dator | Microsoft Docs
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
ms.openlocfilehash: 92c4a40de7e35d0580fe407e36305a50ad68094c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981792"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Fjärrskrivbordstjänster som inte startar på en virtuell Azure-dator

Den här artikeln beskriver hur du felsöker problem när du ansluter till en virtuell Azure-dator (VM) och Fjärrskrivbordstjänster, eller TermService, inte startar eller inte går att starta.

> [!NOTE]  
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../azure-resource-manager/management/deployment-models.md). I den här artikeln beskrivs hur du använder distributions modellen Resource Manager. Vi rekommenderar att du använder den här modellen för nya distributioner i stället för den klassiska distributions modellen.

## <a name="symptoms"></a>Symtom

När du försöker ansluta till en virtuell dator uppstår följande scenarier:

- Skärm bilden för den virtuella datorn visar att operativ systemet är fullständigt inläst och väntar på autentiseringsuppgifter.

    ![Skärm bild av VM-status](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Du fjärrvisa händelseloggarna på den virtuella datorn med hjälp av Loggboken. Du ser att Fjärrskrivbordstjänster, TermService, inte startar eller inte startar. Följande logg är ett exempel:

    **Logg namn**: system </br>
    **Källa**: tjänst kontroll hanteraren </br>
    **Datum**: 12/16/2017 11:19:36 am</br>
    **Händelse-ID**: 7022</br>
    **Uppgift kategori**: ingen</br>
    **Nivå**: fel</br>
    **Nyckelord**: klassisk</br>
    **Användare**: ej tillämpligt</br>
    **Dator**: VM.contoso.com</br>
    **Beskrivning**: den Fjärrskrivbordstjänster tjänsten stannade vid start. 

    Du kan också använda funktionen för seriell åtkomst konsol för att söka efter dessa fel genom att köra följande fråga: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Orsak
 
Det här problemet beror på att Fjärrskrivbordstjänster inte körs på den virtuella datorn. Orsaken kan vara beroende av följande scenarier: 

- TermService-tjänsten är inställd på **inaktive rad**. 
- TermService-tjänsten kraschar eller svarar inte. 
- TermService startar inte på grund av en felaktig konfiguration.

## <a name="solution"></a>Lösning

Använd serie konsolen för att felsöka problemet. Du kan också [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla den virtuella DATORns OS-disk till en virtuell dator för återställning.

### <a name="use-serial-console"></a>Använd serie konsol

1. Öppna den [seriella konsolen](serial-console-windows.md) genom att välja **Support & fel sökning** > **seriell konsol**. Om funktionen är aktive rad på den virtuella datorn kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Ange **cmd** för att starta kanalen och hämta kanal namnet.

3. Växla till kanalen som kör CMD-instansen. I det här fallet ska det vara kanal 1:

   ```
   ch -si 1
   ```

4. Välj **RETUR** igen och ange ett giltigt användar namn och lösen ord, lokalt eller domän-ID för den virtuella datorn.

5. Fråga om status för TermService-tjänsten:

   ```
   sc query TermService
   ```

6. Om tjänst statusen är **stoppad**försöker du starta tjänsten:

    ```
    sc start TermService
     ``` 

7. Fråga tjänsten igen för att kontrol lera att tjänsten har startats:

   ```
   sc query TermService
   ```
8. Om tjänsten inte kan starta följer du lösningen baserat på det fel du fick:

    |  Fel |  Förslag |
    |---|---|
    |5 – ÅTKOMST NEKAD |Se [TermService-tjänsten har stoppats på grund av ett åtkomst nekad-fel](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Se [TermService-tjänsten är inaktive rad](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.|
    |1067 - ERROR_PROCESS_ABORTED  |Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Se [TermService-tjänsten Miss lyckas på grund av ett inloggnings fel](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Se [TermService-tjänsten kraschar eller låser sig](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Se [TermService-tjänsten är inaktive rad](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt. |
    |1753   |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService-tjänsten har stoppats på grund av problem med nekad åtkomst

1. Ansluta till [Seriekonsolen](serial-console-windows.md) och öppna en PowerShell-instans.
2. Hämta Process Monitor-verktyget genom att köra följande skript:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Nu startar en **procmon** spårning:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. Återskapa problemet genom att starta tjänsten som ger **åtkomst nekad**: 

   ```
   sc start TermService 
   ```

   När det inte avsluta spårningen Övervakare för processen:

   ```   
   procmon /Terminate 
   ```

5. Samla in filen **c:\temp\ProcMonTrace.PML**:

    1. [Anslut en datadisk till den virtuella datorn](../windows/attach-managed-disk-portal.md
).
    2. Använd Seriekonsol som du kan kopiera filen till den nya enheten. Till exempel `copy C:\temp\ProcMonTrace.PML F:\`. I det här kommandot är F enhetsbokstaven för den anslutna disken.
    3. Koppla från data enheten och koppla den på en fungerande virtuell dator som har Process Monitor-ubstakke installerad.

6. Öppna **ProcMonTrace. PML** genom att använda Process Monitor den aktiva virtuella datorn. Filtrera efter **resultat är åtkomst nekad**, som visas på följande skärm bild:

    ![Filtrera efter resultat i process övervakaren](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Åtgärda den registernycklar, mappar eller filer som finns på utdata. Det här problemet orsakas vanligtvis när den inloggning som används på tjänsten inte har ACL-behörighet att komma åt dessa objekt. Om du vill veta rätt ACL-behörighet för inloggnings kontot kan du kontrol lera en felfri virtuell dator. 

#### <a name="termservice-service-is-disabled"></a>TermService-tjänsten är inaktive rad

1. Återställa tjänsten till dess standardvärde för start:

   ```
   sc config TermService start= demand 
   ```

2. Starta tjänsten:

   ```
   sc start TermService
   ```

3. Fråga dess status igen för att kontrol lera att tjänsten körs:

   ```
   sc query TermService 
   ```

4. Försök att ansluta till den virtuella datorn med hjälp av fjärr skrivbord.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>TermService-tjänsten Miss lyckas på grund av ett inloggnings fel

1. Det här problemet uppstår om start kontot för den här tjänsten har ändrats. Ändra tillbaka till standardvärdet: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Starta tjänsten:

        sc start TermService
3. Försök att ansluta till den virtuella datorn med hjälp av fjärr skrivbord.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService-tjänsten kraschar eller låser sig
1. Om tjänstens status är fastnat i **starten** eller **stoppas**försöker du stoppa tjänsten: 

        sc stop TermService
2. Isolera tjänsten på sin egen 'svchost ”-behållaren:

        sc config TermService type= own
3. Starta tjänsten:

        sc start TermService
4. [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om tjänsten fortfarande inte kan startas.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn. Se till att den anslutna disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
3. Öppna en upphöjd kommandotolk-instans (**kör som administratör**). Kör sedan följande skript. Vi antar att enhets beteckningen som är kopplad till den anslutna OS-disken är **F**. Ersätt det med lämpligt värde i den virtuella datorn. 

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

4. [Koppla från den OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrol lera sedan om problemet är löst.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.
