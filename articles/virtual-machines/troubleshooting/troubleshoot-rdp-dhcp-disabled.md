---
title: Det går inte att fjärransluta till Azure Virtual Machines eftersom DHCP är inaktiverat | Microsoft Docs
description: Lär dig att felsöka RDP problem som orsakas av DHCP-klienttjänsten har inaktiverats i Microsoft Azure. | Microsoft Docs
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
ms.openlocfilehash: daddb859c6bfc6309ef833c6c6c3ea43c70f1889
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652288"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Kan inte använda RDP till Azure Virtual Machines eftersom DHCP-klienttjänsten har inaktiverats

Den här artikeln beskriver ett problem där det går inte att fjärrskrivbord till Azure Windows Virtual Machines (VM) när tjänsten DHCP Client har inaktiverats i den virtuella datorn.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Symtom
Du kan inte göra en RDP-anslutning en virtuell dator i Azure eftersom DHCP-klienttjänsten har inaktiverats på den virtuella datorn. När du checkar skärmbilden den [Startdiagnostik](../troubleshooting/boot-diagnostics.md) i Azure-portalen, visas den virtuella datorn startas normalt och väntar på autentiseringsuppgifter i inloggningsskärmen. Du fjärrvisa händelseloggarna på den virtuella datorn med hjälp av Loggboken. Du ser att DHCP-klienttjänsten inte är igång eller inte går att starta. Följande ett exempel logga:

**Loggar namnet**: System </br>
**Källa**: Service Control Manager </br>
**Datum**: 12/16/2015 11:19:36 AM </br>
**Händelse-ID**: 7022 </br>
**Uppgift kategori**: Ingen </br>
**Nivå**: Fel </br>
**Nyckelord**: Klassisk</br>
**Användaren**: Gäller inte </br>
**Datorn**: myvm.cosotos.com</br>
**Beskrivning**: DHCP-klienttjänsten Avstannade vid start.</br>

Du kan använda Seriekonsol för åtkomst till funktionen för att fråga händelsen loggar 7022 med följande kommando för Resurshanterar-VM:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

För klassiska virtuella datorer behöver du arbetar i offlineläge och samlar in loggarna manuellt.

## <a name="cause"></a>Orsak

DHCP-klienttjänsten körs inte på den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller bara för DHCP-klienttjänsten och inte DHCP-servern.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att aktivera DHCP eller [Återställ nätverksgränssnittet](reset-network-interface.md) för den virtuella datorn.

### <a name="use-serial-control"></a>Använda seriell kontroll

1. Ansluta till [seriella konsolen och öppna CMD instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Om Seriekonsolen inte är aktiverad på den virtuella datorn finns i [Återställ nätverksgränssnittet](reset-network-interface.md).
2. Kontrollera om DHCP är inaktiverat i nätverksgränssnittet:

        sc query DHCP
3. Om DHCP är stoppad, försök att starta tjänsten

        sc start DHCP

4. Skicka frågor till tjänsten igen för att se till att tjänsten har startats.

        sc query DHCP

    Försök att ansluta till den virtuella datorn och se om problemet är löst.
5. Om tjänsten inte startar, använder du följande lämplig lösning utifrån felmeddelandet som du har fått:

    | Fel  |  Lösning |
    |---|---|
    | 5 – ÅTKOMST NEKAD  | Se [DHCP-klienttjänsten har stoppats på grund av ett felmeddelande om nekad](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Se [DHCP-klienttjänsten inaktiveras](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.   |
    | 1067 - ERROR_PROCESS_ABORTED |Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Se [DHCP-klienttjänsten misslyckas på grund av inloggningsfel](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Se [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Se [DHCP-klienttjänsten inaktiveras](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.  |
    |1053 | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-klienttjänsten har stoppats på grund av ett felmeddelande om nekad

1. Ansluta till [Seriekonsolen](serial-console-windows.md) och öppna en PowerShell-instans.
2. Hämta det Övervakare för processen genom att köra följande skript:

   ```powershell
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
4. Återskapa problemet genom att starta tjänsten som genererar den **åtkomst nekad** meddelande:

   ```
   sc start DHCP
   ```

   När det inte avsluta spårningen Övervakare för processen:

   ```
   procmon /Terminate
   ```
5. Samla in den **c:\temp\ProcMonTrace.PML** fil:

    1. [Anslut en datadisk till den virtuella datorn](../windows/attach-managed-disk-portal.md
).
    2. Använd Seriekonsol som du kan kopiera filen till den nya enheten. Till exempel `copy C:\temp\ProcMonTrace.PML F:\`. I det här kommandot är F enhetsbokstaven för den anslutna disken. Ersätt bokstaven efter behov med rätt värde.
    3. Koppla från dataenheten och koppla den till en aktiv virtuell dator som har Övervakare för processen ubstakke installerad.

6. Öppna **ProcMonTrace.PML** med hjälp av Övervakare för processen på fungerande virtuell dator. Sedan filtrera efter **resultatet är NEKAD**, enligt följande skärmbild:

    ![Filtrera efter resultat i Övervakare för processen](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Åtgärda den registernycklar, mappar eller filer som finns på utdata. Det här problemet orsakas vanligtvis när den inloggning som används på tjänsten inte har ACL-behörighet att komma åt dessa objekt. För att fastställa rätt ACL-behörighet för kontot loggar in, kan du kontrollera en felfri virtuell dator.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-klienttjänsten är inaktiverad

1. Återställa tjänsten till dess standardvärde för start:

   ```
   sc config DHCP start= auto
   ```

2. Starta tjänsten:

   ```
   sc start DHCP
   ```

3. Fråga status för tjänsten igen så att den körs:

   ```
   sc query DHCP
   ```

4. Försök att ansluta till den virtuella datorn med hjälp av fjärrskrivbord.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP-klienttjänsten misslyckas på grund av inloggningsfel

1. Eftersom det här problemet uppstår om konto för tjänststart för den här tjänsten har ändrats, kan du återställa kontot som ska standardstatusen:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Starta tjänsten:

        sc start DHCP
3. Försök att ansluta till den virtuella datorn med hjälp av fjärrskrivbord.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-klienttjänsten kraschar eller låser sig

1. Om status för tjänsten har fastnat i den **startar** eller **stoppar** tillstånd, försök att stoppa tjänsten:

        sc stop DHCP
2. Isolera tjänsten på sin egen 'svchost ”-behållaren:

        sc config DHCP type= own
3. Starta tjänsten:

        sc start DHCP
4. Om tjänsten fortfarande inte startar [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn. Se till att den anslutna disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
3.  Öppna en upphöjd kommandotolk-instans (**kör som administratör**). Kör sedan följande skript. Det här skriptet förutsätter att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är **F**. Ersätt bokstaven efter behov med värdet i den virtuella datorn.

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

4. [Koppla från den OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrollera sedan om problemet är löst.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa ditt problem.