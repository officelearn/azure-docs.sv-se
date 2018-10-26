---
title: Servern för fjärrskrivbordslicenser inte är tillgänglig när du ansluter till virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker problem med RDP-misslyckas eftersom ingen licensserver för fjärrskrivbord är tillgänglig | Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5e54579a35140ee7cfc06358d60cf7a63292e107
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088866"
---
# <a name="remote-desktop-license-server-is-not-available-when-you-connect-to-azure-vm"></a>Servern för fjärrskrivbordslicenser är inte tillgänglig när du ansluter till Azure VM

Den här artikeln hjälper dig att lösa problemet som du inte kan ansluta till en Azure virtuell dator (VM) eftersom ingen licensserver för fjärrskrivbord är tillgängliga för att tillhandahålla en licens.

## <a name="symptoms"></a>Symtom

När du försöker ansluta till en virtuell dator händer följande scenarier:

- Skärmbild för virtuell dator (VM) visar att operativsystemet är fullständigt inläst och väntar på autentiseringsuppgifter.
- Följande felmeddelanden visas när du försöker att ansluta Microsoft Remote Desktop Protocol (RDP):

  - **Fjärrsessionen kopplades från eftersom det finns inga servrar för fjärrskrivbordslicenser tillgängliga för att tillhandahålla en licens.**

  - **Det finns inga licensserver för fjärrskrivbord. Fjärrskrivbordstjänster slutar att fungera eftersom den här datorn är grace-perioden och inte har kontaktat minst en giltig Windows Server 2008 server. Klicka på meddelandet för att öppna RD Session Host serverkonfiguration om du vill använda licensiering diagnos.**

Men kan du ansluta till den virtuella datorn normalt med hjälp av en administrativ session:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Orsak

Det här problemet uppstår om en licensserver för fjärrskrivbord är tillgänglig för att tillhandahålla en licens för att starta en fjärrsession. Det här problemet kan orsakas av flera olika användningsområden, även om en värd för fjärrskrivbordssession roll har ställts in på den virtuella datorn:

- Det har aldrig en licensiering Remote Desktop-roll i miljön och respitperioden (180 dagar) är över.
- En licens för fjärrskrivbord har installerats i miljön, men den aktiveras aldrig.
- En licens för fjärrskrivbord i miljön har klientåtkomstlicenser (CAL) för att upprätta anslutningen.
- En licens för fjärrskrivbord har installerats i miljön. Det finns tillgängliga klientåtkomstlicenser, men de konfigurerats inte korrekt.
- En licens för fjärrskrivbord har klientåtkomstlicenser och den har aktiverats. Dock förhindras några andra problem på servern för fjärrskrivbordslicenser från att tillhandahålla licenser i miljön.

## <a name="solution"></a>Lösning

Du löser problemet, [säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md) och gör följande:

1. Anslut till den virtuella datorn med hjälp av en administrativ session:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

  Om du inte kan ansluta till den virtuella datorn med hjälp av en administrativ session, kan du använda den [Seriekonsolen](serial-console-windows.md) att komma åt den virtuella datorn på följande sätt:

  1. Komma åt Seriekonsolen genom att välja **Support och felsökning** > **seriekonsol (förhandsversion)**. Om funktionen är aktiverad på den virtuella datorn, kan du ansluta den virtuella datorn.

  2. Skapa en ny kanal för en CMD-instans. Typ **CMD** att starta kanalen för att få dess namn.

  3. Växla till kanalen som körs CMD-instans, i det här fallet det bör vara kanal 1.

    ```
    ch -si 1
    ```

  4. Tryck på **RETUR** igen och ange ett giltigt användarnamn och lösenord (lokal eller domänåtkomst-ID) för den virtuella datorn.

2. Kontrollera om den virtuella datorn har en värd för fjärrskrivbordssession-rollen aktiverad. Om rollen är aktiverad, se till att den fungerar korrekt. Öppna en upphöjd CMD-instans och följ de här stegen:

  1. Använd följande kommando för att kontrollera status för rollen värd för fjärrskrivbordssession:

    ```
    reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
    ```

    Om det här kommandot returnerar värdet 0, innebär det att rollen har inaktiverats du kan gå till steg 3.

  2. Använd följande kommando för att kontrollera principerna och konfigurera om efter behov:

    ```
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
    ```

    Om den **LicensingMode** värdet är inställt på ett annat värde än 4 (per användare) och ange sedan värdet till 4:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
    ```

    Om den **SpecifiedLicenseServers** värde inte finns eller också har en felaktig licensinformation på en server kan ändra det på följande sätt:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
    ```

  3. Starta om den virtuella datorn när du gör några ändringar i registret.

  4. Om du inte har klientåtkomstlicenser, tar du bort rollen värd för fjärrskrivbordssession. Sedan RDP anges tillbaka till normal och det tillåter bara två samtidiga RDP-anslutningar till den virtuella datorn.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
    ```

    Om den virtuella datorn har rollen fjärrskrivbord licensiering och den inte används, kan du också ta bort rollen.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Licensing
    ```

  5. Kontrollera att den virtuella datorn kan ansluta till servern för fjärrskrivbordslicenser. Du kan testa anslutningen till port 135 mellan den virtuella datorn och licensservern. 

    ```
    telnet <FQDN / IP License Server> 135
    ```

3. Om det finns inga licensserver för fjärrskrivbord i miljön och du vill ha en, kan du [installera Fjärrskrivbordslicensiering rolltjänsten ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)), och sedan [konfigurerar RDS-licensiering](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Om en licensserver för fjärrskrivbord är konfigurerat och felfritt, se till att licensserver för fjärrskrivbord har aktiverats med för Fjärrskrivbordstjänster.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
