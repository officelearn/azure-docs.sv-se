---
title: Det går inte att fjärrans luta till Azure Virtual Machines eftersom DHCP är inaktive rad | Microsoft Docs
description: Lär dig hur du felsöker RDP-problem som orsakas av att tjänsten DHCP Client är inaktive rad i Microsoft Azure. | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75749888"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Det går inte att RDP till Azure Virtual Machines eftersom DHCP-klienttjänsten är inaktive rad

I den här artikeln beskrivs ett problem där du inte kan fjärrans luta till Azure Virtuella Windows-datorer (VM) när DHCP-klienttjänsten är inaktive rad på den virtuella datorn.


## <a name="symptoms"></a>Symtom
Du kan inte göra en RDP-anslutning till en virtuell dator i Azure eftersom DHCP-klienttjänsten är inaktive rad på den virtuella datorn. När du kontrollerar skärm bilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure Portal ser du att den virtuella datorn startar normalt och väntar på autentiseringsuppgifter på inloggnings skärmen. Du visar händelse loggarna på den virtuella datorn via en fjärr anslutning med hjälp av Loggboken. Du ser att DHCP-klienttjänsten inte har startats eller inte kan startas. Följande exempel logg:

**Logg namn**: system </br>
**Källa**: tjänst kontroll hanteraren </br>
**Datum**: 12/16/2015 11:19:36 am </br>
**Händelse-ID**: 7022 </br>
**Uppgifts kategori**: ingen </br>
**Nivå**: fel </br>
**Nyckelord**: klassisk</br>
**Användare**: ej tillämpligt </br>
**Dator**: myvm.cosotos.com</br>
**Beskrivning**: DHCP-klienttjänsten avstannade vid start.</br>

För virtuella Resource Manager-datorer kan du använda funktionen för konsol av seriell åtkomst för att fråga efter händelse loggarna 7022 med följande kommando:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

För klassiska virtuella datorer måste du arbeta i OFFLINE-läge och samla in loggarna manuellt.

## <a name="cause"></a>Orsak

DHCP-klienttjänsten körs inte på den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller bara för DHCP-klienttjänsten och inte DHCP-server.

## <a name="solution"></a>Lösning

Innan du följer de här stegen ska du ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att aktivera DHCP eller [återställa nätverks gränssnittet](reset-network-interface.md) för den virtuella datorn.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [serie konsolen och öppna cmd-instansen](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Om serie konsolen inte är aktive rad på den virtuella datorn, se [Återställ nätverks gränssnitt](reset-network-interface.md).
2. Kontrol lera om DHCP är inaktiverat i nätverks gränssnittet:

        sc query DHCP
3. Om DHCP är stoppad försöker du starta tjänsten

        sc start DHCP

4. Fråga tjänsten igen för att säkerställa att tjänsten har startats.

        sc query DHCP

    Försök att ansluta till den virtuella datorn och se om problemet är löst.
5. Om tjänsten inte startar använder du följande lösning, baserat på det fel meddelande som du fick:

    | Fel  |  Lösning |
    |---|---|
    | 5-ÅTKOMST NEKAD  | Se [DHCP-klienttjänsten stoppas på grund av ett åtkomst nekad-fel](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT   | Se hur [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 – ERROR_SERVICE_DISABLED  | Se [DHCP client service är inaktive rad](#dhcp-client-service-is-disabled).  |
    | 1059 – ERROR_CIRCULAR_DEPENDENCY  |[Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.   |
    | 1067 – ERROR_PROCESS_ABORTED |Se hur [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).   |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.  |
    |1069 – ERROR_SERVICE_LOGON_FAILED   |  Se [DHCP-klienttjänsten Miss lyckas på grund av inloggnings fel](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 – ERROR_SERVICE_START_HANG  | Se hur [DHCP-klienttjänsten kraschar eller låser sig](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 – ERROR_SERVICE_NEVER_STARTED  | Se [DHCP client service är inaktive rad](#dhcp-client-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.  |
    |1053 | [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-klienttjänsten har stoppats på grund av ett nekat åtkomst fel

1. Anslut till [serie konsolen](serial-console-windows.md) och öppna en PowerShell-instans.
2. Hämta Process Monitor-verktyget genom att köra följande skript:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Starta nu en **Procmon** -spårning:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Återskapa problemet genom att starta tjänsten som genererar meddelandet **åtkomst nekad** :

   ```
   sc start DHCP
   ```

   När det Miss lyckas avslutar du Process Monitor-spårningen:

   ```
   procmon /Terminate
   ```
5. Samla in **c:\temp\ProcMonTrace.PML** -filen:

    1. [Koppla en datadisk till den virtuella datorn](../windows/attach-managed-disk-portal.md
).
    2. Använd en serie konsol du kan kopiera filen till den nya enheten. Till exempel `copy C:\temp\ProcMonTrace.PML F:\`. I det här kommandot är F driv rutins beteckningen för den anslutna data disken. Ersätt bokstaven efter behov med rätt värde.
    3. Koppla från data enheten och Anslut den sedan till en fungerande virtuell dator som har Process Monitor-ubstakke installerad.

6. Öppna **ProcMonTrace. PML** genom att använda Process Monitor på den virtuella datorn. Filtrera efter **resultat är åtkomst nekad**, som visas på följande skärm bild:

    ![Filtrera efter resultat i process övervakaren](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Åtgärda de register nycklar, mappar eller filer som finns i utdata. Det här problemet uppstår vanligt vis när det inloggnings konto som används på tjänsten inte har ACL-behörighet för att komma åt dessa objekt. Om du vill ta reda på rätt ACL-behörighet för inloggnings kontot kan du kontrol lera en felfri virtuell dator.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-klienttjänsten är inaktive rad

1. Återställa tjänsten till standardvärdet start värde:

   ```
   sc config DHCP start= auto
   ```

2. Starta tjänsten:

   ```
   sc start DHCP
   ```

3. Fråga tjänstens status igen för att se till att den körs:

   ```
   sc query DHCP
   ```

4. Försök att ansluta till den virtuella datorn med hjälp av fjärr skrivbord.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Tjänsten DHCP Client Miss lyckas på grund av ett inloggnings fel

1. Eftersom det här problemet uppstår om start kontot för den här tjänsten har ändrats återställer du kontot till dess standard status:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Starta tjänsten:

        sc start DHCP
3. Försök att ansluta till den virtuella datorn med hjälp av fjärr skrivbord.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-klienttjänsten kraschar eller låser sig

1. Om tjänstens status är fastnat i tillståndet **Starta** eller **stoppa** , försök att stoppa tjänsten:

        sc stop DHCP
2. Isolera tjänsten i en egen "svchost"-behållare:

        sc config DHCP type= own
3. Starta tjänsten:

        sc start DHCP
4. [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om tjänsten fortfarande inte startar.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disken till en virtuell dator för återställning](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn. Kontrol lera att den anslutna disken är flaggad som **online** i disk hanterings konsolen. Anteckna enhets beteckningen som är kopplad till den anslutna OS-disken.
3.  Öppna en upphöjd kommando tolks instans (**Kör som administratör**). Kör sedan följande skript. Det här skriptet förutsätter att den enhets beteckning som är kopplad till den anslutna OS-disken är **F**. Ersätt bokstaven efter behov med värdet i den virtuella datorn.

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

4. [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md). Kontrol lera sedan om problemet är löst.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.