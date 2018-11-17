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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 25e70b3a685f3b777a74c4cc6bf0e56dd37741a7
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821522"
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

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Steg 1: Öppna CMD-instans i seriekonsol

1. Åtkomst till den [Seriekonsolen](serial-console-windows.md) genom att välja **Support och felsökning** > **seriekonsol (förhandsversion)**. Om funktionen är aktiverad på den virtuella datorn, kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Typ **CMD** att starta kanalen för att få dess namn.

3. Växla till kanalen som körs CMD-instans, i det här fallet det bör vara kanal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Steg 2: Kontrollera värdena för RDP-registernycklar:

1. Kontrollera om RDP har inaktiverats av principer.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Om domänprincipen som finns, över installationen på den lokala principen.
      - Om domänprincipen som anger att RDP är inaktiverad (1) och sedan AD-uppdateringsprincip från en domänkontrollant.
      - Om domänprincipen som anger att RDP är aktiverad (0), krävs ingen uppdatering.
      - Om domänprincipen som inte finns och den lokala principen anger att RDP är inaktiverat (1), aktiverar du RDP med hjälp av följande kommando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Kontrollera den aktuella konfigurationen av terminal-servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Om kommandot returnerar 0, inaktiveras terminalservern. Aktivera sedan terminalservern på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Terminal Server-modulen är inställd på att tömma läge om servern är i en terminal server (Fjärrskrivbordstjänster eller Citrix). Kontrollera det aktuella läget för Terminal Server-modulen.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Om kommandot returnerar 1, sätts Terminal Server-modulen att tömma läge. Ställ sedan modulen till fungerande läge på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Kontrollera om du kan ansluta till servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Om kommandot returnerar 1, kan du inte ansluta till servern. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Kontrollera den aktuella konfigurationen av RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Om kommandot returnerar 0, inaktiveras RDP-lyssnaren. Aktivera sedan lyssnaren på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Kontrollera om du kan ansluta till RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Om kommandot returnerar 1, kan du inte ansluta till RDP-lyssnaren. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Starta om den virtuella datorn.

8. Avsluta från CMD-instans genom att skriva `exit`, och tryck sedan på **RETUR** två gånger.

9. Starta om den virtuella datorn genom att skriva `restart`, och sedan ansluta till den virtuella datorn.

Om problemet inträffar fortfarande kan gå vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera Fjärrskrivbordstjänster

Mer information finns i [Remote Desktop Services inte startar på en Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [fjärrskrivbord kopplar bort ofta i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offlinereparation

#### <a name="step-1-turn-on-remote-desktop"></a>Steg 1: Aktivera Fjärrskrivbord

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
4. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
5. Öppna en upphöjd kommandotolk-session (**kör som administratör**). Kör följande skript. I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

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

6. Om den virtuella datorn är ansluten till en domän, kontrollerar du följande registernyckel för att se om det finns en grupprincip som inaktiverar RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Om värdet för nyckeln anges till 1 som innebär att RDP har inaktiverats av principen. Ändra följande princip från en domänkontrollant om du vill aktivera Fjärrskrivbord genom GPO-principen:

   
      **Datorn Datorkonfiguration\Principer\Administrativa mallar:**

      Principen definitions\Windows komponenter\Fjärrskrivbordstjänster\Värdserver Desktop för Desktop Session Host\Connections\Allow användare för fjärranslutning med hjälp av Fjärrskrivbordstjänster
  
7. Koppla bort disken från Räddade VM.
8. [Skapa en ny virtuell dator från disken](../windows/create-vm-specialized.md).

Om problemet inträffar fortfarande kan gå vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera Fjärrskrivbordstjänster

Mer information finns i [Remote Desktop Services inte startar på en Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [fjärrskrivbord kopplar bort ofta i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
