---
title: Fjärr skrivbord frånkopplas ofta i virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker frekventa från kopplingar av fjärr skrivbord i Azure VM.
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
ms.openlocfilehash: be563e39ed1bfa405830999a96d8630b6f8254bb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057972"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Fjärr skrivbord frånkopplas ofta i virtuell Azure-dator

Den här artikeln förklarar hur du felsöker frekventa från kopplingar till en virtuell Azure-dator via Remote Desktop Protocol RDP).

> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder distributions modellen för Resource Manager. Vi rekommenderar att du använder den här modellen för nya distributioner istället för att använda den klassiska distributions modellen.

## <a name="symptom"></a>Symtom

Du har tillfälligt problem med RDP-anslutningen under sessionerna. Du kan ansluta till den virtuella datorn till den virtuella datorn, men anslutningen upprättas.

## <a name="cause"></a>Orsak

Det här problemet kan uppstå om RDP-lyssnaren är felkonfigurerad. Det här problemet uppstår vanligt vis på en virtuell dator som använder en anpassad avbildning.

## <a name="solution"></a>Lösning

Innan du följer de här stegen ska du [ta en ögonblicks bild av OS-disken](../windows/snapshot-copy-managed-disk.md) för den berörda virtuella datorn som en säkerhets kopia. 

Du kan felsöka det här problemet genom att använda seriell kontroll eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla den virtuella DATORns OS-disk till en virtuell återställnings dator.

### <a name="serial-control"></a>Serie kontroll

1. Ansluta till [seriella konsolen och öppna CMD instans](./serial-console-windows.md). Kör sedan följande kommandon för att återställa RDP-konfigurationerna. Om serie konsolen inte är aktive rad på den virtuella datorn går du till nästa steg.
2. Sänk säkerhets skiktet för RDP till 0. Vid den här inställningen använder kommunikationen mellan servern och klienten den inbyggda RDP-krypteringen.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Sänk krypterings nivån till lägsta inställningen för att tillåta att äldre RDP-klienter ansluter.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Ange RDP för att läsa in användar konfigurationen för klient datorn.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Aktivera RDP Keep-Alive-kontrollen:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Ange RDP reconnect-kontrollen:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Ange tids kontroll för RDP-session:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Ställ in kontroll tid för RDP-från koppling: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Ange tids kontroll för RDP-anslutning:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Ange tids kontroll för inaktivitet för RDP-session:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Ange kontrollen begränsa maximalt antal samtidiga anslutningar:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Starta om den virtuella datorn och försök att ansluta till den med hjälp av RDP.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. När OS-disken är ansluten till den Virtuella återställningsdatorn, se till att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
3. Gå till mappen **\Windows\System32\Config** på den OS-disk som du har bifogat. Kopiera alla filer i den här mappen som en säkerhets kopia, om det krävs en återställning.
4. Starta Registereditorn (regedit. exe).
5. Välj **HKEY_LOCAL_MACHINE** -nyckel. På menyn väljer du **fil** > **läsnings registrerings data**fil:
6. Bläddra till mappen **\windows\system32\config\SYSTEM** på den OS-disk som du har anslutit. Ange **BROKENSYSTEM**som namn på Hive. Den nya registrerings data filen visas under nyckeln **HKEY_LOCAL_MACHINE** . Läs sedan in program registrerings data filen **\windows\system32\config\SOFTWARE** under nyckeln **HKEY_LOCAL_MACHINE** . Skriv **BROKENSOFTWARE**som namn på Hive-programvaran. 
7. Öppna ett kommando tolks fönster med förhöjd behörighet (**Kör som administratör**) och kör kommandon i de återstående stegen för att återställa RDP-konfigurationerna. 
8. Sänk säkerhets skiktet för RDP till 0 så att kommunikation mellan servern och klienten använder den inbyggda RDP-krypteringen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Sänk krypterings nivån till lägsta inställning så att äldre RDP-klienter kan ansluta:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Ange RDP för att läsa in användar konfigurationen för klient datorn.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Aktivera RDP Keep-Alive-kontrollen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Ange RDP reconnect-kontrollen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Ange tids kontroll för RDP-session:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Ställ in kontroll tid för RDP-från koppling:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Ange tids kontroll för RDP-anslutning:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Ange tids kontroll för inaktivitet för RDP-session:     REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp"/v "fInheritMaxIdleTime"/t REG_DWORD/d 1/f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Ange kontrollen begränsa maximalt antal samtidiga anslutningar:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Starta om den virtuella datorn och försök att ansluta till den med hjälp av RDP.

## <a name="need-help"></a>Behöver du hjälp? 
Kontakta supporten. Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.





