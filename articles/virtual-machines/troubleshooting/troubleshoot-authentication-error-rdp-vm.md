---
title: Felsöka autentiseringsfel när du använder RDP för att ansluta till den virtuella Azure-datorn | Microsoft Docs
description: Lär dig hur du felsöker autentiseringsfel som inträffar när du använder Remote Desktop Protocol (RDP) för att ansluta till en virtuell Azure-dator (VM).
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
ms.openlocfilehash: cc1ad3104596cc7ad4bb48f88e4c8312bc833371
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088571"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Felsöka autentiseringsfel när du använder RDP för att ansluta till virtuella Azure-datorer

Den här artikeln kan hjälpa dig att felsöka autentiseringsfel som inträffar när du använder Remote Desktop Protocol-anslutning (RDP) för att ansluta till en virtuell Azure-dator (VM).

## <a name="symptoms"></a>Symtom

Du fångar en skärm bild av en virtuell Azure-dator som visar välkomst skärmen och indikerar att operativ systemet körs. Men när du försöker ansluta till den virtuella datorn med hjälp av Anslutning till fjärrskrivbord får du ett av följande fel meddelanden.

### <a name="error-message-1"></a>Fel meddelande 1

**Ett autentiseringsfel har inträffat. Det går inte att kontakta den lokala säkerhets kontrollen.**

### <a name="error-message-2"></a>Fel meddelande 2

**Den fjärrdator som du försöker ansluta till kräver autentisering på nätverksnivå (NLA), men det går inte att kontakta Windows-domänkontrollanten för att utföra NLA. Om du är administratör på fjärrdatorn kan du inaktivera NLA genom att använda alternativen på fliken Fjärr i dialog rutan system egenskaper.**

### <a name="error-message-3-generic-connection-error"></a>Fel meddelande 3 (allmänt anslutnings fel)

**Datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen. om problemet kvarstår kan du kontakta ägaren till fjärrdatorn eller nätverks administratören.**

## <a name="cause"></a>Orsak

Det finns flera orsaker till att NLA kan blockera RDP-åtkomsten till en virtuell dator.

### <a name="cause-1"></a>Orsak 1

Den virtuella datorn kan inte kommunicera med domänkontrollanten (DC). Det här problemet kan förhindra att en RDP-session får åtkomst till en virtuell dator med hjälp av domänautentiseringsuppgifter. Men du kan fortfarande logga in med hjälp av autentiseringsuppgifterna för lokal administratör. Det här problemet kan uppstå i följande situationer:

1. Active Directory säkerhets kanal mellan den här virtuella datorn och DOMÄNKONTROLLANTen är bruten.

2. Den virtuella datorn har en gammal kopia av konto lösen ordet och DOMÄNKONTROLLANTen har en nyare kopia.

3. Den DOMÄNKONTROLLANT som den här virtuella datorn ansluter till är inte felfri.

### <a name="cause-2"></a>Orsak 2

Den virtuella datorns krypterings nivå är högre än den som används av klient datorn.

### <a name="cause-3"></a>Orsak 3

TLS 1,0-, 1,1-eller 1,2-protokollen (Server) är inaktiverade på den virtuella datorn.

### <a name="cause-4"></a>Orsak 4

Den virtuella datorn har kon figurer ATS för att inaktivera inloggning genom att använda domänautentiseringsuppgifter och den lokala säkerhets kontrollen (LSA) har kon figurer ATS felaktigt.

### <a name="cause-5"></a>Orsak 5

Den virtuella datorn har kon figurer ATS för att endast godkänna anslutningar för Federal Information Processing Standard (FIPS)-kompatibla algoritmer. Detta görs vanligt vis genom att använda Active Directory princip. Detta är en sällsynt konfiguration, men FIPS kan endast tillämpas för fjärr skrivbords anslutningar.

## <a name="before-you-troubleshoot"></a>Innan du felsöker

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicks bild av säkerhets kopia

Om du vill skapa en ögonblicks bild av säkerhets kopia följer du stegen i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Fjärrans luta till den virtuella datorn

Om du vill ansluta till den virtuella datorn via fjärr anslutning använder du någon av metoderna i [hur du använder fjärrverktyg för att felsöka problem med virtuella Azure-datorer](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Klient tjänst för grup princip

Om det här är en domänansluten virtuell dator stoppar du först grupprincip-klient tjänsten för att förhindra att någon Active Directorys princip skriver över ändringarna. Gör detta genom att köra följande kommando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

När problemet har åtgärd ATS återställer du möjligheten för den här virtuella datorn att kontakta domänen för att hämta det senaste GRUPPRINCIPOBJEKTet från domänen. Det gör du genom att köra följande kommandon:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Om ändringen återställs innebär det att en Active Directory princip orsakar problemet. 

### <a name="workaround"></a>Lösning

Undvik det här problemet genom att köra följande kommandon i kommando fönstret för att inaktivera NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Starta sedan om den virtuella datorn.

Om du vill återaktivera NLA kör du följande kommando och startar sedan om den virtuella datorn:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Felsökning

### <a name="for-domain-joined-vms"></a>För domänanslutna virtuella datorer

Om du vill felsöka det här problemet måste du först kontrol lera om den virtuella datorn kan ansluta till en DOMÄNKONTROLLANT och om DOMÄNKONTROLLANTen har statusen "felfri" och kan hantera begär Anden från den virtuella datorn.

>[!Note] 
>Om du vill testa DC-hälso tillståndet kan du använda en annan virtuell dator i samma VNET och undernät som delar samma inloggnings Server.

Anslut till den virtuella datorn som har problemet genom att använda Seriell konsol, fjärr-CMD eller fjärr-PowerShell, enligt stegen i avsnittet "ansluta till den virtuella datorn via fjärr anslutning".

För att avgöra vilken DOMÄNKONTROLLANT som den virtuella datorn ansluter till kör du följande kommando i-konsolen: 

```cmd
set | find /i "LOGONSERVER"
```

Kontrol lera sedan hälsan för den säkra kanalen mellan den virtuella datorn och DOMÄNKONTROLLANTen. Det gör du genom att köra följande kommando i en upphöjd PowerShell-instans. Det här kommandot returnerar en boolesk flagga som anger om den säkra kanalen är aktiv:

```powershell
Test-ComputerSecureChannel -verbose
```

Om kanalen är bruten kör du följande kommando för att reparera den:

```powershell
Test-ComputerSecureChannel -repair
```

Kontrol lera att lösen ordet för dator kontot i Active Directory uppdateras på den virtuella datorn och DOMÄNKONTROLLANTen:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Om kommunikationen mellan DOMÄNKONTROLLANTen och den virtuella datorn är god, men DOMÄNKONTROLLANTen inte är tillräckligt felfri för att öppna en RDP-session, kan du försöka starta om DOMÄNKONTROLLANTen.

Om föregående kommandon inte löste kommunikations problemet till domänen kan du ansluta den till domänen igen. Det gör du genom att följa dessa steg:

1. Skapa ett skript som heter Unjoin.ps1 med hjälp av följande innehåll och distribuera skriptet som ett anpassat skript tillägg på Azure Portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Det här skriptet tar den virtuella datorn från domänen tvång och startar om den 10 sekunder senare. Sedan måste du rensa dator-objektet på domän sidan.

2.  När rensningen är färdig ansluter du den här virtuella datorn till domänen. Det gör du genom att skapa ett skript som heter JoinDomain.ps1 med hjälp av följande innehåll och sedan distribuera skriptet som ett anpassat skript tillägg på Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Detta ansluter den virtuella datorn till domänen med hjälp av de angivna autentiseringsuppgifterna.

Om Active Directory-kanalen är felfri uppdateras datorns lösen ord och domänkontrollanten fungerar som förväntat, prova följande steg.

Om problemet kvarstår kontrollerar du om domän autentiseringsuppgiften är inaktive rad. Det gör du genom att öppna en kommando tolk med förhöjd behörighet och sedan köra följande kommando för att avgöra om den virtuella datorn har kon figurer ATS för att inaktivera domän konton för att logga in på den virtuella datorn:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Om nyckeln har angetts till **1**innebär det att servern har kon figurer ATS för att inte tillåta domänautentiseringsuppgifter. Ändra den här nyckeln till **0**.

### <a name="for-standalone-vms"></a>För fristående virtuella datorer

#### <a name="check-minencryptionlevel"></a>Kontrol lera MinEncryptionLevel

Kör följande kommando i en CMD-instans för att fråga efter registervärdet **MinEncryptionLevel** :

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Följ dessa steg baserat på registervärdet:

* 4 (FIPS): gå till [kontrol lera FIPS-kompatibla algoritmer-anslutningar](#fips-compliant).

* 3 (128-bitars kryptering): Ange allvarlighets grad till **2** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (högsta möjliga kryptering som styrs av klienten): du kan försöka att ställa in krypteringen på det lägsta värdet **1** genom att köra följande kommando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Starta om den virtuella datorn så att ändringarna i registret börjar gälla.

#### <a name="tls-version"></a>TLS-version

Beroende på systemet använder RDP protokollet TLS 1,0, 1,1 eller 1,2 (Server). Om du vill fråga hur dessa protokoll är inställda på den virtuella datorn öppnar du en CMD-instans och kör sedan följande kommandon:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Om de värden som returneras inte är **1**betyder det att protokollet är inaktiverat. Om du vill aktivera dessa protokoll kör du följande kommandon:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

För andra protokoll versioner kan du köra följande kommandon:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Hämta SSH/TLS-versionen x. x från gäst operativ system loggarna på SCHANNEL-felen.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>Kontrol lera anslutningar för FIPs-kompatibla algoritmer

Fjärr skrivbord kan tillämpas för att endast använda FIPs-kompatibla algoritm anslutningar. Detta kan anges med hjälp av en register nyckel. Det gör du genom att öppna ett kommando tolks fönster med förhöjd behörighet och sedan fråga följande nycklar:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Om kommandot returnerar **1**ändrar du registervärdet till **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Kontrol lera vilken som är den aktuella MinEncryptionLevel på den virtuella datorn:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Om kommandot returnerar **4**ändrar du registervärdet till **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Starta om den virtuella datorn så att ändringarna i registret börjar gälla.

## <a name="next-steps"></a>Nästa steg

[SetEncryptionLevel-metoden för Win32_TSGeneralSetting-klassen](/windows/desktop/termserv/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurera serverns autentiserings-och krypterings nivåer](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting klass](/windows/desktop/termserv/win32-tsgeneralsetting)
