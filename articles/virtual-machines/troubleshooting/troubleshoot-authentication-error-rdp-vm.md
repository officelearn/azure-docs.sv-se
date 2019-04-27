---
title: Felsök autentiseringsfel när du använder RDP för att ansluta till virtuell Azure-dator | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594929"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Felsök autentiseringsfel när du använder RDP för att ansluta till virtuella Azure-datorer

Den här artikeln kan hjälpa dig att felsöka autentiseringsfel som uppstår när du använder Remote Desktop Protocol (RDP)-anslutning för att ansluta till en Azure-dator (VM).

## <a name="symptoms"></a>Symtom

Du kan avbilda en skärmbild av en Azure-dator som visas på välkomstskärmen och indikerar att operativsystemet är igång. Men när du försöker ansluta till den virtuella datorn med hjälp av anslutning till fjärrskrivbord visas något av följande felmeddelanden.

### <a name="error-message-1"></a>Felmeddelande 1

**Ett autentiseringsfel inträffade. Det går inte att kontakta den lokala säkerhetskontrollen.**

### <a name="error-message-2"></a>Felmeddelande 2

**Den fjärrdator som du försöker ansluta till kräver autentisering på nätverksnivå (NLA), men det går inte att kontakta din Windows-domänkontrollant om du vill utföra NLA. Om du är administratör på fjärrdatorn kan inaktivera du NLA med hjälp av alternativen på fliken Fjärrsessioner i dialogrutan Systemegenskaper.**

### <a name="error-message-3-generic-connection-error"></a>Felmeddelande 3 (allmän anslutningsfel)

**Den här datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen, om problemet kvarstår, kontakta ägaren till fjärrdatorn eller nätverksadministratören.**

## <a name="cause"></a>Orsak

Det finns flera orsaker till varför NLA kan blockera RDP-åtkomst till en virtuell dator.

### <a name="cause-1"></a>Orsak 1

Den virtuella datorn inte kan kommunicera med domänkontrollanten (DC). Det här problemet kan förhindra att en RDP-session från att komma åt en virtuell dator med hjälp av autentiseringsuppgifter för domänen. Du kan dock fortfarande att kunna logga in med autentiseringsuppgifter för lokal administratör. Det här problemet kan inträffa i följande situationer:

1. Active Directory-säkerhetskanal mellan den här virtuella datorn och domänkontrollanten har brutits.

2. Den virtuella datorn har en gammal kopia av lösenordet för och domänkontrollanten har en nyare kopia.

3. Domänkontrollanten som ansluter till den här virtuella datorn är felfri.

### <a name="cause-2"></a>Orsak 2

Krypteringsnivå för den virtuella datorn är högre än det som används av datorn.

### <a name="cause-3"></a>Orsak 3

TLS 1.0, 1.1 eller 1.2 (server)-protokoll är inaktiverade på den virtuella datorn.

### <a name="cause-4"></a>Orsak 4

Den virtuella datorn har ställts in för att inaktivera loggning för med hjälp av autentiseringsuppgifter för domänen och Local Security Authority (LSA) är konfigurerad på rätt sätt.

### <a name="cause-5"></a>Orsak 5

Den virtuella datorn har ställts in för att acceptera endast FIPS Federal Information Processing Standard ()-kompatibel algoritm anslutningar. Detta görs normalt med hjälp av Grupprincip i Active Directory. Detta är en sällsynt konfiguration, men FIPS kan tillämpas för endast anslutningar till fjärrskrivbord.

## <a name="before-you-troubleshoot"></a>Innan du felsöka

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicksbild

Om du vill skapa en ögonblicksbild, följer du stegen i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Fjärransluta till den virtuella datorn

För att ansluta till den virtuella datorn via en fjärranslutning, kan du använda någon av metoderna i [så Använd verktyg för att felsöka problem med Azure Virtuella](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Tjänsten för klienten

Om det här är en domänansluten VM först stoppa tjänsten grupprincipklient för att förhindra att en Active Directory-princip skriver över ändringarna. Gör detta genom att köra följande kommando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

När problemet är löst, återställa möjligheten för den här virtuella datorn att kontakta domänen för att hämta senaste Grupprincipobjektet från domänen. Gör detta genom att köra följande kommandon:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Om ändringen återställs, innebär det att problemet orsakas av en princip för Active Directory. 

### <a name="workaround"></a>Lösning

Undvik det här problemet, kör du följande kommandon i kommandofönstret att inaktivera NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Starta sedan om den virtuella datorn.

Om du vill återaktivera NLA, kör du följande kommando och starta sedan om den virtuella datorn:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Felsökning

### <a name="for-domain-joined-vms"></a>För domänanslutna virtuella datorer

Om du vill felsöka problemet du först kontrollera om den virtuella datorn kan ansluta till en Domänkontrollant och om domänkontrollanten har statusen ”felfri” och kan hantera förfrågningar från den virtuella datorn.

>[!Note] 
>Om du vill testa DC-hälsotillstånd, kan du använda en annan virtuell dator på samma virtuella nätverk och undernät som delar samma inloggningsservern.

Anslut till den virtuella datorn som har problem med hjälp av Seriell konsol, remote CMD eller fjärr-PowerShell enligt anvisningarna i avsnittet ”ansluta till den virtuella datorn via en fjärranslutning”.

För att avgöra vilken Domänkontrollant som ansluter till den virtuella datorn, kör du följande kommando i konsolen: 

```cmd
set | find /i "LOGONSERVER"
```

Kontrollera hälsotillståndet för säker kanal mellan den virtuella datorn och domänkontrollanten. Gör detta genom att köra följande kommando i en upphöjd PowerShell-instans. Det här kommandot returnerar en boolesk flagga som anger om den säkra kanalen är igång:

```powershell
Test-ComputerSecureChannel -verbose
```

När kanalen har brutits, kör du följande kommando för att reparera den:

```powershell
Test-ComputerSecureChannel -repair
```

Kontrollera att lösenordet för datorkontot i Active Directory har uppdaterats på den virtuella datorn och domänkontrollanten:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Om kommunikationen mellan domänkontrollanten och den virtuella datorn är bra, men domänkontrollanten inte är felfri att öppna en RDP-session, kan du försöka starta om domänkontrollanten.

Om föregående kommandon inte löste problemet kommunikation till domänen, kan du återansluta till den här virtuella datorn till domänen. Det gör du genom att följa dessa steg:

1. Skapa ett skript som heter Unjoin.ps1 med följande innehåll och sedan distribuera skriptet som ett anpassat skripttillägg på Azure portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Det här skriptet tar den virtuella datorn från domänen tvång och startar om den 10 sekunder senare. Du måste sedan rensa datorobjekt på domän-sidan.

2.  När rensningen är klar kan du återansluta till den här virtuella datorn till domänen. Om du vill göra detta måste skapa ett skript som heter JoinDomain.ps1 med följande innehåll och distribuera skriptet som ett anpassat skripttillägg på Azure portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Detta kopplar den virtuella datorn på domänen med hjälp av de angivna autentiseringsuppgifterna.

Om Active Directory-kanalen är felfri, uppdateras lösenordet för datorn och domänkontrollanten fungerar som förväntat, kan du prova följande steg.

Om problemet kvarstår kontrollerar du om autentiseringsuppgifter för domänen är inaktiverad. Om du vill göra detta, öppna en upphöjd kommandotolk och kör sedan följande kommando för att avgöra om den virtuella datorn har ställts in att inaktivera domänkonton för att logga in på den virtuella datorn:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Om nyckeln är inställt på **1**, det innebär att servern har ställts in upp till att inte tillåta autentiseringsuppgifter för domänen. Ändra den här nyckeln till **0**.

### <a name="for-standalone-vms"></a>För fristående virtuella datorer

#### <a name="check-minencryptionlevel"></a>Kontrollera MinEncryptionLevel

I en CMD-instans, kör du följande kommando för att fråga den **MinEncryptionLevel** registervärdet:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Baserat på registervärdet, Följ dessa steg:

* 4 (FIPS): Gå till [Kontrollera FIPs-kompatibla algoritmer anslutningar](#fips-compliant).

* 3 (128-bitars kryptering): Ange allvarlighetsgraden **2** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (högsta kryptering möjligt, enligt klienten): Du kan försöka att ange krypteringen till det lägsta värdet på **1** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Starta om den virtuella datorn så att ändringar i registret gälla.

#### <a name="tls-version"></a>TLS-version

Beroende på systemet använder RDP TLS 1.0, 1.1 eller 1.2 (server)-protokollet. Om du vill fråga hur dessa protokoll har ställts in på den virtuella datorn, öppna en CMD-instans och kör sedan följande kommandon:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Om de returnerade värdena inte är alla **1**, det innebär att protokollet är inaktiverat. Om du vill aktivera dessa protokoll, kör du följande kommandon:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

För andra protokollversioner kan du köra följande kommandon:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Hämta SSH/TLS version x.x från loggarna för gästoperativsystemet på SCHANNEL-fel.

#### <a name="fips-compliant"></a> Kontrollera anslutningen för FIPs-kompatibla algoritmer

Fjärrskrivbord kan tillämpas för att använda endast FIPs-kompatibel algoritm anslutningar. Detta kan ställas in med hjälp av en registernyckel. Du gör detta, öppna en upphöjd kommandotolk och frågar sedan följande nycklar:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Om kommandot returnerar **1**, ändra registervärdet för att **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Kontrollera vilken är den aktuella MinEncryptionLevel på den virtuella datorn:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Om kommandot returnerar **4**, ändra registervärdet för att **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Starta om den virtuella datorn så att ändringar i registret gälla.

## <a name="next-steps"></a>Nästa steg

[SetEncryptionLevel-metoden i klassen Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurera Server-autentisering och krypteringsnivåer](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting-klass](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
