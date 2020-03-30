---
title: Ett internt fel uppstår när du gör en RDP-anslutning till Virtuella Azure-datorer | Microsoft-dokument
description: Lär dig hur du felsöker interna fel i fjärrskrivbords-avdelningen i Microsoft Azure.| Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266928"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Ett internt fel inträffar när du försöker ansluta till en virtuell Azure-dator via Fjärrskrivbord

I den här artikeln beskrivs ett fel som kan uppstå när du försöker ansluta till en virtuell dator (VM) i Microsoft Azure.


## <a name="symptoms"></a>Symtom

Du kan inte ansluta till en virtuell Azure-dator med fjärrskrivbordsprotokollet (RDP). Anslutningen fastnar i avsnittet Konfigurera fjärr eller så visas följande felmeddelande:

- Internt fel för RDP
- Ett internt fel har inträffat
- Det går inte att ansluta den här datorn till fjärrdatorn. Försök ansluta igen. Om problemet kvarstår kontaktar du fjärrdatorns ägare eller nätverksadministratören


## <a name="cause"></a>Orsak

Det här problemet kan uppstå av följande skäl:

- Det går inte att komma åt de lokala RSA-krypteringsnycklarna.
- TLS-protokollet är inaktiverat.
- Certifikatet är skadat eller utgånget.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

Om du vill felsöka problemet använder du seriekonsolen eller [reparerar den virtuella datorn offline](#repair-the-vm-offline) genom att koppla OS-disken för den virtuella datorn till en återställnings-VM.


### <a name="use-serial-control"></a>Använd seriell kontroll

Anslut till [seriekonsolen och öppna PowerShell-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om seriekonsolen inte är aktiverad på den virtuella datorn går du till [avsnittet reparera den virtuella datorn offline.](#repair-the-vm-offline)

#### <a name="step-1-check-the-rdp-port"></a>Steg: 1 Kontrollera RDP-porten

1. I en PowerShell-instans använder du [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) för att kontrollera om port 8080 används av andra program:

        Netstat -anob |more
2. Om Termservice.exe använder 8080-porten går du till steg 2. Om en annan tjänst eller ett annat program än Termservice.exe använder 8080-porten gör du så här:

    1. Stoppa tjänsten för programmet som använder 3389-tjänsten:

            Stop-Service -Name <ServiceName> -Force

    2. Starta terminaltjänsten:

            Start-Service -Name Termservice

2. Om programmet inte kan stoppas, eller om den här metoden inte gäller dig, ändrar du porten för RDP:

    1. Ändra porten:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Ställ in brandväggen för den nya porten:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Uppdatera nätverksskyddsgruppen för den nya porten](../../virtual-network/security-overview.md) i Azure portal RDP-porten.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Steg 2: Ange rätt behörigheter för det självsignerade RDP-certifikatet

1.  I en PowerShell-instans kör du följande kommandon en efter en för att förnya det självsignerade RDP-certifikatet:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Om du inte kan förnya certifikatet med den här metoden kan du försöka förnya det självsignerade RDP-certifikatet på distans:

    1. Skriv **mmc** i rutan **Kör** från en fungerande virtuell dator som har anslutning till den virtuella datorn som har problem.
    2. Välj **Lägg till/ta bort snapin-moduler på Arkiv-menyn,** välj **Certifikat**och välj sedan **Lägg till**. **File**
    3. Välj **Datorkonton,** välj **En annan dator**och lägg sedan till IP-adressen för problemet VM.
    4. Gå till mappen **Fjärrskrivbord\Certifikat,** högerklicka på certifikatet och välj sedan **Ta bort**.
    5. Starta om konfigurationstjänsten för fjärrskrivbord i en PowerShell-instans från seriekonsolen:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Återställ behörigheten för mappen MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Starta om den virtuella datorn och försök sedan starta en anslutning till den virtuella datorn. Om felet fortfarande inträffar går du till nästa steg.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Steg 3: Aktivera alla TLS-versioner som stöds

RDP-klienten använder TLS 1.0 som standardprotokoll. Detta kan dock ändras till TLS 1.1, som har blivit den nya standarden. Om TLS 1.1 är inaktiverat på den virtuella datorn misslyckas anslutningen.
1.  I en CMD-instans aktiverar du TLS-protokollet:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Om du vill förhindra att AD-principen skriver över ändringarna stoppar du grupprincipuppdateringen tillfälligt:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Starta om den virtuella datorn så att ändringarna börjar gälla. Om problemet är löst kör du följande kommando för att återaktivera grupprincipen:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Om ändringen återställs betyder det att det finns en Active Directory-princip i företagsdomänen. Du måste ändra den principen för att undvika att det här problemet uppstår igen.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en återställnings-VM

1. [Koppla OS-disken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. När OS-disken är ansluten till återställningsdatorn kontrollerar du att disken är flaggad som **online** i diskhanteringskonsolen. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.
3. Starta en anslutning till återställningsdatorn till återställningsdatorn.

#### <a name="enable-dump-log-and-serial-console"></a>Aktivera dumplogg och seriekonsol

Om du vill aktivera dumplogg och seriekonsol kör du följande skript.

1. Öppna en upphöjd kommandotolkssession (**Kör som administratör**).
2. Kör följande skript:

    I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.

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

1. Öppna en upphöjd kommandotolkssession (**Kör som administratör**).
2. Kör följande skript. I det här skriptet antar vi att enhetsbeteckningen som har tilldelats den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Aktivera alla TLS-versioner som stöds

1.  Öppna en upphöjd kommandotolkssession (**Kör som administratör**) och kör följande kommandon. Följande skript förutsätter att drivrutinsbeteckningen är tilldelad till den anslutna OS-disken är F. Ersätt den här enhetsbeteckningen med rätt värde för den virtuella datorn.
2.  Kontrollera vilka TLS som är aktiverat:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Om nyckeln inte finns, eller om värdet är **0,** aktiverar du protokollet genom att köra följande skript:

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
5.  [Koppla från OS-disken och återskapa den virtuella datorn](../windows/troubleshoot-recovery-disks-portal.md)och kontrollera sedan om problemet är löst.





