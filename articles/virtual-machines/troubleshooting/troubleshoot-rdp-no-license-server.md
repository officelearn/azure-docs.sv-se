---
title: Licensservern för fjärrskrivbord är inte tillgänglig när du ansluter till en Virtuell Azure-dator | Microsoft-dokument
description: Lär dig hur du felsöker problem med rdp-fel eftersom det inte finns någon licensserver för fjärrskrivbord | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088526"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Licensserver för fjärrskrivbord är inte tillgänglig när du ansluter till en Virtuell Azure-dator

Den här artikeln hjälper till att lösa problemet när du inte kan ansluta till en virtuell Azure-dator (VM) eftersom ingen licensserver för fjärrskrivbord är tillgänglig för att tillhandahålla en licens.

## <a name="symptoms"></a>Symtom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till en virtuell dator (VM) visas fÃ¶1 fÃ¶1 ã¤ ns:

- Skärmbilden för den virtuella datorn visar att operativsystemet är fullständigt laddat och väntar på autentiseringsuppgifter.
- Följande felmeddelanden visas när du försöker upprätta en RDP-anslutning (Microsoft Remote Desktop Protocol):

  - Fjärrsessionen kopplades från eftersom det inte finns några licensservrar för fjärrskrivbord tillgängliga för att tillhandahålla en licens.

  - Det finns ingen licensserver för fjärrskrivbord. Fjärrskrivbordstjänster slutar fungera eftersom den här datorn har passerat sin respitperiod och inte har kontaktat åtminstone en giltig Licensserver för Windows Server 2008. Välj det här meddelandet om du vill öppna värdserverkonfiguration för fjärrskrivbordssession för att använda licensieringsdiagnos.

Du kan dock ansluta till den virtuella datorn normalt med hjälp av en administrativ session:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Orsak

Det hÃ¤r problemet uppstÃ¥r om en licensserver fÃ¶r fjärrskrivbord inte är tillgänglig fÃ¶r att tillhandahålla en licens fÃ¶r att starta en fjärrsession. Det kan orsakas av flera scenarier, även om en värdroll för fjärrskrivbordssession har konfigurerats på den virtuella datorn:

- Det fanns aldrig en fjärrskrivbordslicensieringsroll i miljön, och respitperioden, 180 dagar, är över.
- En fjärrskrivbordslicens installerades i miljön, men den aktiveras aldrig.
- En fjärrskrivbordslicens i miljön har inte klientåtkomstlicenser (CALs) som injiceras för att konfigurera anslutningen.
- En licens för fjärrskrivbord installerades i miljön. Det finns tillgängliga åtkomstkontrollistor, men de har inte konfigurerats korrekt.
- En fjärrskrivbordslicens har klientåtkomstlicenser och aktiverades. Vissa andra problem på licensservern för fjärrskrivbord hindrar den dock från att tillhandahålla licenser i miljön.

## <a name="solution"></a>Lösning

LÃ¶s problemet genom [att säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md) och sÃ¶j:

1. Anslut till den virtuella datorn med hjälp av en administrativ session:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Om du inte kan ansluta till den virtuella datorn med hjälp av en administrativ session kan du använda [seriekonsolen för virtuella](serial-console-windows.md) datorer på Azure för att komma åt den virtuella datorn enligt följande:

    1. Öppna seriekonsolen genom att välja **Stöd & Felsökning av** > **seriekonsol (förhandsgranskning)**. Om funktionen är aktiverad på den virtuella datorn kan du ansluta den virtuella datorn.

    2. Skapa en ny kanal för en CMD-instans. Ange **CMD** för att starta kanalen och hämta kanalnamnet.

    3. Växla till kanalen som kör CMD-instansen. I detta fall bör det vara kanal 1:

       ```
       ch -si 1
       ```

    4. Välj **Ange** igen och ange ett giltigt användarnamn och lösenord, lokalt eller domän-ID, för den virtuella datorn.

2. Kontrollera om den virtuella datorn har en värdroll för fjärrskrivbordssession aktiverad. Om rollen är aktiverad kontrollerar du att den fungerar som den ska. Öppna en förhöjd CMD-förekomst och gör så här:

    1. Använd följande kommando för att kontrollera status för rollen Värd för fjärrskrivbordssession:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Om det här kommandot returnerar värdet 0 betyder det att rollen är inaktiverad och du kan gå till steg 3.

    2. Använd följande kommando för att kontrollera principerna och konfigurera om efter behov:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Om **LicensingMode-värdet** är inställt på något annat värde än 4, per användare, ställer du in värdet på 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Om värdet **SpecifiedLicenseServers** inte finns, eller om det har felaktig licensserverinformation, ändrar du det på följande sätt:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. När du har gjort några ändringar i registret startar du om den virtuella datorn.

    4. Om du inte har klientåtkomstlicenser tar du bort rollen Värd för fjärrskrivbordssession. Då RDP kommer att ställas tillbaka till det normala. Det tillåter bara två samtidiga RDP-anslutningar till den virtuella datorn:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Om den virtuella datorn har rollen licensiering för fjärrskrivbord och den inte används kan du också ta bort den rollen:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Kontrollera att den virtuella datorn kan ansluta till licensservern för fjärrskrivbord. Du kan testa anslutningen till port 135 mellan den virtuella datorn och licensservern: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Om det inte finns någon licensserver för fjärrskrivbord i miljön och du vill ha en, kan du [installera en rolltjänst för licensiering av fjärrskrivbord](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Konfigurera sedan [RDS-licensieringen](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Om en licensserver för fjärrskrivbord är konfigurerad och felfri kontrollerar du att licensservern för fjärrskrivbord är aktiverad med klientåtkomstlicenser.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.
