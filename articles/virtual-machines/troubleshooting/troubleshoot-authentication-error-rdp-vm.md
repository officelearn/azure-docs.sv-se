---
title: Felsöka autentiseringsfel när du använder RDP för att ansluta till Azure VM | Microsoft-dokument
description: Lär dig hur du felsöker autentiseringsfel som uppstår när du använder RDP (Remote Desktop Protocol) för att ansluta till en virtuell Azure-dator (VM).
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 03356c0b4a93f4befdbc529523e58642137a8887
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420819"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Felsöka autentiseringsfel när du använder RDP för att ansluta till virtuella Azure-datorer

Den här artikeln kan hjälpa dig att felsöka autentiseringsfel som uppstår när du använder RDP-anslutning (Remote Desktop Protocol) för att ansluta till en virtuell virtuell azure-dator (VM).

## <a name="symptoms"></a>Symtom

Du tar en skärmbild av en Virtuell Azure-dator som visar välkomstskärmen och anger att operativsystemet körs. Men när du försöker ansluta till den virtuella datorn med hjälp av Anslutning till fjärrskrivbord visas något av följande felmeddelanden.

### <a name="error-message-1"></a>Felmeddelande 1

**Ett autentiseringsfel har uppstått. Den lokala säkerhetsmyndigheten kan inte kontaktas.**

### <a name="error-message-2"></a>Felmeddelande 2

**Fjärrdatorn som du försöker ansluta till kräver NLA (Network Level Authentication), men windows-domänkontrollanten kan inte kontaktas för att utföra NLA. Om du är administratör på fjärrdatorn kan du inaktivera NLA genom att använda alternativen på fliken Fjärr i dialogrutan Systemegenskaper.**

### <a name="error-message-3-generic-connection-error"></a>Felmeddelande 3 (allmänt anslutningsfel)

**Den här datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen, om problemet kvarstår, kontakta fjärrdatorns ägare eller nätverksadministratören.**

## <a name="cause"></a>Orsak

Det finns flera orsaker till att NLA kan blockera RDP-åtkomsten till en virtuell dator.

### <a name="cause-1"></a>Orsak 1

Den virtuella datorn kan inte kommunicera med domänkontrollanten (DC). Det här problemet kan förhindra att en RDP-session kommer åt en virtuell dator med hjälp av domänautentiseringsuppgifter. Du kan dock fortfarande logga in med autentiseringsuppgifterna för den lokala administratören. Det här problemet kan uppstå i följande situationer:

1. Active Directory-säkerhetskanalen mellan den här virtuella datorn och domänkontrollanten är bruten.

2. Den virtuella datorn har en gammal kopia av kontolösenordet och domänkontrollanten har en nyare kopia.

3. Den domänkontrollant som den här virtuella datorn ansluter till är fel.

### <a name="cause-2"></a>Orsak 2

Krypteringsnivån för den virtuella datorn är högre än den som används av klientdatorn.

### <a name="cause-3"></a>Orsak 3

TLS 1.0-, 1.1- eller 1.2-protokollen (server) är inaktiverade på den virtuella datorn.

### <a name="cause-4"></a>Orsak 4

Den virtuella datorn har konfigurerats för att inaktivera inloggning med hjälp av domänautentiseringsuppgifter och LSA (Local Security Authority) har ställts in felaktigt.

### <a name="cause-5"></a>Orsak 5

Den virtuella datorn har konfigurerats för att endast acceptera FIPS-kompatibla algoritmanslutningar (Federal Information Processing Standard). Detta görs vanligtvis med hjälp av Active Directory-principen. Detta är en sällsynt konfiguration, men FIPS kan endast tillämpas för fjärrskrivbordsanslutningar.

## <a name="before-you-troubleshoot"></a>Innan du felsöker

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicksbild av säkerhetskopian

Om du vill skapa en ögonblicksbild av säkerhetskopian följer du stegen i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ansluta till den virtuella datorn på distans

Om du vill ansluta till den virtuella datorn på distans använder du någon av metoderna i [Hur du använder fjärrverktyg för att felsöka Azure VM-problem](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Klienttjänst för grupprincip

Om det här är en domänansluten virtuell dator stoppar du först tjänsten Grupprincipklient för att förhindra att Active Directory-principen skriver över ändringarna. Gör detta genom att köra följande kommando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

När problemet har åtgärdats återställer du möjligheten för den här virtuella datorn att kontakta domänen för att hämta det senaste gpo-värdet från domänen. Det gör du genom att köra följande kommandon:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Om ändringen återställs betyder det att en Active Directory-princip orsakar problemet. 

### <a name="workaround"></a>Lösning

Du kan lösa problemet genom att köra följande kommandon i kommandofönstret för att inaktivera NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Starta sedan om den virtuella datorn.

Om du vill aktivera NLA igen kör du följande kommando och startar sedan om den virtuella datorn:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Felsökning

### <a name="for-domain-joined-vms"></a>För domänanslutna virtuella datorer

Om du vill felsöka problemet kontrollerar du först om den virtuella datorn kan ansluta till en domänkontrollant och om domänkontrollanten har statusen "felfri" och kan hantera begäranden från den virtuella datorn.

>[!Note] 
>Om du vill testa dc-hälsotillståndet kan du använda en annan virtuell dator på samma virtuella nätverk och undernät som delar samma inloggningsserver.

Anslut till den virtuella datorn som har problemet med seriekonsol, fjärr-CMD eller fjärr-PowerShell, enligt stegen i avsnittet "Anslut till den virtuella datorn på distans".

Ta reda på vilken domänkontrollant den virtuella datorn ansluter till genom att köra följande kommando i konsolen: 

```cmd
set | find /i "LOGONSERVER"
```

Kontrollera sedan hälsotillståndet för den säkra kanalen mellan den virtuella datorn och domänkontrollanten. Det gör du genom att köra följande kommando i en upphöjd PowerShell-instans. Det här kommandot returnerar en boolesk flagga som anger om den säkra kanalen är vid liv:

```powershell
Test-ComputerSecureChannel -verbose
```

Om kanalen är bruten kör du följande kommando för att reparera den:

```powershell
Test-ComputerSecureChannel -repair
```

Kontrollera att lösenordet för datorkontot i Active Directory uppdateras på den virtuella datorn och domänkontrollanten:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Om kommunikationen mellan domänkontrollanten och den virtuella datorn är bra, men domänkontrollanten inte är tillräckligt felfri för att öppna en RDP-session, kan du försöka starta om domänkontrollanten.

Om de föregående kommandona inte åtgärdade kommunikationsproblemet till domänen kan du återansluta den här virtuella datorn till domänen. Det gör du genom att följa dessa steg:

1. Skapa ett skript med namnet Unjoin.ps1 med hjälp av följande innehåll och distribuera sedan skriptet som ett anpassat skripttillägg på Azure-portalen:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Det här skriptet tar den virtuella datorn ut ur domänen med våld och startar om den 10 sekunder senare. Sedan måste du rensa datorn objektet på domänsidan.

2.  När rensningen är klar kan du återansluta den här virtuella datorn till domänen. Det gör du genom att skapa ett skript med namnet JoinDomain.ps1 med hjälp av följande innehåll och distribuera sedan skriptet som ett anpassat skripttillägg på Azure-portalen: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Detta ansluter den virtuella datorn på domänen med hjälp av de angivna autentiseringsuppgifterna.

Om Active Directory-kanalen är felfri uppdateras datorns lösenord och domänkontrollanten fungerar som förväntat provar du följande steg.

Om problemet kvarstår kontrollerar du om domänautentiseringsuppgifterna är inaktiverade. Det gör du genom att öppna ett upphöjt kommandotolksfönster och sedan köra följande kommando för att avgöra om den virtuella datorn är konfigurerad för att inaktivera domänkonton för inloggning till den virtuella datorn:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Om nyckeln är inställd på **1**innebär det att servern har konfigurerats för att inte tillåta domänautentiseringsuppgifter. Ändra den här nyckeln till **0**.

### <a name="for-standalone-vms"></a>För fristående virtuella datorer

#### <a name="check-minencryptionlevel"></a>Kontrollera MinEncryptionLevel

I en CMD-instans kör du följande kommando för att fråga registervärdet **MinEncryptionLevel:**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Gör så här baserat på registervärdet:

* 4 (FIPS): Gå till [Kontrollera FIPs-kompatibla algoritmer anslutningar](#fips-compliant).

* 3 (128-bitars kryptering): Ställ in allvarlighetsgraden på **2** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Högsta möjliga kryptering, enligt klientens dikterat): Du kan försöka ställa in krypteringen till minimivärdet **1** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Starta om den virtuella datorn så att ändringarna i registret börjar gälla.

#### <a name="tls-version"></a>TLS-version

Beroende på systemet använder RDP TLS 1.0, 1.1 eller 1.2 (server)-protokollet. Om du vill fråga hur dessa protokoll har konfigurerats på den virtuella datorn öppnar du en CMD-instans och kör sedan följande kommandon:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Om de returnerade värdena inte är **alla 1**betyder det att protokollet är inaktiverat. Om du vill aktivera dessa protokoll kör du följande kommandon:

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
> Hämta SSH/TLS version x.x från Gäst-OS-loggarna på SCHANNEL-felen.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>Kontrollera FIPs-kompatibla algoritmer anslutningar

Fjärrskrivbord kan endast användas för FIPs-kompatibla algoritmanslutningar. Detta kan ställas in med hjälp av en registernyckel. Det gör du genom att öppna ett upphöjt kommandotolksfönster och sedan fråga följande tangenter:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Om kommandot returnerar **1**ändrar du registervärdet till **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Kontrollera vilken som är den aktuella MinEncryptionLevel på den virtuella datorn:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Om kommandot returnerar **4**ändrar du registervärdet till **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Starta om den virtuella datorn så att ändringarna i registret börjar gälla.

## <a name="next-steps"></a>Nästa steg

[SetEncryptionLevel-metoden för klassen Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurera serverautentiserings- och krypteringsnivåer](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting klass](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
