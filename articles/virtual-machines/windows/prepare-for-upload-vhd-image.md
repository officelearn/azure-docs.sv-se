---
title: Förbereda en Windows virtuell Hårddisk för överföring till Azure | Microsoft Docs
description: Lär dig hur du förbereder en Windows-VHD eller VHDX för att överföra den till Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: f40b3e0d2a49f6522149a977572d4f3c12e34255
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720060"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbereda en Windows-VHD eller VHDX för att överföra till Azure

Innan du överför en Windows virtuell dator (VM) från en lokal plats till Azure måste du förbereda den virtuella hårddisken (VHD eller VHDX). Azure stöder både generation 1 och generation 2 virtuella datorer som finns i VHD-format och som har en disk med fast storlek. Den maximala storleken som tillåts för den virtuella Hårddisken är 1,023 GB. 

I en generation 1 VM, kan du konvertera ett filsystem för VHDX till VHD. Du kan också konvertera en dynamiskt expanderande disk till en disk med fast storlek. Men du kan inte ändra en virtuell dator generation. Mer information finns i [bör jag skapa en generation 1 eller 2 virtuella datorer i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) och [supporten för generering 2 virtuella datorer (förhandsversion)](generation-2.md).

Information om principen för stöd för virtuella Azure-datorer finns i [Microsofts serverprogramsupport för virtuella Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Anvisningar i den här artikeln gäller för 64-bitarsversionen av Windows Server 2008 R2 och senare Windows Server-operativsystem. Information om hur du kör ett 32-bitars operativsystem i Azure finns i [stöd för 32-bitars operativsystem i virtuella Azure-datorer](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Konvertera den virtuella disken till en fast storlek och till virtuell Hårddisk 
Om du vill konvertera din virtuella hårddisk till formatet som krävs för Azure kan du använda någon av metoderna i det här avsnittet. Säkerhetskopiera den virtuella datorn innan du konverterar den virtuella disken. Kontrollera att Windows-VHD: N fungerar korrekt på den lokala servern. Lös eventuella fel i Virtuellt datorn innan du försöker konvertera eller överför den till Azure.

När du konverterar disken kan du skapa en virtuell dator som använder disken. Starta och logga in på den virtuella datorn och slutför förbereder den för att ladda upp.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Använd Hyper-V Manager för att konvertera disken 
1. Öppna Hyper-V Manager och välj din lokala dator till vänster. I menyn ovanför datorlistan, väljer **åtgärd** > **redigera Disk**.
2. På den **hitta virtuell hårddisk** väljer du den virtuella disken.
3. På den **Välj åtgärd** väljer **konvertera** > **nästa**.
4. Om du vill konvertera från VHDX Välj **VHD** > **nästa**.
5. Om du vill konvertera från en dynamiskt expanderande disk kan välja **fast storlek** > **nästa**.
6. Leta upp och välj en sökväg för att spara den nya VHD-filen till.
7. Välj **Slutför**.

> [!NOTE]
> Använda en upphöjd PowerShell-session för att köra kommandon i den här artikeln.

### <a name="use-powershell-to-convert-the-disk"></a>Använd PowerShell för att konvertera disken 
Du kan konvertera en virtuell disk med hjälp av den [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) i Windows PowerShell. Välj **kör som administratör** när du startar PowerShell. 

Följande exempelkommando konverterar disken från VHDX till VHD. Kommandot också konverterar disken från en dynamiskt expanderande disk till en disk med fast storlek.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

I det här kommandot ersätter du värdet för `-Path` med sökvägen till den virtuella hårddisken som du vill konvertera. Ersätt värdet för `-DestinationPath` med den nya sökvägen och namnet på den konvertera disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK diskformat
Om du har en Windows VM-avbildning i den [VMDK-filformat](https://en.wikipedia.org/wiki/VMDK), använda den [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) att konvertera den till VHD-format. Mer information finns i [så här konverterar du en VMware VMDK till VHD för Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

På den virtuella datorn som du planerar att ladda upp till Azure, kör du följande kommandon från ett [kommandotolk](https://technet.microsoft.com/library/cc947813.aspx):

1. Ta bort alla permanent statisk väg i routningstabellen:
   
   * Om du vill visa routningstabellen kör `route print` i Kommandotolken.
   * Kontrollera den `Persistence Routes` avsnitt. Om det finns en beständig väg, använda den `route delete` kommando för att ta bort den.
2. Ta bort WinHTTP-proxyservern:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Om den virtuella datorn behöver arbeta med en specifik proxy kan du lägga till en proxy-undantag till Azure-IP-adressen ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) så att den virtuella datorn kan ansluta till Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Ange disken SAN-principen för [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    I den öppna Kommandotolken, Skriv följande kommandon:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ange tiden för Coordinated Universal Time (UTC) för Windows. Också ställa in starttypen för tjänsten Windows time (`w32time`) till `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ange power-profil till hög prestanda:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Se till att miljövariabler `TEMP` och `TMP` ställs till sina standardvärden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Kontrollera Windows-tjänster
Se till att var och en av följande Windows-tjänster är inställda på standardvärdena för Windows. De här tjänsterna är minst måste ställas in för att se till att VM-anslutning. Om du vill återställa inställningar för Start, kör du följande kommandon:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Uppdatera remote-desktop registerinställningar
Kontrollera att följande inställningar är korrekt konfigurerade för fjärråtkomst:

>[!NOTE] 
>Du kan få ett felmeddelande när du kör `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. Du kan ignorera det här meddelandet. Det innebär bara att domänen inte push-överföra den konfigurationen via ett grupprincipobjekt.

1. Remote Desktop Protocol (RDP) är aktiverat:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP-porten är korrekt konfigurerad. Standardporten är 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    När du distribuerar en virtuell dator skapas standardreglerna mot port 3389. Om du vill ändra portnumret kan du göra det efter att den virtuella datorn har distribuerats i Azure.

3. Lyssnaren lyssnar i varje nätverksgränssnitt:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Konfigurera autentisering på nätverksnivå (NLA)-läge för RDP-anslutningar:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Ange keep-alive värdet:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Återansluta:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Begränsa antalet samtidiga anslutningar:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Ta bort eventuella självsignerade certifikat som är kopplad till RDP-lyssnaren:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Den här koden ser till att du kan ansluta i början när du distribuerar den virtuella datorn. Om du vill granska det senare kan göra du det efter att den virtuella datorn har distribuerats i Azure.

9. Om den virtuella datorn kommer att ingå i en domän, kontrollerar du följande principer för att kontrollera att de tidigare inställningarna inte har återställts. 
    
    | Mål                                     | Princip                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP har aktiverats                           | Datorkonfiguration\Principer\Windows Settings\Administrative Templates\Components\Remote för fjärrskrivbordssession\Anslutningar         | Tillåt användare att ansluta via en fjärranslutning med hjälp av fjärrskrivbord                                  |
    | NLA Grupprincip                         | Settings\Administrative Templates\Components\Remote för skrivbordssession fjärrskrivbordssession\Säkerhet                                                    | Kräv användarautentisering för fjärråtkomst med hjälp av NLA |
    | Inställningar för keep-alive                      | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Konfigurera intervallet för keep-alive-anslutningen                                                 |
    | Återansluta inställningar                       | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Återansluta automatiskt                                                                   |
    | Begränsat antal anslutningsinställningar | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Begränsa antalet anslutningar                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurera brandväggsregler för Windows
1. Aktivera Windows-brandväggen på de tre profilerna (domän, standard och offentliga):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Kör följande kommando i PowerShell så att WinRM tillåts via tre brandväggsprofiler (domän, privata och offentliga) och aktivera PowerShell fjärrtjänsten:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Aktivera följande brandväggsregler som tillåter RDP-trafik:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Aktivera regeln för fil- och skrivardelning så att den virtuella datorn kan svara på en ping-kommandot i det virtuella nätverket:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Om den virtuella datorn kommer att ingå i en domän, kontrollerar du följande Azure AD-principer för att kontrollera att de tidigare inställningarna inte har återställts. 

    | Mål                                 | Princip                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Aktivera Windows-brandväggen-profiler | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Skydda alla nätverksanslutningar         |
    | Aktiverar du RDP                           | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Tillåt inkommande undantag för fjärrskrivbord |
    |                                      | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows brandvägg | Tillåt inkommande undantag för fjärrskrivbord |
    | Aktivera ICMP-V4                       | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Tillåt ICMP-undantag                   |
    |                                      | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows brandvägg | Tillåt ICMP-undantag                   |

## <a name="verify-the-vm"></a>Kontrollera den virtuella datorn 

Kontrollera att den virtuella datorn är felfri, säker, och RDP som är tillgängliga: 

1. Om du vill kontrollera att disken är felfri och konsekvent, kontrollera disken vid nästa omstart av virtuella datorer:

    ```PowerShell
    Chkdsk /f
    ```
    Kontrollera att rapporten visar en ren och hälsosam disk.

2. Ange inställningar för Boot Configuration Data (BCD). 

    > [!NOTE]
    > Använd en upphöjd PowerShell-kommandotolk för att köra dessa kommandon.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Dump-loggen kan vara användbart vid felsökning av problem för Windows-krascher. Aktivera kraschdump log-samling:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Kontrollera att Windows Management Instrumentation (WMI)-databasen är konsekvent:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Om databasen är skadad, se [WMI: Databasen skadas eller inte](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Kontrollera att inget annat program använder port 3389. Den här porten används för RDP-tjänst i Azure. Om du vill se vilka portar som används på den virtuella datorn kan köra `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Om du vill ladda upp en VHD för Windows är en domänkontrollant:

   * Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) förbereda disken.

   * Kontrollera att du känner till lösenordet för Directory Services återställningsläge (DSRM) om du måste starta den virtuella datorn i DSRM vid en viss tidpunkt. Mer information finns i [ange DSRM-lösenordet](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Kontrollera att du vet det inbyggda administratörskontot och lösenord. Du kanske vill återställa det aktuella lokala administratörslösenordet och se till att du kan använda det här kontot för att logga in på Windows via RDP-anslutningen. Den här behörigheten åtkomst styrs av ”Tillåt inloggning genom Fjärrskrivbordstjänster” grupprincipobjekt. Visa det här objektet i den lokala Redigeraren för här:

    Datorn Datorkonfiguration\Windows Settings\Security Settings\Local principer\Tilldelning av användarrättigheter

8. Kontrollera följande Azure AD-principer för att kontrollera att du inte blockerar RDP-åtkomst via RDP eller från nätverket:

    - Datorkonfiguration\Windows inställningar\Säkerhetsinställningar\Lokala Principer\tilldelning av användarrättigheter\neka åtkomsten till den här datorn från nätverket

    - Datorn Datorkonfiguration\Windows inställningar\Säkerhetsinställningar\Lokala Principer\tilldelning av användarrättigheter\neka inloggning genom Fjärrskrivbordstjänster


9. Kontrollera följande Azure AD-princip för att kontrollera att du inte bort någon av de nödvändiga åtkomstkontona:

   - Datorn Datorkonfiguration\Windows Settings\Security Settings\Local av rättigheter Assignment\Access den här datorn från nätverket

   Principen bör innehålla följande grupper:

   - Administratörer

   - Ansvariga för säkerhetskopiering

   - Alla

   - Användare

10. Starta om den virtuella datorn för att se till att Windows är fortfarande felfri och kan nås via RDP-anslutningen. Nu kan du skapa en virtuell dator i din lokala Hyper-V att kontrollera att den virtuella datorn startar helt. Kontrollera om du kan nå den virtuella datorn via RDP.

11. Ta bort alla extra Transport Driver Interface (TDI)-filter. Till exempel ta bort programvara som analyserar TCP-paket eller extra brandväggar. Om du vill granska det senare kan göra du det efter att den virtuella datorn har distribuerats i Azure.

12. Avinstallera några andra programvara från tredje part eller drivrutin som är relaterade till fysiska komponenter eller någon annan virtualiseringsteknik används.

### <a name="install-windows-updates"></a>Installera Windows-uppdateringar
Helst bör du behålla den datorn uppdaterat den *korrigera nivå*. Om detta inte är möjligt, kontrollera att följande uppdateringar är installerade:

| Komponent               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Nätverk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Fjärrskrivbordstjänster | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Säkerhet                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Avgöra när du ska använda Sysprep <a id="step23"></a>    

Systemförberedelseverktyget (Sysprep) är en process som du kan köra om du vill återställa en Windows-installation. Sysprep ger en ”out of box”-upplevelse genom att ta bort alla personliga data och återställa flera komponenter. 

Du vanligtvis köra Sysprep för att skapa en mall som du kan distribuera flera andra virtuella datorer som har en viss konfiguration. Mallen kallas för en *generaliserad avbildning*.

Om du vill skapa bara en virtuell dator från en disk som du inte behöver använda Sysprep. I stället kan du skapa den virtuella datorn från en *specialiserad avbildning*. Information om hur du skapar en virtuell dator från en särskild disk finns i:

- [Skapa en virtuell dator från en särskild disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en specialiserad virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Om du vill skapa en generaliserad avbildning måste du köra Sysprep. Mer information finns i [så använder du Sysprep: En introduktion](https://technet.microsoft.com/library/bb457073.aspx). 

Inte alla roll eller program som är installerad på en dator med Windows har stöd för generaliserade avbildningar. Så innan du kör den här proceduren, kontrollera att Sysprep stöder rollen på datorn. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalisera en virtuell Hårddisk

>[!NOTE]
> När du har kört `sysprep.exe` i följande steg ska du stänga av den virtuella datorn. Inte aktivera det igen förrän du har skapat en avbildning från den i Azure.

1. Logga in på Windows virtuell dator.
1. Kör **kommandotolk** som administratör. 
1. Ändra katalogen till `%windir%\system32\sysprep`. Kör sedan `sysprep.exe`.
1. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.

    ![Systemförberedelseverktyget](media/prepare-for-upload-vhd-image/syspre.png)
1. I **Avslutningsalternativ**väljer **avstängning**.
1. Välj **OK**.
1. Stäng den virtuella datorn när Sysprep är klar. Använd inte **starta om** att stänga av den virtuella datorn.

Den virtuella Hårddisken är nu redo att laddas upp. Läs mer om hur du skapar en virtuell dator från en generaliserad disk [överföra en generaliserad virtuell Hårddisk och använda den för att skapa en ny virtuell dator i Azure](sa-upload-generalized.md).


>[!NOTE]
> En anpassad *unattend.xml* fil stöds inte. Även om vi har stöd för den `additionalUnattendContent` egenskap som har endast begränsat stöd för att lägga till [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) alternativ till den *unattend.xml* filen som Azure etableringen agenten använder. Du kan använda, till exempel [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) att lägga till FirstLogonCommands och LogonCommands. Mer information finns i [additionalUnattendContent FirstLogonCommands exempel](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Slutför de rekommenderade konfigurationerna
Följande inställningar påverkar inte ladda upp VHD. Men rekommenderar vi starkt att du har konfigurerat dem.

* Installera den [Azure VM Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sedan kan du aktivera VM-tillägg. VM-tillägg implementera de flesta av de viktiga funktioner som du kanske vill använda med dina virtuella datorer. Du måste tillägg, till exempel att återställa lösenord eller konfigurera RDP. Mer information finns i [översikt över Azure VM-agenten](../extensions/agent-windows.md).
* När du skapar den virtuella datorn i Azure rekommenderar vi att du anger växlingsfilen för den *temporala enhetsvolym* att förbättra prestanda. Du kan ställa in filplacering på följande sätt:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Om en datadisk är kopplat till den virtuella datorn, temporal enhet enhetsbeteckning är vanligtvis *D*. Den här beteckning kan vara olika, beroende på dina inställningar och antalet enheter som är tillgängliga.

## <a name="next-steps"></a>Nästa steg
* [Överför en Windows VM-avbildning till Azure för Resource Manager-distributioner](upload-generalized-managed.md)
* [Felsöka problem med aktivering av virtuell Azure Windows-dator](troubleshoot-activation-problems.md)

