---
title: Felsöka en RDP-Allmänt fel till en Windows-dator i Azure | Microsoft Docs
description: Lär dig hur du felsöker ett allmänt fel RDP till en Windows-dator i Azure | Microsoft Docs
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 7f5e1f2141a58f666367d253d5fc313499e64c9f
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239398"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Felsöka en RDP-Allmänt fel i Azure VM

Den här artikeln beskriver ett allmänt fel som kan uppstå när du gör en Remote Desktop Protocol (RDP)-anslutning till en Windows virtuell dator (VM) i Azure.

## <a name="symptom"></a>Symtom

När du gör en RDP-anslutning i en Windows-dator i Azure, kan det hända att följande allmänt felmeddelande visas:

**Fjärrskrivbord kan inte ansluta till fjärrdatorn för något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktiverat**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrollera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst har aktiverats.**

## <a name="cause"></a>Orsak

Det här problemet kan inträffa på grund av följande orsaker:

### <a name="cause-1"></a>Orsak 1

RDP-komponenten har inaktiverats på följande sätt:

- På komponentnivå
- På nivån lyssnare
- På servern
- På rollen värd för fjärrskrivbordssession

### <a name="cause-2"></a>Orsak 2

Fjärrskrivbordstjänster (TermService) körs inte.

### <a name="cause-3"></a>Orsak 3

RDP-lyssnaren är felkonfigurerad.

## <a name="solution"></a>Lösning

Du löser problemet, [säkerhetskopiera operativsystemdisken](../windows/snapshot-copy-managed-disk.md), och [ansluta operativsystemdisken till en undsättning VM](troubleshoot-recovery-disks-portal-windows.md), och följ sedan anvisningarna.

### <a name="serial-console"></a>Seriekonsol

#### <a name="step-1-turn-on-remote-deskop"></a>Steg 1: Aktivera Remote stationärt

1. Åtkomst till den [Seriekonsolen](serial-console-windows.md) genom att välja **Support och felsökning** > **seriekonsol (förhandsversion)**. Om funktionen är aktiverad på den virtuella datorn, kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Typ **CMD** att starta kanalen för att få dess namn.

3. Växla till kanalen som körs CMD-instans, i det här fallet det bör vara kanal 1.

   ```
   ch -si 1
   ```
4. Kontrollera värdena för registernycklarna på följande sätt:

   1. Se till att RDP-komponenten är aktiverad.

      ```
      REM Get the local policy
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      Om domänprincipen som finns, över installationen på den lokala principen.

         - Om domänprincipen som anger att RDP är inaktiverad (1) och sedan AD-uppdateringsprincip från en domänkontrollant.
         - Om domänprincipen som anger att RDP är aktiverad (0), krävs ingen uppdatering.

      Om domänprincipen som inte finns och den lokala principen anger att RDP är inaktiverat (1), aktiverar du RDP med hjälp av följande kommando:

         ```
         reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
         ```

   2. Kontrollera den aktuella konfigurationen av terminal-servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

   3. Om kommandot returnerar 0, inaktiveras terminalservern. Aktivera sedan terminalservern på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

   4. Terminal Server-modulen är inställd på att tömma läge om servern är i en terminal server (Fjärrskrivbordstjänster eller Citrix). Kontrollera det aktuella läget för Terminal Server-modulen.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

   5. Om kommandot returnerar 1, sätts Terminal Server-modulen att tömma läge. Ställ sedan modulen till fungerande läge på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

   6. Kontrollera om du kan ansluta till servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

   7. Om kommandot returnerar 1, kan du inte ansluta till servern. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```

   8. Kontrollera den aktuella konfigurationen av RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

   9. Om kommandot returnerar 0, inaktiveras RDP-lyssnaren. Aktivera sedan lyssnaren på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

   10. Kontrollera om du kan ansluta till RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   11. Om kommandot returnerar 1, kan du inte ansluta till RDP-lyssnaren. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

6. Starta om den virtuella datorn.

7. Avsluta från CMD-instans genom att skriva `exit`, och tryck sedan på **RETUR** två gånger.

8. Starta om den virtuella datorn genom att skriva `restart`.

Om problemet inträffar fortfarande kan gå vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera Fjärrskrivbordstjänster

Mer information finns i [Remote Desktop Services inte startar på en Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [fjärrskrivbord kopplar bort ofta i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offlinereparation

#### <a name="step-1-turn-on-remote-deskop"></a>Steg 1: Aktivera Remote stationärt

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
3. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
4. Öppna en upphöjd kommandotolk-session (**kör som administratör**). Kör följande skript. I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

3. Om den virtuella datorn är ansluten till en domän, kontrollerar du följande registernyckel för att se om det finns en grupprincip som inaktiverar RDP. 

   ```
   HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
   ```


      Om värdet för nyckeln anges till 1 som innebär att RDP har inaktiverats av principen. Ändra följande princip från en domänkontrollant om du vill aktivera Fjärrskrivbord genom GPO-principen:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

4. Koppla bort disken från Räddade VM.
5. [Skapa en ny virtuell dator från disken](../windows/create-vm-specialized.md).

Om problemet inträffar fortfarande kan gå vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera Fjärrskrivbordstjänster

Mer information finns i [Remote Desktop Services inte startar på en Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [fjärrskrivbord kopplar bort ofta i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
