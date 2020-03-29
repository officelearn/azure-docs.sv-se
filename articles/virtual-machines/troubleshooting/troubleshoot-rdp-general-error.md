---
title: Felsöka ett generalfel för fjärrskrivbords-distributioner till en Windows-virtuell dator i Azure | Microsoft-dokument
description: Lär dig hur du felsöker ett generalfel för fjärrskrivbords-distributioner till en Windows VM i Azure | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058002"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Felsöka ett generalfel för fjärrskrivbords-distributioner i Azure VM

I den här artikeln beskrivs ett allmänt fel som kan uppstå när du gör en RDP-anslutning (Remote Desktop Protocol) till en virtuell dator (Virtuell dator) i Azure.

## <a name="symptom"></a>Symptom

NÃ¤r du ankÃ¤nder en RDP-anslutning till en Windows VM i Azure kan fÃ¶50 visas fÃ¶1 ã¶ssã¶en allmän felmeddelande:

**Fjärrskrivbord kan inte ansluta till fjärrdatorn av något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktiverad**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrollera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst är aktiverad.**

## <a name="cause"></a>Orsak

Det här problemet kan uppstå på grund av följande orsaker:

### <a name="cause-1"></a>Orsak 1

RDP-komponenten är inaktiverad enligt följande:

- På komponentnivå
- På lyssnarnivå
- På terminalservern
- På värdrollen Värd för fjärrskrivbordssession

### <a name="cause-2"></a>Orsak 2

Fjärrskrivbordstjänster (TermService) körs inte.

### <a name="cause-3"></a>Orsak 3

RDP-lyssnaren är felkonfigurerad.

## <a name="solution"></a>Lösning

LÃ¶s problemet genom [att säkerhetskopiera operativsystemets disk](../windows/snapshot-copy-managed-disk.md)och [ansluta operativsystemdisken till en räddnings-VM](troubleshoot-recovery-disks-portal-windows.md)och följ sedan stegen.

### <a name="serial-console"></a>Seriekonsol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Steg 1: Öppna CMD-instans i seriell konsol

1. Öppna [seriekonsolen](serial-console-windows.md) genom att välja **Stöd & Felsökning av** > **seriekonsol (förhandsgranskning)**. Om funktionen är aktiverad på den virtuella datorn kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Skriv **CMD** för att starta kanalen för att hämta kanalnamnet.

3. Växla till kanalen som kör CMD-instansen, i det här fallet bör det vara kanal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Steg 2: Kontrollera värdena för RDP-registernycklar:

1. Kontrollera om RDP är inaktiverat av polisen.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Om domänprincipen finns skrivs inställningarna för den lokala principen över.
      - Om domänprincipen anger att RDP är inaktiverat (1) uppdaterar du AD-principen från domänkontrollanten.
      - Om domänprincipen anger att RDP är aktiverat (0) behövs ingen uppdatering.
      - Om domänprincipen inte finns och den lokala principen anger att RDP är inaktiverat (1) aktiverar du RDP med hjälp av följande kommando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Kontrollera terminalserverns aktuella konfiguration.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Om kommandot returnerar 0 inaktiveras terminalservern. Aktivera sedan terminalservern på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Terminal Server-modulen är inställd på tömningsläge om servern finns på en terminalservergrupp (RDS eller Citrix). Kontrollera det aktuella läget för Terminal Server-modulen.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Om kommandot returneras 1 är Terminal Server-modulen inställd på tömningsläge. Ställ sedan in modulen på arbetsläge enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Kontrollera om du kan ansluta till terminalservern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Om kommandot returnerar 1 kan du inte ansluta till terminalservern. Aktivera sedan anslutningen på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Kontrollera den aktuella konfigurationen för RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Om kommandot returnerar 0 inaktiveras RDP-lyssnaren. Aktivera sedan lyssnaren på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Kontrollera om du kan ansluta till RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Om kommandot returnerar 1 kan du inte ansluta till RDP-lyssnaren. Aktivera sedan anslutningen på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Starta om den virtuella datorn.

8. Avsluta CMD-förekomsten genom `exit`att skriva och tryck sedan **på Retur** två gånger.

9. Starta om den `restart`virtuella datorn genom att skriva och anslut sedan till den virtuella datorn.

Om problemet kvarstår går du vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera fjärrskrivbordstjänster

Mer information finns i [Fjärrskrivbordstjänster startar inte på en Virtuell Azure.](troubleshoot-remote-desktop-services-issues.md)

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [Remote Desktop kopplar ofta från i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Reparation offline

#### <a name="step-1-turn-on-remote-desktop"></a>Steg 1: Aktivera fjärrskrivbord

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en anslutning till återställningsdatorn till återställningsdatorn.
3. Kontrollera att disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.
4. Starta en anslutning till återställningsdatorn till återställningsdatorn.
5. Öppna en upphöjd kommandotolkssession (**Kör som administratör**). Kör följande skript. I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.

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

6. Om den virtuella datorn är domänansluten kontrollerar du följande registernyckel för att se om det finns en gruppprincip som inaktiverar RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Om det här nyckelvärdet är inställt på 1 betyder det att RDP inaktiveras av principen. Om du vill aktivera Fjärrskrivbord via principen omrincipobjekt ändrar du följande princip från domänkontrollanten:

   
      **Datorkonfiguration\Principer\Administrativa mallar:**

      Principdefinitioner\Windows-komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Anslutningar\Tillåt användare att fjärransluta med hjälp av Fjärrskrivbordstjänster
  
1. Koppla bort disken från räddningsdatorn.
1. [Skapa en ny virtuell dator från disken](../windows/create-vm-specialized.md).

Om problemet kvarstår går du vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera fjärrskrivbordstjänster

Mer information finns i [Fjärrskrivbordstjänster startar inte på en Virtuell Azure.](troubleshoot-remote-desktop-services-issues.md)

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställa RDP-lyssnare

Mer information finns i [Remote Desktop kopplar ofta från i Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
