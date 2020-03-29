---
title: Fjärrskrivbord kopplas ofta från i Azure VM| Microsoft-dokument
description: Lär dig hur du felsöker frekventa frånkopplingar av Fjärrskrivbord i Azure VM.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918231"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Fjärrskrivbord kopplar ofta från på den virtuella Azure-datorn

I den här artikeln beskrivs felsöka frekventa frånkopplingar till en virtuell Azure-dator (VM) via FJÄRRSKRIVBORDSPROTOKOLL RDP).


## <a name="symptom"></a>Symptom

Du står inför återkommande RDP-anslutningsproblem under dina sessioner. Du kan först ansluta till den virtuella datorn, men sedan anslutningen sjunker.

## <a name="cause"></a>Orsak

Det här problemet kan uppstå om RDP-lyssnaren är felkonfigurerad. Det här problemet uppstår vanligtvis på en virtuell dator som använder en anpassad avbildning.

## <a name="solution"></a>Lösning

Innan du följer dessa steg [ska du ta en ögonblicksbild av OS-disken](../windows/snapshot-copy-managed-disk.md) för den berörda virtuella datorn som en säkerhetskopia. 

Om du vill felsöka problemet använder du seriekontroll eller [reparerar den virtuella datorn offline](#repair-the-vm-offline) genom att koppla OS-disken för den virtuella datorn till en återställnings-VM.

### <a name="serial-control"></a>Seriell kontroll

1. Anslut till [seriekonsolen och öppna CMD-instansen](./serial-console-windows.md). Kör sedan följande kommandon för att återställa RDP-konfigurationerna. Om seriekonsolen inte är aktiverad på den virtuella datorn går du vidare till nästa steg.
2. Sänk RDP-säkerhetslagret till 0. Vid den här inställningen använder kommunikation mellan server och klient den inbyggda RDP-krypteringen.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Sänk krypteringsnivån till minimiinställningen så att äldre RDP-klienter kan ansluta.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Ange att RDP ska läsa in klientdatorns användarkonfiguration.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Aktivera RDP Keep-Alive-kontrollen:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Ställ in kontrollen för återanslutning till FJÄRRSKRIVBORDSP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Ange tidskontrollen för RDP-sessionen:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Ställ in time-kontrollen för frånkoppling av fjärrskrivbords-distribution: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Ange kontrollen för anslutningstid för FJÄRRSKRIVBORDS-anslutning:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Ange inaktiv tidskontroll för fjärrskrivbordssession:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Ställ in kontrollen "Begränsa de maximala samtidiga anslutningarna":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Starta om den virtuella datorn och försök igen att ansluta till den med hjälp av RDP.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. När OS-disken är ansluten till återställningsdatorn kontrollerar du att disken är flaggad som **online** i diskhanteringskonsolen. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.
3. Navigera till mappen **\windows\system32\config** på den OS-disk som du har bifogat. Kopiera alla filer i den här mappen som en säkerhetskopia, om en återställning krävs.
4. Starta Registereditorn (regedit.exe).
5. Välj **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten. Välj > **Filinläsningsdatafil**på menyn: **File**
6. Bläddra till mappen **\windows\system32\config\SYSTEM** på os-disken som du har bifogat. För namnet på registreringsdatafilen anger du **BROKENSYSTEM**. Den nya registreringsdatafilen visas under **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten. Läs sedan in programvaran hive **\windows\system32\config\SOFTWARE** under **HKEY_LOCAL_MACHINE.Then** load the software hive \windows\system32\config\SOFTWARE under the HKEY_LOCAL_MACHINE key. För namnet på hive-programvaran, ange **BROKENSOFTWARE**. 
7. Öppna ett upphöjt kommandotolksfönster (**Kör som administratör**) och kör kommandon i de återstående stegen för att återställa RDP-konfigurationerna. 
8. Sänk RDP-säkerhetslagret till 0 så att kommunikationen mellan servern och klienten använder den inbyggda RDP-krypteringen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Sänk krypteringsnivån till minimiinställningen så att äldre RDP-klienter kan ansluta:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Ange att RDP ska läsas in användarkonfigurationen för klientdatorn.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Aktivera RDP Keep-Alive-kontrollen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Ställ in kontrollen för återanslutning till FJÄRRSKRIVBORDSP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Ange tidskontrollen för RDP-sessionen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Ställ in time-kontrollen för frånkoppling av fjärrskrivbords-distribution:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Ange kontrollen för anslutningstid för FJÄRRSKRIVBORDS-anslutning:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Ange inaktiv tidskontroll för fjärrskrivbordssession: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Ställ in kontrollen "Begränsa de maximala samtidiga anslutningarna":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Starta om den virtuella datorn och försök igen att ansluta till den med hjälp av RDP.

## <a name="need-help"></a>Behöver du hjälp? 
Kontakta supporten. Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.





