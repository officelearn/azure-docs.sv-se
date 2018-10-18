---
title: Förbereda en Windows virtuell Hårddisk för överföring till Azure | Microsoft Docs
description: Så här förbereder du en Windows-VHD eller VHDX innan du laddar upp till Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/10/2018
ms.author: genli
ms.openlocfilehash: f9b950b1d85f50331d556a54b4237d78ec5c07ac
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388183"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbereda en Windows-VHD eller VHDX för att överföra till Azure
Innan du överför en Windows-dator (VM) från en lokal plats till Microsoft Azure, måste du förbereda den virtuella hårddisken (VHD eller VHDX). Azure stöder **endast 1 virtuella datorer i generation** som är i VHD-format och har en fast storlek disk. Den maximala storleken som tillåts för den virtuella Hårddisken är 1,023 GB. Du kan konvertera en generation 1 VM från VHDX filsystemet till virtuell Hårddisk och från en dynamiskt expanderande disk till fast storlek. Men du kan inte ändra en virtuell dator generation. Mer information finns i [bör jag skapa en generation 1 eller 2 virtuella datorer i Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Läs mer om principen för support för Azure VM, [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Anvisningar i den här artikeln gäller för 64-bitarsversionen av Windows Server 2008 R2 och senare Windows server-operativsystem. Information om pågående 32-bitars version av operativsystemet i Azure finns i [stöd för 32-bitars operativsystem i Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Konvertera den virtuella disken till VHD- och disk med fast storlek 
Om du vill konvertera din virtuella hårddisk till formatet som krävs för Azure kan du använda någon av metoderna i det här avsnittet. Säkerhetskopiera den virtuella datorn innan du kör konverteringsprocessen virtuell disk och se till att fungerar Windows-VHD: N korrekt på den lokala servern. Åtgärda eventuella fel i Virtuellt datorn innan du försöker konvertera eller överför den till Azure.

När du konverterar disken kan du skapa en virtuell dator som använder den konvertera disken. Starta och logga in på den virtuella datorn och slutför förbereda den virtuella datorn för att ladda upp.

### <a name="convert-disk-using-hyper-v-manager"></a>Konvertera disk med hjälp av Hyper-V Manager
1. Öppna Hyper-V Manager och välj din lokala dator till vänster. I menyn ovanför datorlistan klickar du på **åtgärd** > **redigera Disk**.
2. På den **hitta virtuell hårddisk** skärmen, leta upp och välj din virtuella hårddisk.
3. På den **Välj åtgärd** skärmen och välj sedan **konvertera** och **nästa**.
4. Om du vill konvertera från VHDX Välj **VHD** och klicka sedan på **nästa**.
5. Om du vill konvertera från en dynamiskt expanderande disk kan välja **fast storlek** och klicka sedan på **nästa**.
6. Leta upp och välj en sökväg för att spara den nya VHD-filen till.
7. Klicka på **Slutför**.

>[!NOTE]
>Kommandona i den här artikeln måste köras på en upphöjd PowerShell-session.

### <a name="convert-disk-by-using-powershell"></a>Konvertera disk med hjälp av PowerShell
Du kan konvertera en virtuell disk med hjälp av den [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) i Windows PowerShell. Välj **kör som administratör** när du startar PowerShell. 

Följande exempelkommando konverterar från VHDX till VHD och från en dynamiskt expanderande disk till fast storlek:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
I det här kommandot ersätter du värdet för ”-sökvägen” med sökvägen till den virtuella hårddisken som du vill konvertera och värdet för ”-DestinationPath” med den nya sökvägen och namnet på den konvertera disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK diskformat
Om du har en Windows VM-avbildning i den [VMDK-filformat](https://en.wikipedia.org/wiki/VMDK), konvertera den till en virtuell Hårddisk med hjälp av den [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). Mer information finns i bloggen artikeln [så här konverterar du en VMware VMDK till VHD för Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

På den virtuella datorn som du planerar att ladda upp till Azure, köra alla kommandon i följande steg från ett [kommandotolk](https://technet.microsoft.com/library/cc947813.aspx):

1. Ta bort alla permanent statisk väg i routningstabellen:
   
   * Om du vill visa routningstabellen kör `route print` i Kommandotolken.
   * Kontrollera den **Persistence vägar** avsnitt. Om det finns en beständig väg, använda den **route delete** kommando för att ta bort den.
2. Ta bort WinHTTP-proxyservern:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Ange disken SAN-principen för [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    I fönstret Öppna Kommandotolken skriver du följande kommandon:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ange tiden för Coordinated Universal Time (UTC) för Windows och starttypen för tjänsten Windows Time (w32time) till **automatiskt**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ange power-profil den **högpresterande**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Se till att miljövariabler **TEMP** och **TMP** ställs till sina standardvärden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Kontrollera Windows-tjänster
Se till att var och en av följande Windows-tjänster har angetts till den **Windows standardvärdena**. Det här är det minsta antalet tjänster som måste ställas in för att se till att den virtuella datorn är ansluten. Om du vill återställa inställningar för Start, kör du följande kommandon:
   
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

## <a name="update-remote-desktop-registry-settings"></a>Uppdatera registerinställningar för fjärrskrivbord
Kontrollera att följande inställningar är korrekt konfigurerade för anslutning till fjärrskrivbord:

>[!Note] 
>Du får ett felmeddelande när du kör den **Set-itemproperty-egenskap-sökvägen ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal tjänster - namnet &lt;objektnamn&gt; -värdet &lt;värdet&gt;**  i dessa steg. Felmeddelandet kan ignoreras. Det innebär bara att domänen inte är push-överföra den konfigurationen via ett grupprincipobjekt.
>
>

1. Remote Desktop Protocol (RDP) är aktiverat:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP-porten är korrekt konfigurerad (standard port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    När du distribuerar en virtuell dator skapas standardreglerna mot port 3389. Om du vill ändra portnumret kan du göra det efter att den virtuella datorn har distribuerats i Azure.

3. Lyssnaren lyssnar i varje nätverksgränssnitt:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Konfigurera autentisering på nätverksnivå-läge för RDP-anslutningar:
   
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
8. Om det finns något självsignerat certifikat som är kopplad till RDP-lyssnaren kan du ta bort dem:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Detta är att se till att du kan ansluta i början när du distribuerar den virtuella datorn. Du kan också använda detta i ett senare skede när den virtuella datorn har distribuerats i Azure om det behövs.

9. Om den virtuella datorn kommer att ingå i en domän, kontrollerar du alla följande inställningar för att se till att de tidigare inställningarna inte återställs. De principer som måste vara markerad är följande:
    
    | Mål                                     | Princip                                                                                                                                                       | Värde                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP har aktiverats                           | Datorkonfiguration\Principer\Windows Settings\Administrative Templates\Components\Remote för fjärrskrivbordssession\Anslutningar         | Tillåt användare att ansluta via en fjärranslutning med hjälp av fjärrskrivbord                                  |
    | NLA Grupprincip                         | Settings\Administrative Templates\Components\Remote för skrivbordssession fjärrskrivbordssession\Säkerhet                                                    | Kräv användaren autentisering för fjärranslutningar med autentisering på nätverksnivå |
    | Behålla Alive-inställningar                      | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Konfigurera intervallet för keep-alive-anslutningen                                                 |
    | Återansluta inställningar                       | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Automatisk återanslutning                                                                   |
    | Begränsa antalet anslutningsinställningar | Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar | Begränsa antalet anslutningar                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurera brandväggsregler för Windows
1. Aktivera Windows-brandväggen på de tre profilerna (domän, Standard och offentlig):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Kör följande kommando i PowerShell för att tillåta WinRM via tre brandväggsprofiler (domän, privata och offentliga) och aktivera PowerShell Remote-tjänsten:
   
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
5. Om den virtuella datorn kommer att ingå i en domän, kontrollerar du följande inställningar för att se till att de tidigare inställningarna inte återställs. AD-principer som måste vara markerad är följande:

    | Mål                                 | Princip                                                                                                                                                  | Värde                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Aktivera Windows-brandväggen-profiler | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Skydda alla nätverksanslutningar         |
    | Aktiverar du RDP                           | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Tillåt inkommande undantag för fjärrskrivbord |
    |                                      | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows brandvägg | Tillåt inkommande undantag för fjärrskrivbord |
    | Aktivera ICMP-V4                       | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows brandvägg   | Tillåt ICMP-undantag                   |
    |                                      | Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows brandvägg | Tillåt ICMP-undantag                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Kontrollera den virtuella datorn är felfri, säker och tillgänglig med RDP 
1. Om du vill kontrollera att disken är felfri och konsekvent, kör du en disk kontrollåtgärd vid nästa omstart av virtuella datorer:

    ```PowerShell
    Chkdsk /f
    ```
    Kontrollera att rapporten visar en ren och hälsosam disk.

2. Ange inställningar för Boot Configuration Data (BCD). 

    > [!Note]
    > Kontrollera att du kör dessa kommandon på en upphöjd PowerShell-kommandotolk.
   
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
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Kontrollera att Windows Management Instrumentation-lagringsplatsen är konsekvent. Om du vill göra detta kör du följande kommando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Om databasen är skadad, se [WMI: databasen skadas eller inte](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Se till att alla andra program inte använder port 3389. Den här porten används för RDP-tjänst i Azure. Du kan köra **netstat - anob** vill se vilka portar som i används på den virtuella datorn:

    ```PowerShell
    netstat -anob
    ```

6. Om Windows-VHD som du vill ladda upp är en domänkontrollant, följer du dessa steg:

    1. Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) förbereda disken.

    1. Se till att du vet DSRM-lösenordet om du måste starta den virtuella datorn i DSRM vid en viss tidpunkt. Du kanske vill referera till den här länken för att den [DSRM-lösenordet](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Se till att det inbyggda administratörskontot och lösenordet är kända för dig. Du kanske vill återställa det aktuella lösenordet för lokal administratör och se till att du använder det här kontot för att logga in på Windows via RDP-anslutningen. Den här behörigheten åtkomst styrs av grupprincipobjektet ”Tillåt inloggning genom Fjärrskrivbordstjänster”. Du kan visa det här objektet i den lokala Redigeraren för under:

    Datorn Datorkonfiguration\Windows Settings\Security Settings\Local principer\Tilldelning av användarrättigheter

8. Kontrollera följande AD-principer för att se till att du inte blockerar RDP-åtkomst via RDP eller från nätverket:

    - Datorkonfiguration\Windows inställningar\Säkerhetsinställningar\Lokala Principer\tilldelning av användarrättigheter\neka åtkomsten till den här datorn från nätverket

    - Datorn Datorkonfiguration\Windows inställningar\Säkerhetsinställningar\Lokala Principer\tilldelning av användarrättigheter\neka inloggning genom Fjärrskrivbordstjänster


9. Kontrollera följande AD-princip för att se till att du inte tar bort någon av följande obligatoriska åtkomstkonton:

    - Datorn Datorkonfiguration\Windows Settings\Security Settings\Local av rättigheter Assignment\Access den här beräkningen från nätverket

    Följande grupper bör visas på den här principen:

    - Administratörer
    - Ansvariga för säkerhetskopiering
    - Alla
    - Användare

10. Starta om den virtuella datorn och kontrollera att Windows är fortfarande felfri kan nås med hjälp av RDP-anslutning. Nu kan du skapa en virtuell dator i din lokala Hyper-V att kontrollera att den virtuella datorn startar helt och sedan testa om det är RDP kan nås.

11. Ta bort några extra Transport Driver Interface-filter, t.ex programvara som analyserar TCP-paket eller extra brandväggar. Du kan också använda detta i ett senare skede när den virtuella datorn har distribuerats i Azure om det behövs.

12. Avinstallera någon annan programvara från tredje part eller drivrutin som är relaterat till fysiska komponenter eller någon annan virtualiseringsteknik används.

### <a name="install-windows-updates"></a>Installera Windows-uppdateringar
Konfigurationen som är bäst är att **har korrigeringsnivån för datorn senast**. Om det inte är möjligt, kontrollerar du att följande uppdateringar är installerade:

| Komponent               | Binär         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10-Version 1607Windows Server 2016-Version 1607 | Windows 10, version 1703    | Windows 10 1709 Windows Server 2016-Version 1709 | Windows 10 1803Windows Server 2016-Version 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | MSDSM.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Nätverk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | Mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | HTTP.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Kärna                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Fjärrskrivbordstjänster | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | TERMSRV.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Säkerhet                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### När du ska använda sysprep <a id="step23"></a>    

Sysprep är en process som du kan köra i en windows-installation som återställs installationen av systemet och ger en ”direkt ur lådan uppleva” genom att ta bort alla personliga data och återställa flera komponenter. Gör du vanligtvis det om du vill skapa en mall som du kan distribuera flera andra virtuella datorer som har en viss konfiguration. Detta kallas en **generaliserad avbildning**.

Om du vill i stället bara att skapa en virtuell dator från en disk, har du inte använda sysprep. I så fall kan du bara skapa den virtuella datorn från som kallas en **specialiserad avbildning**.

Mer information om hur du skapar en virtuell dator från en särskild disk finns:

- [Skapa en virtuell dator från en särskild disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en specialiserad virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Om du vill skapa en generaliserad avbildning måste du köra sysprep. Mer information om Sysprep finns i [så här använder du Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx). 

Inte alla roll eller program som är installerad på en Windows-baserad dator stöder tack vare generaliseringen. Så innan du kör den här proceduren finns i följande artikel för att se till att rollen för den datorn stöds av sysprep. Mer information [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Steg för att generalisera en virtuell Hårddisk

>[!NOTE]
> När du har kört sysprep.exe som anges i följande steg, Stäng av den virtuella datorn och inte aktiverar det igen förrän du har skapat en avbildning från den i Azure.

1. Logga in på Windows virtuell dator.
2. Kör **kommandotolk** som administratör. 
3. Ändra katalogen till: **%windir%\system32\sysprep**, och kör sedan **sysprep.exe**.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.

    ![Systemförberedelseverktyget](media/prepare-for-upload-vhd-image/syspre.png)
4. I **Avslutningsalternativ**väljer **avstängning**.
5. Klicka på **OK**.
6. Stäng den virtuella datorn när Sysprep har slutförts. Använd inte **starta om** att stänga av den virtuella datorn.
7. Den virtuella Hårddisken är nu redo att laddas upp. Läs mer om hur du skapar en virtuell dator från en generaliserad disk [överföra en generaliserad virtuell Hårddisk och använda den för att skapa en nya virtuella datorer i Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Ange rekommenderade konfigurationen
Följande inställningar påverkar inte ladda upp VHD. Men rekommenderar vi starkt att du har konfigurerat dem.

* Installera den [virtuella Azure-datorer agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sedan kan du aktivera VM-tillägg. VM-tillägg implementera de flesta av de viktiga funktioner som du kanske vill använda med dina virtuella datorer, som att återställa lösenord, konfigurera RDP och så vidare. Mer information finns i:

    - [VM-agenten och tillägg – del 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [VM-agenten och tillägg – del 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)

*  När den virtuella datorn har skapats i Azure, rekommenderar vi att du anger växlingsfilen för ”Temporala” enheten att förbättra prestanda. Du kan ställa in detta på följande sätt:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile" -Type MultiString -force
    ```
Om det finns någon datadisk som är kopplad till den virtuella datorn, är Temporal enhet enhetsbeteckning vanligtvis ”d”. Den här beteckning kan vara olika, beroende på antalet enheter som är tillgängliga och de inställningar som du gör.

## <a name="next-steps"></a>Nästa steg
* [Överför en Windows VM-avbildning till Azure för Resource Manager-distributioner](upload-generalized-managed.md)
* [Felsöka problem med Windows Azure VM-aktivering](troubleshoot-activation-problems.md)

