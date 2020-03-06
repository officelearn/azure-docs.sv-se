---
title: Ett internt fel uppstår när du gör en RDP-anslutning till Azure Virtual Machines | Microsoft Docs
description: Lär dig att felsöka RDP internt fel i Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381676"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Ett internt fel inträffar vid försök att ansluta till en Azure-dator via fjärrskrivbord

Den här artikeln beskrivs ett fel som kan uppstå när du försöker ansluta till en virtuell dator (VM) i Microsoft Azure.


## <a name="symptoms"></a>Symtom

Du kan inte ansluta till en Azure-dator med hjälp av remote desktop protocol (RDP). Anslutningen fastnar i avsnittet ”Konfigurera fjärranslutning” eller så visas följande felmeddelande visas:

- Internt fel för RDP
- Ett internt fel har uppstått
- Den här datorn kan inte anslutas till fjärrdatorn. Vill du försöka ansluta igen. Om problemet kvarstår, kontakta ägaren till fjärrdatorn eller nätverksadministratören


## <a name="cause"></a>Orsak

Det här problemet kan inträffa av följande skäl:

- De lokala RSA-nycklarna för kryptering kan inte nås.
- TLS-protokollet är inaktiverat.
- Certifikatet är skadat eller har upphört att gälla.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Du kan felsöka det här problemet genom att använda serie konsolen eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla den virtuella DATORns OS-disk till en virtuell dator för återställning.


### <a name="use-serial-control"></a>Använda seriell kontroll

Anslut till [serie konsolen och öppna PowerShell-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om serie konsolen inte är aktive rad på den virtuella datorn går du till avsnittet [reparera den virtuella datorn offline](#repair-the-vm-offline) .

#### <a name="step-1-check-the-rdp-port"></a>Steg: 1 Kontrollera RDP-porten

1. I en PowerShell-instans använder du [netstat](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) för att kontrol lera om Port 8080 används av andra program:

        Netstat -anob |more
2. Om Termservice.exe använder port 8080, gå till steg 2. Om en annan tjänst eller program än Termservice.exe använder port 8080, följer du dessa steg:

    1. Stoppa tjänsten för det program som använder 3389-tjänsten:

            Stop-Service -Name <ServiceName> -Force

    2. Starta tjänsten terminal:

            Start-Service -Name Termservice

2. Om programmet inte kan stoppas, eller om den här metoden inte gäller för dig, kan du ändra porten för RDP:

    1. Ändra porten:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Ställa in brandväggen för den nya porten:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Uppdatera nätverks säkerhets gruppen för den nya porten](../../virtual-network/security-overview.md) i Azure Portal RDP-porten.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Steg 2: Ange rätt behörigheter för det självsignerade certifikatet för RDP

1.  Kör följande kommandon ett i taget att förnya det självsignerade certifikatet för RDP i en PowerShell-instans:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Om du inte förnyar certifikatet med hjälp av den här metoden, försök förnya ett självsignerat certifikat för RDP via en fjärranslutning:

    1. Från en fungerande virtuell dator som har anslutning till den virtuella datorn som har problem, skriver du **MMC** i rutan **Kör** för att öppna Microsoft Management Console.
    2. I menyn **Arkiv** väljer du **Lägg till/ta bort snapin-modul**, väljer **certifikat**och väljer sedan **Lägg till**.
    3. Välj **dator konton**, Välj **en annan dator**och Lägg sedan till IP-adressen för den virtuella problemet.
    4. Gå till mappen **fjärr Desktop\Certificates** , högerklicka på certifikatet och välj sedan **ta bort**.
    5. Starta om tjänsten för konfiguration av fjärrskrivbord i en PowerShell-instans från Seriekonsolen:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Återställa behörigheten för mappen MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Starta om den virtuella datorn och försök sedan starta en fjärrskrivbordsanslutning till den virtuella datorn. Om felet kvarstår, gå till nästa steg.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Steg 3: Aktivera alla TLS-versioner som stöds

RDP-klient använder TLS 1.0 som standardprotokoll. Detta kan dock ändras till TLS 1.1, som har blivit den nya standarden. Om TLS 1.1 inaktiveras på den virtuella datorn, misslyckas anslutningen.
1.  Aktivera TLS-protokollet i en CMD-instans:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Förhindra att skriver över ändringarna i AD-princip genom att stoppa uppdateringen av Grupprincip tillfälligt:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Starta om den virtuella datorn så att ändringarna träder i kraft. Om problemet är löst, kör du följande kommando för att återaktivera en Grupprincip:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Om ändringen återställs, innebär det att det finns en princip för Active Directory i din företagsdomän. Du måste ändra principen för att undvika det här problemet uppstår igen.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disken till en virtuell dator för återställning](../windows/troubleshoot-recovery-disks-portal.md).
2. När OS-disken är ansluten till den virtuella återställnings datorn kontrollerar du att disken är flaggad som **online** i disk hanterings konsolen. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
3. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

#### <a name="enable-dump-log-and-serial-console"></a>Aktivera dump logg- och Seriekonsol

Kör följande skript för att aktivera dump logg- och Seriekonsol.

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kör följande skript:

    I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Återställa behörigheten för mappen MachineKeys

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kör följande skript. I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Aktivera alla TLS-versioner

1.  Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**) och kör följande kommandon. Skriptet nedan förutsätter att enhetsbokstaven som har tilldelats till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med rätt värde för den virtuella datorn.
2.  Kontrollera vilken TLS är aktiverat:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Om nyckeln inte finns, eller om dess värde är **0**, aktiverar du protokollet genom att köra följande skript:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Aktivera NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  Ta [bort OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md)och kontrol lera om problemet är löst.





