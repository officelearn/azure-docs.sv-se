---
title: Felsöka anslutningsproblem för Azure VM-fjärrskrivbord efter händelse-ID | Microsoft-dokument
description: Använd händelse-ID:n för att felsöka olika problem som förhindrar en RDP-anslutning (Remote Desktop Protocol) till en virtuell virtuell dator (Virtuell dator).
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
ms.openlocfilehash: 2073d5f91b26cd2ae53e3291a6d1dad4d711b66d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437066"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Felsöka Azure VM RDP-anslutningsfel efter händelse-ID 

I den här artikeln beskrivs hur du använder händelse-ID:n för att felsöka problem som förhindrar en RDP-anslutning (Remote Desktop Protocol) till en virtuell virtuell dator (Virtuell dator) för fjärrskrivbord.

## <a name="symptoms"></a>Symtom

Du försöker använda en RDP-session (Remote Desktop Protocol) för att ansluta till en Virtuell Azure.You try to use a Remote Desktop Protocol (RDP) session to connect to an Azure VM. När du har angett dina autentiseringsuppgifter misslyckas anslutningen och följande felmeddelande visas:

**Den här datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen, om problemet kvarstår, kontakta fjärrdatorns ägare eller nätverksadministratören.**

Om du vill felsöka problemet granskar du händelseloggarna på den virtuella datorn och refererar sedan till följande scenarier.

## <a name="before-you-troubleshoot"></a>Innan du felsöker

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicksbild av säkerhetskopian

Om du vill skapa en ögonblicksbild av säkerhetskopian följer du stegen i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ansluta till den virtuella datorn på distans

Om du vill ansluta till den virtuella datorn på distans använder du någon av metoderna i [Hur du använder fjärrverktyg för att felsöka Azure VM-problem](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Händelseloggar

I en CMD-instans kör du följande kommandon för att kontrollera om händelse 1058 eller händelse 1057 är inloggad i systemloggen inom de senaste 24 timmarna:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:**      System <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:** 1058 <br />
**Uppgiftskategori:** Ingen <br />
**Nivå:**         Fel <br />
**Nyckelord:**      Klassiska <br />
**Användare:**          Ej mycket <br />
**Dator:**      *dator* <br />
**Beskrivning:** Värdservern för fjärrskrivbordssessionen kunde inte ersätta det självsignerade certifikat som har upphört att gälla för värdserver för fjärrskrivbordssession på TLS-anslutningar. Relevant statuskod nekades Access.

**Loggnamn:**      System <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:** 1058 <br />
**Uppgiftskategori:** Ingen <br />
**Nivå:**         Fel <br />
**Nyckelord:**      Klassiska <br />
**Användare:**          Ej mycket <br />
**Dator:**      *dator* <br />
**Beskrivning:** Värdservern för fjärrskrivbordssessionen har inte kunnat skapa ett nytt självsignerat certifikat som ska användas för värdserverautentisering för fjärrskrivbordssession på TLS-anslutningar, det finns redan relevant statuskod.

**Loggnamn:**      System <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:** 1057 <br />
**Uppgiftskategori:** Ingen <br />
**Nivå:**         Fel <br />
**Nyckelord:**      Klassiska <br />
**Användare:**          Ej mycket <br />
**Dator:**      *dator* <br />
**Beskrivning:** Värdservern för fjärrskrivbordssessionen kunde inte skapa ett nytt självsignerat certifikat som ska användas för värdserverautentisering för fjärrskrivbordssession på TLS-anslutningar. Relevant statuskod var Keyset finns inte

Du kan också söka efter SCHANNEL-felhändelserna 36872 och 36870 genom att köra följande kommandon:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:**      System <br />
**Källa:**        Schannel <br />
**Datum:** — <br />
**Händelse-ID:** 36870 <br />
**Uppgiftskategori:** Ingen <br />
**Nivå:**         Fel <br />
**Sökord:**       <br />
**Användare:**          System <br />
**Dator:**      *dator* <br />
**Beskrivning:** Ett allvarligt fel uppstod när du försökte komma åt den privata nyckeln för TLS-servern. Felkoden som returneras från den kryptografiska modulen är 0x8009030D.  <br />
Det interna feltillståndet är 10001.

### <a name="cause"></a>Orsak
Det hÃ¤r problemet uppstÃ¥r pÃ¥ grund av att de lokala RSA-krypteringsnycklarna i mappen MachineKeys pÃ¥ den virtuella datorn inte kan kommas åt. Det här problemet kan uppstå av något av följande skäl:

1. Felaktig behörighetskonfiguration på mappen Machinekeys eller RSA-filerna.

2. Skadad eller saknad RSA-nyckel.

### <a name="resolution"></a>Lösning

Om du vill felsöka problemet måste du ställa in rätt behörigheter för RDP-certifikatet med hjälp av dessa steg.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Bevilja behörighet till mappen MachineKeys

1. Skapa ett skript med hjälp av följande innehåll:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Kör skriptet om du vill återställa behörigheterna för MachineKey-mappen och återställa RSA-filerna till standardvärdena.

3.  Försök komma åt den virtuella datorn igen.

När du har kört skriptet kan du kontrollera följande filer som har behörighetsproblem:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Förnya RDP:s självsignerade certifikat

Om problemet kvarstår kör du följande skript för att kontrollera att rdp-självsignerade certifikatet förnyas:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Om du inte kan förnya certifikatet gör du så här för att försöka ta bort certifikatet:

1. Öppna rutan **Kör** på en annan virtuell dator i samma virtuella nätverk, skriv **mmc**och tryck sedan på **OK**. 

2. Välj **Lägg till/ta bort snapin-modulen på Arkiv-menyn**. **File**

3. I listan **Tillgängliga snapin-moduler** väljer du **Certifikat**och väljer sedan **Lägg till**.

4. Välj **Datorkonto**och välj sedan **Nästa**.

5. Välj **En annan dator**och lägg sedan till IP-adressen för den virtuella datorn som har problem.
   >[!Note]
   >Försök att använda det interna nätverket för att undvika att använda en virtuell IP-adress.

6. Välj **Slutför**och välj sedan **OK**.

   ![Välj dator](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expandera certifikaten, gå till mappen Fjärrskrivbord\Certifikat, högerklicka på certifikatet och välj sedan **Ta bort**.

8. Starta om konfigurationstjänsten för fjärrskrivbord:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Om du uppdaterar arkivet från MMC visas certifikatet igen. 

Försök komma åt den virtuella datorn med hjälp av RDP igen.

#### <a name="update-tlsssl-certificate"></a>Uppdatera TLS/SSL-certifikat

Om du konfigurerar den virtuella datorn så att det använder ett TLS/SSL-certifikat kör du följande kommando för att hämta tumavtrycket. Kontrollera sedan om det är samma som certifikatets tumavtryck:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Om så inte är fallet ändrar du tumavtrycket:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Du kan också försöka ta bort nyckeln så att RDP använder det självsignerade certifikatet för RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Händelseloggen

I en CMD-instans kör du följande kommandon för att kontrollera om SCHANNEL-felhändelsen 36871 är inloggad i systemloggen inom de senaste 24 timmarna:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:**      System <br />
**Källa:**        Schannel <br />
**Datum:** — <br />
**Händelse-ID:** 36871 <br />
**Uppgiftskategori:** Ingen <br />
**Nivå:**         Fel <br />
**Sökord:**       <br />
**Användare:**          System <br />
**Dator:**      *dator* <br />
**Beskrivning:** Ett allvarligt fel uppstod när en TLS-serverautentiseringsautentisering skulle skapas. Det interna feltillståndet är 10013.
 
### <a name="cause"></a>Orsak

Det här problemet orsakas av säkerhetsprinciper. När äldre versioner av TLS (till exempel 1.0) är inaktiverade misslyckas RDP-åtkomsten.

### <a name="resolution"></a>Lösning

RDP använder TLS 1.0 som standardprotokoll. Protokollet kan dock ändras till TLS 1.1, vilket är den nya standarden.

Information om felsökning av problemet finns [i Felsöka autentiseringsfel när du använder RDP för att ansluta till Azure VM](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scenario 3

Om du har installerat rollen **Anslutningsutjämning för fjärrskrivbord** på den virtuella datorn kontrollerar du om det finns händelse 2056 eller händelse 1296 under de senaste 24 timmarna. I en CMD-instans kör du följande kommandon: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:**      Microsoft-Windows-TerminalServices-SessionBroker/Drift <br />
**Källa:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *tid* <br />
**Händelse-ID:** 2056 <br />
**Uppgiftskategori:** (109) <br />
**Nivå:**         Fel <br />
**Sökord:**       <br />
**Användare:**          NÄTTJÄNST <br />
**Dator:**      *dator fqdn* <br />
**Beskrivning:** Det går inte att hitta beskrivningen för händelse-ID 2056 från källan Microsoft-Windows-TerminalServices-SessionBroker. Antingen är komponenten som väcker den här händelsen inte installerad på den lokala datorn eller så är installationen skadad. Du kan installera eller reparera komponenten på den lokala datorn. <br />
Om händelsen har sitt ursprung på en annan dator måste visningsinformationen sparas med händelsen. <br />
Följande information ingick i händelsen: <br />
NULL <br />
NULL <br />
Inloggningen till databasen misslyckades.

**Loggnamn:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Källa:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:**          *tid* <br />
**Händelse-ID:** 1296 <br />
**Uppgiftskategori:** (104) <br />
**Nivå:**         Fel <br />
**Sökord:**       <br />
**Användare:**          NÄTTJÄNST <br />
**Dator:**      *dator fqdn* <br />
**Beskrivning:** Det går inte att hitta beskrivningen för händelse-ID 1296 från källan Microsoft-Windows-TerminalServices-SessionBroker-Client. Antingen är komponenten som väcker den här händelsen inte installerad på den lokala datorn eller så är installationen skadad. Du kan installera eller reparera komponenten på den lokala datorn.
Om händelsen har sitt ursprung på en annan dator måste visningsinformationen sparas med händelsen.
Följande information ingick i händelsen:  <br />
*text* <br />
*text* <br />
Anslutningsutjämning för fjärrskrivbord är inte redo för RPC-kommunikation.

### <a name="cause"></a>Orsak

Det här problemet beror på att värdnamnet för servern för anslutningsutjämning för fjärrskrivbord ändras, vilket inte är en ändring som stöds. 

Värdnamnet har poster och beroenden i Windows interna databas, vilket krävs av Remote Desktop Service-servergruppen för att kunna fungera. Om du ändrar värdnamnet efter att servergruppen redan har skapats orsakas många fel och det kan leda till att mäklarservern slutar fungera.

### <a name="resolution"></a>Lösning 

För att åtgärda problemet måste rollen Anslutningsutjämning för fjärrskrivbord och den interna windowsdatabasen installeras om.

## <a name="next-steps"></a>Efterföljande moment

[Schannel Händelser](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Teknisk översikt av SSP med säker kanal](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP misslyckas med händelse-ID 1058 & Händelse 36870 med värdcertifikat för fjärrskrivbordssession & SSL-kommunikation](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 eller Schannel 36870 på en domänkontrollant](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Händelse-ID 1058 – Autentisering och kryptering av fjärrskrivbordstjänster](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

