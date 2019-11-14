---
title: Förbered en virtuell Windows-hårddisk att ladda upp till Azure
description: Lär dig hur du förbereder en virtuell Windows-disk eller VHDX för att ladda upp det till Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 4b6fea63cd56ddee7eaeaed50a74a01cc8549f0a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032930"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbered en Windows-VHD eller VHDX som ska överföras till Azure

Innan du laddar upp en virtuell Windows-dator (VM) från en lokal plats till Azure måste du förbereda den virtuella hård disken (VHD eller VHDX). Azure stöder virtuella datorer i generation 1 och generation 2 som är i VHD-filformat och har en disk med fast storlek. Den maximala storlek som tillåts för den virtuella hård disken är 1 023 GB. 

I en virtuell dator i generation 1 kan du konvertera ett VHDX-filsystem till en virtuell hård disk. Du kan också konvertera en dynamiskt expanderande disk till en disk med fast storlek. Men du kan inte ändra den virtuella datorns generation. Mer information finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) och [Azure-stöd för virtuella datorer i generation 2 (för hands version)](generation-2.md).

Information om support policyn för virtuella Azure-datorer finns i [Microsoft Server Software support för virtuella Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Anvisningarna i den här artikeln gäller:
>1. 64-bitars versionen av Windows Server 2008 R2 och senare Windows Server-operativsystem. Information om hur du kör ett 32-bitars operativ system i Azure finns i [stöd för 32-bitars operativ system i virtuella Azure-datorer](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Om något katastrof återställnings verktyg används för att migrera arbets belastningen, t. ex. Azure Site Recovery eller Azure Migrate, måste den här processen fortfarande utföras och följas av gäst operativ systemet för att förbereda avbildningen före migreringen.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Konvertera den virtuella disken till en fast storlek och till VHD

Om du behöver konvertera den virtuella disken till det format som krävs för Azure kan du använda någon av metoderna i det här avsnittet:

1. Säkerhetskopiera den virtuella datorn innan du kör konverterings processen för den virtuella disken.

1. Kontrol lera att Windows-VHD fungerar korrekt på den lokala servern. Lös eventuella fel i den virtuella datorn innan du försöker konvertera eller ladda upp den till Azure.

1. Vad gäller storleken på den virtuella hård disken:

   1. Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en rå disk till VHD måste du kontrol lera att storleken på den råa disken är en multipel av 1 MB före konverteringen. Bråk delar av en megabyte ger upphov till fel när avbildningar skapas från den uppladdade virtuella hård disken.

   2. Den största tillåtna storleken för OS-VHD: n är 2 TB.


När du har konverterat disken skapar du en virtuell dator som använder disken. Starta och logga in på den virtuella datorn för att slutföra förberedelserna för uppladdning.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Använd Hyper-V Manager för att konvertera disken 
1. Öppna Hyper-V Manager och välj den lokala datorn till vänster. I menyn ovanför dator listan väljer du **åtgärd** > **Redigera disk**.
2. På sidan **hitta virtuell hård disk** väljer du den virtuella disken.
3. På sidan **Välj åtgärd** väljer du **konvertera** > **Nästa**.
4. Om du behöver konvertera från VHDX väljer du **VHD** > **Nästa**.
5. Om du behöver konvertera från en dynamiskt expanderande disk väljer du **fast storlek** > **Nästa**.
6. Leta upp och välj en sökväg för att spara den nya VHD-filen till.
7. Välj **Slutför**.

> [!NOTE]
> Använd en upphöjd PowerShell-session för att köra kommandona i den här artikeln.

### <a name="use-powershell-to-convert-the-disk"></a>Använd PowerShell för att konvertera disken 
Du kan konvertera en virtuell disk med kommandot [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) i Windows PowerShell. Välj **Kör som administratör** när du startar PowerShell. 

Följande exempel kommando konverterar disken från VHDX till VHD. Kommandot konverterar också disken från en dynamiskt expanderande disk till en disk med fast storlek.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

I det här kommandot ersätter du värdet för `-Path` med sökvägen till den virtuella hård disk som du vill konvertera. Ersätt värdet för `-DestinationPath` med den nya sökvägen till och namnet på den konverterade disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK disk format
Om du har en avbildning av en virtuell Windows-dator i [formatet VMDK](https://en.wikipedia.org/wiki/VMDK)använder du [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) för att konvertera den till VHD-format. Mer information finns i [så här konverterar du en VMware VMDK till Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

Kör följande kommandon från en [upphöjd kommando tolk](https://technet.microsoft.com/library/cc947813.aspx)i den virtuella datorn som du planerar att överföra till Azure:

1. Ta bort alla statiska permanenta vägar i routningstabellen:
   
   * Kör `route print` i kommando tolken om du vill visa routningstabellen.
   * Kontrol lera `Persistence Routes` avsnitt. Om det finns en beständig väg använder du kommandot `route delete` för att ta bort det.
2. Ta bort WinHTTP-proxyn:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Om den virtuella datorn behöver arbeta med en speciell proxy lägger du till ett proxy-undantag till Azure IP-adressen ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) så att den virtuella datorn kan ansluta till Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Ange diskens SAN-princip till [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    I fönstret Öppna kommando tolken skriver du följande kommandon:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ange UTC-tid (Coordinated Universal Time) för Windows. Ange också start typen för Windows tids tjänst (`w32time`) som ska `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ange energi profilen till hög prestanda:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Kontrol lera att miljövariablerna `TEMP` och `TMP` har angetts till sina standardvärden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Kontrol lera Windows-tjänsterna
Kontrol lera att följande Windows-tjänster är inställda på standardvärdena i Windows. De här tjänsterna är de lägsta som måste konfigureras för att säkerställa VM-anslutningen. Kör följande kommandon för att återställa start inställningarna:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```

## <a name="update-remote-desktop-registry-settings"></a>Uppdatera register inställningar för fjärr skrivbord
Kontrol lera att följande inställningar är korrekt konfigurerade för fjärråtkomst:

>[!NOTE] 
>Du kan få ett fel meddelande när du kör `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`. Du kan ignorera det här meddelandet på ett säkert sätt. Det innebär bara att domänen inte skickar den konfigurationen via ett grupprincip-objekt.

1. Remote Desktop Protocol (RDP) är aktiverat:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. RDP-porten har kon figurer ATS korrekt. Standard porten är 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    När du distribuerar en virtuell dator skapas standard reglerna mot port 3389. Om du vill ändra port numret gör du det efter att den virtuella datorn har distribuerats i Azure.

3. Lyssnaren lyssnar i varje nätverks gränssnitt:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Konfigurera NLA-läget (Network-Level Authentication) för RDP-anslutningarna:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Ange värdet för Keep-Alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Återansluta
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Begränsa antalet samtidiga anslutningar:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Ta bort alla självsignerade certifikat som är kopplade till RDP-lyssnaren:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Den här koden säkerställer att du kan ansluta till början när du distribuerar den virtuella datorn. Om du behöver granska detta senare kan du göra det efter att den virtuella datorn har distribuerats i Azure.

9. Om den virtuella datorn kommer att ingå i en domän kontrollerar du följande principer för att se till att de tidigare inställningarna inte har återställts. 
    
    | Mål                                     | Princip                                                                                                                                                       | Värde                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP är aktiverat                           | Dator konfiguration \ konfiguration Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | Tillåt användare att fjärrans luta via fjärr skrivbord                                  |
    | Princip för NLA-grup princip                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | Kräv användarautentisering för fjärråtkomst med hjälp av NLA |
    | Keep-Alive-inställningar                      | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Konfigurera intervall för Keep-Alive-anslutning                                                 |
    | Återanslut inställningar                       | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Återanslut automatiskt                                                                   |
    | Begränsat antal anslutnings inställningar | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Begränsa antalet anslutningar                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurera regler för Windows-brandväggen
1. Aktivera Windows-brandväggen på de tre profilerna (domän, standard och offentligt):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Kör följande kommando i PowerShell för att tillåta WinRM genom de tre brand Väggs profilerna (domän, privat och offentlig) och aktivera PowerShell-fjärrtjänsten:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Aktivera följande brand Väggs regler för att tillåta RDP-trafik:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Aktivera regeln för fil-och skrivar delning så att den virtuella datorn kan svara på ett ping-kommando i det virtuella nätverket:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Om den virtuella datorn kommer att ingå i en domän kontrollerar du följande Azure AD-principer för att kontrol lera att de tidigare inställningarna inte har återställts. 

    | Mål                                 | Princip                                                                                                                                                  | Värde                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Aktivera profiler för Windows-brandväggen | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Skydda alla nätverks anslutningar         |
    | Aktivera RDP                           | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Tillåt inkommande fjärr skrivbords undantag |
    |                                      | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Tillåt inkommande fjärr skrivbords undantag |
    | Aktivera ICMP-v4                       | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Tillåt ICMP-undantag                   |
    |                                      | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Tillåt ICMP-undantag                   |

## <a name="verify-the-vm"></a>Verifiera den virtuella datorn 

Kontrol lera att den virtuella datorn är felfri, säker och RDP-tillgänglig: 

1. Kontrol lera att disken är felfri och konsekvent genom att kontrol lera disken vid nästa omstart av datorn:

    ```PowerShell
    Chkdsk /f
    ```
    Se till att rapporten innehåller en ren och felfri disk.

2. Ange inställningarna för Boot Configuration Data (BCD). 

    > [!NOTE]
    > Använd ett upphöjd PowerShell-fönster för att köra dessa kommandon.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Dump-loggen kan vara till hjälp vid fel sökning av problem med Windows-krasch. Aktivera dump-logg samling:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Kontrol lera att lagrings platsen för Windows Management Instrumentation (WMI) är konsekvent:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Om lagrings platsen är skadad, se [WMI: skadad databas eller inte](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Se till att inga andra program använder port 3389. Den här porten används för RDP-tjänsten i Azure. Om du vill se vilka portar som används på den virtuella datorn kör `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Ladda upp en virtuell Windows-hårddisk som är en domänkontrollant:

   * Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) för att förbereda disken.

   * Se till att du känner till DSRM-lösenordet (Directory Services Restore Mode) om du måste starta den virtuella datorn i DSRM-läge. Mer information finns i [Ange ett DSRM-lösenord](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Se till att du känner till det inbyggda administratörs kontot och lösen ordet. Du kanske vill återställa det aktuella lokala administratörs lösen ordet och se till att du kan använda det här kontot för att logga in i Windows via RDP-anslutningen. Åtkomst behörigheten styrs av grupprincip-objektet "Tillåt inloggning via Fjärrskrivbordstjänster". Visa det här objektet i redigerare för lokalt grupprincipobjekt här:

    Dator konfiguration \ säkerhets inställningar \ lokala Principer\tilldelning Rights Assignment

8. Kontrol lera följande Azure AD-principer för att se till att du inte blockerar RDP-åtkomst via RDP eller från nätverket:

    - Dator konfiguration \ säkerhets inställningar \ lokala Principer\tilldelning rättigheter \ neka åtkomst till den här datorn från nätverket

    - Dator konfiguration \ säkerhets inställningar \ lokala Principer\tilldelning rättigheter \ neka logga in via Fjärrskrivbordstjänster


9. Kontrol lera följande Azure AD-princip för att se till att du inte tar bort några av de nödvändiga åtkomst kontona:

   - Dator konfiguration \ säkerhets inställningar \ lokala Principer\tilldelning rättigheter Assignment\Access den här datorn från nätverket

   Principen ska lista följande grupper:

   - Administratörer

   - Ansvariga för säkerhets kopiering

   - Grupperna

   - Användare

10. Starta om den virtuella datorn för att kontrol lera att Windows fortfarande är felfritt och kan nås via RDP-anslutningen. Nu kanske du vill skapa en virtuell dator i din lokala Hyper-V för att se till att den virtuella datorn startar helt. Testa sedan för att kontrol lera att du kan komma åt den virtuella datorn via RDP.

11. Ta bort alla filter för filter för extra transport driv Rutins gränssnitt (TDI). Ta till exempel bort program vara som analyserar TCP-paket eller extra brand väggar. Om du behöver granska detta senare kan du göra det efter att den virtuella datorn har distribuerats i Azure.

12. Avinstallera eventuella program från tredje part eller driv rutin som är relaterade till fysiska komponenter eller någon annan virtualiseringsteknik.

### <a name="install-windows-updates"></a>Installera Windows-uppdateringar
Helst bör du hålla datorn uppdaterad på *korrigerings nivå*. Om detta inte är möjligt kontrollerar du att följande uppdateringar är installerade:

| Komponent               | Binär         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8,1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi. dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase. dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Nätverk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Kärna                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Fjärrskrivbordstjänster | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Rdpwd. sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Säkerhet                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> För att undvika en oavsiktlig omstart under VM-etablering rekommenderar vi att du ser till att alla Windows Update installationer är klara och att inga uppdateringar väntar. Ett sätt att göra detta är att installera alla möjliga Windows-uppdateringar och starta om en gång innan du kör Sysprep-kommandot.

### Avgöra när Sysprep ska användas<a id="step23"></a>    

Sysprep (System Preparation Tool) är en process som du kan köra för att återställa en Windows-installation. Sysprep tillhandahåller en "direkt användning"-upplevelse genom att ta bort all personlig information och återställa flera komponenter. 

Du kör vanligt vis Sysprep för att skapa en mall som du kan använda för att distribuera flera andra virtuella datorer som har en speciell konfiguration. Mallen kallas en *generaliserad avbildning*.

Om du bara vill skapa en virtuell dator från en disk behöver du inte använda Sysprep. I stället kan du skapa den virtuella datorn från en *specialiserad avbildning*. Information om hur du skapar en virtuell dator från en specialiserad disk finns i:

- [Skapa en virtuell dator från en specialiserad disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en specialiserad virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Om du vill skapa en generaliserad avbildning måste du köra Sysprep. Mer information finns i [så här använder du Sysprep: en introduktion](https://technet.microsoft.com/library/bb457073.aspx). 

Alla roller och program som är installerade på en Windows-baserad dator stöder inte generaliserade avbildningar. Innan du kör den här proceduren ser du till att Sysprep stöder datorns roll. Mer information finns i [Sysprep-stöd för Server roller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalisera en virtuell hård disk

>[!NOTE]
> När du har kört `sysprep.exe` i följande steg stänger du av den virtuella datorn. Aktivera inte det igen förrän du har skapat en avbildning från den i Azure.

1. Logga in på den virtuella Windows-datorn.
1. Kör **kommando tolken** som administratör. 
1. Ändra katalogen till `%windir%\system32\sysprep`. Kör sedan `sysprep.exe`.
1. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.

    ![System förberedelse verktyg](media/prepare-for-upload-vhd-image/syspre.png)
1. I **avslutnings alternativ**väljer du **Stäng**av.
1. Välj **OK**.
1. När Sysprep är klar stänger du den virtuella datorn. Använd inte **omstart** för att stänga av den virtuella datorn.

Nu kan den virtuella hård disken laddas upp. Mer information om hur du skapar en virtuell dator från en generaliserad disk finns i [överföra en generaliserad virtuell hård disk och använda den för att skapa en ny virtuell dator i Azure](sa-upload-generalized.md).


>[!NOTE]
> Det finns inte stöd för en anpassad *Unattend. XML-* fil. Även om vi har stöd för egenskapen `additionalUnattendContent`, som endast ger begränsat stöd för att lägga till [Microsoft-Windows-Shell-Setup-](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) alternativ i filen *Unattend. XML* som Azure Provisioning-agenten använder. Du kan till exempel använda [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) för att lägga till FirstLogonCommands och LogonCommands. Mer information finns i [AdditionalUnattendContent FirstLogonCommands-exempel](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Slutför de rekommenderade konfigurationerna
Följande inställningar påverkar inte VHD-uppladdning. Vi rekommenderar dock starkt att du har konfigurerat dem.

* Installera [Azure Virtual Machine-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sedan kan du aktivera VM-tillägg. VM-tilläggen implementerar de flesta av de kritiska funktioner som du kanske vill använda med dina virtuella datorer. Du behöver tillägg, till exempel för att återställa lösen ord eller konfigurera RDP. Mer information finns i [Översikt över Azure Virtual Machine agent](../extensions/agent-windows.md).
* När du har skapat den virtuella datorn i Azure rekommenderar vi att du sätter växlings filen på den *temporala volym volymen* för att förbättra prestandan. Du kan ställa in fil placeringen på följande sätt:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Om en datadisk är ansluten till den virtuella datorn är den temporala enhets volymens beteckning normalt *D*. Den här beteckningen kan vara olika beroende på dina inställningar och antalet tillgängliga enheter.
  * Vi rekommenderar att du inaktiverar skript block som kan tillhandahållas av antivirus program. De kan störa och blockera Windows Provisioning agent-skript som körs när du distribuerar en ny virtuell dator från avbildningen.
  
## <a name="next-steps"></a>Nästa steg
* [Ladda upp en Windows VM-avbildning till Azure för Resource Manager-distributioner](upload-generalized-managed.md)
* [Felsök problem med aktivering av virtuella Azure Windows-datorer](troubleshoot-activation-problems.md)

