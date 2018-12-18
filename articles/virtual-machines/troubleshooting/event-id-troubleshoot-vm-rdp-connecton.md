---
title: Felsöka Azure VM RDP-anslutningsproblem efter händelse-ID | Microsoft Docs
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
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959690"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Felsöka Azure VM RDP-anslutningsfel efter händelse-ID 

Den här artikeln förklarar hur du använder händelse-ID för att felsöka problem som hindrar en Remote Desktop protocol (RDP)-anslutning till en Azure virtuell dator (VM).

## <a name="symptoms"></a>Symtom

Du försöker använda en fjärrskrivbordssession (RDP)-protokollet för att ansluta till en Azure-dator. När du anger dina autentiseringsuppgifter om anslutningen misslyckas och du får följande felmeddelande visas:

**Den här datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen, om problemet kvarstår, kontakta ägaren till fjärrdatorn eller nätverksadministratören.**

Granska händelseloggarna på den virtuella datorn för att felsöka problemet, och sedan referera till följande scenarier.

## <a name="before-you-troubleshoot"></a>Innan du felsöka

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicksbild

Om du vill skapa en ögonblicksbild, följer du stegen i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Fjärransluta till den virtuella datorn

För att ansluta till den virtuella datorn via en fjärranslutning, kan du använda någon av metoderna i [så Använd verktyg för att felsöka problem med Azure Virtuella](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Händelseloggar

Kör följande kommandon för att kontrollera om händelse 1058 eller händelse 1057 loggas i systemloggen under de senaste 24 timmarna i en CMD-instans:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:** System <br />
**Källa:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tid* <br />
**Händelse-ID:** 1058 <br />
**Aktivitetskategori:** None <br />
**Nivå:** fel <br />
**Nyckelord:** klassiska <br />
**Användare:** saknas <br />
**Dator:** *dator* <br />
**Beskrivning:** värdserver för fjärrskrivbordssession har det gick inte att ersätta det utgångna självsignerat certifikat som används för autentisering av värdserver för fjärrskrivbordssession i SSL-anslutningar. Statuskoden var åtkomst nekas.

**Loggnamn:** System <br />
**Källa:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tid* <br />
**Händelse-ID:** 1058 <br />
**Aktivitetskategori:** None <br />
**Nivå:** fel <br />
**Nyckelord:** klassiska <br />
**Användare:** saknas <br />
**Dator:** *dator* <br />
**Beskrivning:** värdserver för fjärrskrivbordssessioner kunde inte skapa ett nytt självsignerat certifikat som ska användas för serverautentisering för RD Session host i SSL-anslutningar, statuskod: var objektet finns redan.

**Loggnamn:** System <br />
**Källa:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tid* <br />
**Händelse-ID:** 1057 <br />
**Aktivitetskategori:** None <br />
**Nivå:** fel <br />
**Nyckelord:** klassiska <br />
**Användare:** saknas <br />
**Dator:** *dator* <br />
**Beskrivning:** värdserver för fjärrskrivbordssessioner kunde inte skapa ett nytt självsignerat certifikat som ska användas för autentisering av värdserver för fjärrskrivbordssession på SSL-anslutningar. Statuskoden var Keyset inte finns

Du kan också leta efter SCHANNEL felhändelser 36872 och 36870 genom att köra följande kommandon:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:** System <br />
**Källa:** Schannel <br />
**Datum:** – <br />
**Händelse-ID:** 36870 <br />
**Aktivitetskategori:** None <br />
**Nivå:** fel <br />
**Nyckelord:**       <br />
**Användare:** SYSTEM <br />
**Dator:** *dator* <br />
**Beskrivning:** ett allvarligt fel uppstod vid försök att komma åt den privata nyckeln för SSL server autentiseringsuppgifter. Felkoden som returnerades från den kryptografiska modulen är 0x8009030D.  <br />
Internt fel-tillståndet är 10001.

### <a name="cause"></a>Orsak
Det här problemet uppstår eftersom lokal RSA krypteringsnycklarna i mappen MachineKeys på den virtuella datorn inte är tillgänglig. Felet kan bero på något av följande orsaker:

1. Fel behörigheter konfigurationen på mappen Machinekeys eller RSA-filer.

2. RSA-nyckel är skadad eller saknas.

### <a name="resolution"></a>Lösning

Du måste ställa in behörighet på RDP-certifikatet med hjälp av de här stegen för att felsöka problemet.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Ge behörighet till mappen MachineKeys

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

2.  Kör skriptet att återställa behörigheterna för MachineKey-mapp och återställa RSA-filer till standardvärdena.

3.  Försök att komma åt den virtuella datorn igen.

När skriptet har körts kan du kontrollera följande filer som har problem med behörigheter:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Förnya självsignerade certifikat för RDP

Om problemet kvarstår kör du följande skript för att se till att RDP självsignerade certifikatet förnyas:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Om du inte förnyar certifikatet, Följ dessa steg om du vill försöka ta bort certifikatet:

1. På en annan virtuell dator i samma virtuella nätverk, öppnar den **kör** skriver **mmc**, och tryck sedan på **OK**. 

2. På den **filen** menyn och välj **Lägg till/ta bort snapin-modulen**.

3. I den **tillgängliga snapin-moduler** väljer **certifikat**, och välj sedan **Lägg till**.

4. Välj **datorkontot**, och välj sedan **nästa**.

5. Välj **en annan dator**, och Lägg sedan till IP-adressen för den virtuella datorn som har problem.
   >[!Note]
   >Försök att använda det interna nätverket för att undvika att använda en virtuell IP-adress.

6. Välj **Slutför**, och välj sedan **OK**.

   ![Välj dator](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expandera certifikat, gå till mappen Remote Desktop\Certificates, högerklicka på certifikatet och välj sedan **ta bort**.

8. Starta om tjänsten för konfiguration av fjärrskrivbord:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Nu om du uppdaterar store från mmc certifikatet på nytt. 

Försök att komma åt den virtuella datorn med RDP igen.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Uppdatera Secure Sockets Layer (SSL)-certifikat

Om du har konfigurerat den virtuella datorn att använda ett SSL-certifikat, kör du följande kommando för att hämta tumavtrycket. Kontrollera om det är samma som certifikatets tumavtryck:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Om det inte finns kan du ändra tumavtrycket:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Du kan även försöka att ta bort nyckeln så att RDP använder ett självsignerat certifikat för RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Händelseloggen

Kör följande kommandon för att kontrollera om SCHANNEL-felhändelse 36871 loggas i systemloggen under de senaste 24 timmarna i en CMD-instans:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:** System <br />
**Källa:** Schannel <br />
**Datum:** – <br />
**Händelse-ID:** 36871 <br />
**Aktivitetskategori:** None <br />
**Nivå:** fel <br />
**Nyckelord:**       <br />
**Användare:** SYSTEM <br />
**Dator:** *dator* <br />
**Beskrivning:** ett allvarligt fel inträffade när en autentiseringsuppgift för TLS-server. Internt fel-tillståndet är 10013.
 
### <a name="cause"></a>Orsak

Det här problemet orsakas av säkerhetsprinciper. När äldre versioner av TLS (till exempel 1.0) är inaktiverad, misslyckas för RDP-åtkomst.

### <a name="resolution"></a>Lösning

RDP använder TLS 1.0 som standardprotokoll. Men kan protokollet som ha ändrats till TLS 1.1, vilket är den nya standarden.

Om du vill felsöka det här problemet, se [Felsök autentiseringsfel när du använder RDP för att ansluta till virtuella Azure-datorn](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scenario 3

Om du har installerat den **Anslutningsutjämning för fjärrskrivbord** rollen på den virtuella datorn och kontrollera om det finns händelse 2056 eller händelse 1296 under de senaste 24 timmarna. Kör följande kommandon i en CMD-instans: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Loggnamn:** Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Källa:** Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:** *tid* <br />
**Händelse-ID:** 2056 <br />
**Aktivitetskategori:** (109) <br />
**Nivå:** fel <br />
**Nyckelord:**       <br />
**Användare:** NÄTVERKSTJÄNST <br />
**Dator:** *datorn fqdn* <br />
**Beskrivning:** går inte att hitta beskrivningen av händelse-ID 2056 från källan Microsoft-Windows-TerminalServices-SessionBroker. Antingen den komponent som genererar den här händelsen är inte installerad på den lokala datorn eller installationen är skadad. Du kan installera eller reparera komponenten på den lokala datorn. <br />
Om händelsen skapades på en annan dator, hade visningsinformationen sparas till händelsen. <br />
Följande information ingick i händelsen: <br />
NULL <br />
NULL <br />
Det gick inte att logga in på databasen.

**Loggnamn:** Microsoft-Windows-TerminalServices-SessionBroker-klient/Operational <br />
**Källa:** Microsoft-Windows-TerminalServices-SessionBroker-klient <br />
**Datum:** *tid* <br />
**Händelse-ID:** 1296 <br />
**Aktivitetskategori:** (104) <br />
**Nivå:** fel <br />
**Nyckelord:**       <br />
**Användare:** NÄTVERKSTJÄNST <br />
**Dator:** *datorn fqdn* <br />
**Beskrivning:** går inte att hitta beskrivningen av händelse-ID 1296 från källan Microsoft-Windows-TerminalServices-SessionBroker-klient. Antingen den komponent som genererar den här händelsen är inte installerad på den lokala datorn eller installationen är skadad. Du kan installera eller reparera komponenten på den lokala datorn.
Om händelsen skapades på en annan dator, hade visningsinformationen sparas till händelsen.
Följande information ingick i händelsen:  <br />
*Text* <br />
*Text* <br />
Anslutningsutjämning för fjärrskrivbord är inte klar för RPC-kommunikation.

### <a name="cause"></a>Orsak

Det här problemet uppstår eftersom värdnamnet på servern för Anslutningsutjämning för fjärrskrivbord har ändrats, som inte är en ändring som stöds. 

Värdnamnet har poster och beroenden på Windows Internal Database, vilket krävs av Fjärrskrivbordstjänst servergruppen för att kunna fungera. Ändra värdnamnet när gruppen har redan skapats orsakar många fel och kan orsaka broker servern slutar att fungera.

### <a name="resolution"></a>Lösning 

Rollen Anslutningsutjämning för fjärrskrivbord och Windows Internal Database måste installeras för att åtgärda problemet.

## <a name="next-steps"></a>Nästa steg

[Schannel-händelser](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Teknisk översikt av SSP med säker KANAL](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP misslyckas med händelse-ID 1058 & händelse 36870 med fjärrskrivbordssession Värdcertifikatet & SSL-kommunikation](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 eller Schannel 36870 på en domänkontrollant](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Händelse-ID 1058 – Fjärrskrivbordstjänster autentisering och kryptering](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

