---
title: Förbereda en Virtuell Windows-hårddisk för att ladda upp till Azure
description: Lär dig hur du förbereder en Windows VHD eller VHDX för att ladda upp den till Azure
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
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250197"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbereda en VHD eller VHDX i Windows för överföring till Azure

Innan du laddar upp en virtuell Dator (VM) från lokalt till Azure måste du förbereda den virtuella hårddisken (VHD eller VHDX). Azure stöder både generation 1 och generation 2 virtuella datorer som är i VHD-filformat och som har en disk med fast storlek. Den maximala storleken för den virtuella hårddisken är 1 023 GB. 

I en generation 1 virtuell dator kan du konvertera ett VHDX-filsystem till VHD. Du kan också konvertera en dynamiskt expanderande disk till en disk med fast storlek. Men du kan inte ändra en virtuell dators generation. Mer information finns i [Ska jag skapa en generation 1 eller 2 virtuell dator i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) och [Azure-stöd för generation 2 virtuella datorer (förhandsversion)](generation-2.md).

Information om supportprincipen för virtuella Azure-datorer finns i [Stöd för Microsoft-serverprogramvara för virtuella Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Instruktionerna i den här artikeln gäller för:
>1. 64-bitarsversionen av Windows Server 2008 R2 och senare Windows Server-operativsystem. Information om hur du kör ett 32-bitars operativsystem i Azure finns [i Support för 32-bitars operativsystem i virtuella Azure-datorer](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Om något verktyg för haveriberedskap används för att migrera arbetsbelastningen, till exempel Azure Site Recovery eller Azure Migrate, krävs den här processen fortfarande att göra och följas på gästoperativsystemet för att förbereda avbildningen före migreringen.

## <a name="system-file-checker-sfc-command"></a>SFC-kommando (System File Checker)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Kör verktyget Windows SystemFilskontroll (kör sfc /scannow) på OS före generaliseringssteget för att skapa kund-OS-avbildning

Kommandot Systemfilskontroll (SFC) används för att verifiera och ersätta Windows-systemfiler.

Så här kör du kommandot SFC:

1. Öppna en upphöjd CMD-prompt som administratör.
1. Skriv `sfc /scannow` och välj **Retur**.

    ![ systemfilskontroll](media/prepare-for-upload-vhd-image/system-file-checker.png)


När SFC-genomsökningen är klar försöker du installera Windows-uppdateringar och starta om datorn.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Konvertera den virtuella disken till en fast storlek och till virtuell hårddisk

Om du behöver konvertera den virtuella disken till det format som krävs för Azure använder du någon av metoderna i det här avsnittet:

1. Säkerhetskopiera den virtuella datorn innan du kör konverteringsprocessen för virtuell disk.

1. Kontrollera att Windows VHD fungerar korrekt på den lokala servern. Lös eventuella fel i själva den virtuella datorn innan du försöker konvertera eller överföra den till Azure.

1. När det gäller storleken på den virtuella hårddisken:

   1. Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att rådiskstorleken är en multipel av 1 MB före konvertering. Bråkdelar av en megabyte orsakar fel när du skapar bilder från den uppladdade virtuella hårddisken.

   2. Den maximala storleken som tillåts för OS VHD är 2TB.


När du har konverterat disken skapar du en virtuell dator som använder disken. Starta och logga in på den virtuella datorn för att slutföra förberedelserna för uppladdning.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Använda Hyper-V-hanteraren för att konvertera disken 
1. Öppna Hyper-V Manager och välj din lokala dator till vänster. Välj > **Åtgärdredigeringsdisk**på **Action**menyn ovanför datorlistan .
2. Välj din virtuella disk på sidan **Hitta virtuell hårddisk.**
3. På sidan **Välj åtgärd** väljer du **Konvertera** > **nästa**.
4. Om du behöver konvertera från VHDX väljer du **VHD** > **Next**.
5. Om du behöver konvertera från en dynamiskt expanderande disk väljer du **Fast storlek** > **Nästa**.
6. Leta reda på och markera en sökväg som den nya VHD-filen ska sparas på.
7. Välj **Slutför**.

> [!NOTE]
> Använd en upphöjd PowerShell-session för att köra kommandona i den här artikeln.

### <a name="use-powershell-to-convert-the-disk"></a>Använda PowerShell för att konvertera disken 
Du kan konvertera en virtuell disk med kommandot [Konvertera-VHD](https://technet.microsoft.com/library/hh848454.aspx) i Windows PowerShell. Välj **Kör som administratör** när du startar PowerShell. 

Följande exempelkommando konverterar disken från VHDX till VHD. Kommandot konverterar också disken från en dynamiskt expanderande disk till en disk med fast storlek.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

I det här kommandot `-Path` ersätter du värdet för med sökvägen till den virtuella hårddisk som du vill konvertera. Ersätt värdet `-DestinationPath` för med den nya sökvägen och namnet på den konverterade disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK-diskformat
Om du har en Windows VM-avbildning i [VMDK-filformatet](https://en.wikipedia.org/wiki/VMDK)använder du [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) för att konvertera den till VHD-format. Mer information finns i [Så här konverterar du en VIRTUELLVMware-VMDK till Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

> [!NOTE]
> Azure-plattformen monterar en ISO-fil till DVD-ROM-skivan när en Windows VM skapas från en generaliserad avbildning.
> Därför måste DVD-ROM-skivan vara aktiverad i operativsystemet i den generaliserade avbildningen. Om den är inaktiverad kommer Windows VM att fastna på OOBE.

På den virtuella datorn som du planerar att överföra till Azure kör du följande kommandon från ett [upphöjd kommandotolksfönster:](https://technet.microsoft.com/library/cc947813.aspx)

1. Ta bort alla statiska beständiga vägar i routningstabellen:
   
   * Om du vill visa `route print` flödestabellen körs du i kommandotolken.
   * Kontrollera `Persistence Routes` avsnitten. Om det finns en beständig `route delete` väg använder du kommandot för att ta bort den.
2. Ta bort WinHTTP-proxyn:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Om den virtuella datorn behöver arbeta med en viss proxy lägger du till ett proxyundantag till Azure IP-adressen ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) så att den virtuella datorn kan ansluta till Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Ange att disk-SAN-principen ska: [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)
   
    ```PowerShell
    diskpart 
    ```
    Skriv följande kommandon i det öppna kommandotolksfönstret:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ange utc-tid (Coordinated Universal Time) för Windows. Ställ också in starttypen för`w32time`Windows `Automatic`tidstjänst ( ) till:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ställ in effektprofilen på hög prestanda:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Kontrollera att miljövariablerna `TEMP` och `TMP` är inställda på deras standardvärden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Kontrollera Windows-tjänsterna
Kontrollera att var och en av följande Windows-tjänster är inställd på Windows-standardvärdena. Dessa tjänster är det minimum som måste ställas in för att säkerställa vm-anslutning. Om du vill återställa startinställningarna kör du följande kommandon:
   
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
## <a name="update-remote-desktop-registry-settings"></a>Uppdatera registerinställningar för fjärrskrivbord
Kontrollera att följande inställningar är korrekt konfigurerade för fjärråtkomst:

>[!NOTE] 
>Ett felmeddelande kan visas när `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`du kör . Du kan ignorera det här meddelandet. Det betyder bara att domänen inte driver den konfigurationen genom ett grupprincipobjekt.

1. RDP (Remote Desktop Protocol) är aktiverat:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. RDP-porten är korrekt inställd. Standardporten är 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    När du distribuerar en virtuell dator skapas standardreglerna mot port 3389. Om du vill ändra portnumret gör du det när den virtuella datorn har distribuerats i Azure.

3. Lyssnaren lyssnar i alla nätverksgränssnitt:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Konfigurera NLA-läget (Network-level Authentication) för RDP-anslutningar:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Ange värdet för att hålla liv:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Återansluta:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Begränsa antalet samtidiga anslutningar:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Ta bort alla självsignerade certifikat som är knutna till RDP-lyssnaren:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Den här koden säkerställer att du kan ansluta i början när du distribuerar den virtuella datorn. Om du behöver granska detta senare kan du göra det när den virtuella datorn har distribuerats i Azure.

9. Om den virtuella datorn ska ingå i en domän kontrollerar du följande principer för att se till att de tidigare inställningarna inte återställs. 
    
    | Mål                                     | Princip                                                                                                                                                       | Värde                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP är aktiverat                           | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Anslutningar         | Tillåt användare att fjärransluta med fjärrskrivbord                                  |
    | NLA-grupprincip                         | Inställningar\Administrativa mallar\Komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Säkerhet                                                    | Kräv användarautentisering för fjärråtkomst med hjälp av NLA |
    | Inställningar för Keep-alive                      | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Anslutningar | Konfigurera anslutningsintervall för keep-alive                                                 |
    | Återanslut inställningar                       | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Anslutningar | Återanslut automatiskt                                                                   |
    | Begränsat antal anslutningsinställningar | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värd för fjärrskrivbordssession\Anslutningar | Begränsa antalet anslutningar                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurera windows-brandväggsregler
1. Aktivera Windows-brandväggen på de tre profilerna (domän, standard och offentlig):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Kör följande kommando i PowerShell för att tillåta WinRM genom de tre brandväggsprofilerna (domän, privat och offentlig) och aktivera PowerShell-fjärrtjänsten:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Aktivera följande brandväggsregler för att tillåta RDP-trafik:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Aktivera regeln för fil- och skrivardelning så att den virtuella datorn kan svara på ett ping-kommando i det virtuella nätverket:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Skapa en regel för Azure-plattformsnätverket:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Om den virtuella datorn ska ingå i en domän kontrollerar du följande Azure AD-principer för att se till att de tidigare inställningarna inte återställs. 

    | Mål                                 | Princip                                                                                                                                                  | Värde                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Aktivera Windows-brandväggsprofilerna | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Nätverk\Nätverksanslutning\Windows-brandväggen\Domänprofil\Windows-brandväggen   | Skydda alla nätverksanslutningar         |
    | Aktivera RDP                           | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Nätverk\Nätverksanslutning\Windows-brandväggen\Domänprofil\Windows-brandväggen   | Tillåt undantag för inkommande fjärrskrivbord |
    |                                      | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Nätverk\Nätverksanslutning\Windows-brandväggen\Standardprofil\Windows-brandväggen | Tillåt undantag för inkommande fjärrskrivbord |
    | Aktivera ICMP-V4                       | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Nätverk\Nätverksanslutning\Windows-brandväggen\Domänprofil\Windows-brandväggen   | Tillåt ICMP-undantag                   |
    |                                      | Datorkonfiguration\Principer\Windows-inställningar\Administrativa mallar\Nätverk\Nätverksanslutning\Windows-brandväggen\Standardprofil\Windows-brandväggen | Tillåt ICMP-undantag                   |

## <a name="verify-the-vm"></a>Verifiera den virtuella datorn 

Kontrollera att den virtuella datorn är felfri, säker och RDP tillgänglig: 

1. Kontrollera disken vid nästa omstart av den virtuella datorn för att se till att disken är felfri och konsekvent:

    ```PowerShell
    Chkdsk /f
    ```
    Kontrollera att rapporten visar en ren och felfri disk.

2. Ange inställningarna för startkonfigurationsdata (BCD). 

    > [!NOTE]
    > Använd ett upphöjt PowerShell-fönster för att köra dessa kommandon.
   
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
3. Dumploggen kan vara till hjälp vid felsökning av Windows-kraschproblem. Aktivera dumploggsamlingen:

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
4. Kontrollera att WMI-databasen (Windows Management Instrumentation) är konsekvent:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Om databasen är skadad läser du [WMI: Data från databasen skadas eller inte](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Kontrollera att inget annat program använder port 3389. Den här porten används för RDP-tjänsten i Azure. Om du vill se vilka portar som används på den virtuella datorn kör du: `netstat -anob`

    ```PowerShell
    netstat -anob
    ```

6. Så här laddar du upp en Virtuell Windows-hårddisk som är en domänkontrollant:

   * Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) för att förbereda disken.

   * Se till att du känner till DSRM-lösenordet (Directory Services Restore Mode) om du måste starta den virtuella datorn i DSRM någon gång. Mer information finns i [Ange ett DSRM-lösenord](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Se till att du känner till det inbyggda administratörskontot och lösenordet. Du kanske vill återställa det aktuella lokala administratörslösenordet och se till att du kan använda det här kontot för att logga in i Windows via RDP-anslutningen. Den här åtkomstbehörigheten styrs av grupprincipobjektet "Tillåt inloggning via Fjärrskrivbordstjänster". Visa det här objektet i redigeraren för lokala grupprinciper här:

    Datorkonfiguration\Windows-inställningar\Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter

8. Kontrollera följande Azure AD-principer för att se till att du inte blockerar din RDP-åtkomst via RDP eller från nätverket:

    - Datorkonfiguration\Windows-inställningar\Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter\Neka åtkomst till den här datorn från nätverket

    - Datorkonfiguration\Windows-inställningar\Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter\Neka inloggning via fjärrskrivbordstjänster


9. Kontrollera följande Azure AD-princip för att se till att du inte tar bort något av de nödvändiga åtkomstkontona:

   - Datorkonfiguration\Windows-inställningar\Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter\Åtkomst till den här datorn från nätverket

   Principen bör lista följande grupper:

   - Administratörer

   - Ansvariga för säkerhetskopiering

   - Alla

   - Användare

10. Starta om den virtuella datorn för att se till att Windows fortfarande är felfritt och kan nås via RDP-anslutningen. Nu kanske du vill skapa en virtuell dator i din lokala Hyper-V för att se till att den virtuella datorn startar helt. Testa sedan för att se till att du kan nå den virtuella datorn via RDP.

11. Ta bort eventuella extra TDI-filter (Transport Driver Interface). Ta till exempel bort programvara som analyserar TCP-paket eller extra brandväggar. Om du behöver granska detta senare kan du göra det när den virtuella datorn har distribuerats i Azure.

12. Avinstallera annan programvara eller drivrutin från tredje part som är relaterad till fysiska komponenter eller annan virtualiseringsteknik.

### <a name="install-windows-updates"></a>Installera Windows-uppdateringar
Helst bör du hålla maskinen uppdaterad på *plåsternivå*. Om detta inte är möjligt kontrollerar du att följande uppdateringar är installerade. De senaste uppdateringarna finns på historiksidorna för Windows Update: [Windows 10 och Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 och Windows Server 2012 R2](https://support.microsoft.com/help/4009470) och [Windows 7 SP1 och Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| Komponent               | Binär         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Lagring                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys (svenska)       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys (svenska)    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys (svenska)    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys (svenska)     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys (svenska)    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys (svenska)    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys (svenska)       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys (svenska)   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Nätverk                 | netvsc.sys (svenska)     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys (svenska)     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys (svenska)       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Kärna                    | Ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Fjärrskrivbordstjänster | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | Termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys (svenska)     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys (svenska)      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Säkerhet                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> För att undvika en oavsiktlig omstart under vm-etablering rekommenderar vi att du ser till att alla Windows Update-installationer är färdiga och att inga uppdateringar väntar. Ett sätt att göra detta är att installera alla möjliga Windows-uppdateringar och starta om en gång innan du kör Sysprep kommandot.

### <a name="determine-when-to-use-sysprep"></a>Ta reda på när Sysprep ska användas<a id="step23"></a>    

System Preparation Tool (Sysprep) är en process som du kan köra för att återställa en Windows-installation. Sysprep ger en "out of the box"-upplevelse genom att ta bort alla personuppgifter och återställa flera komponenter. 

Du kör vanligtvis Sysprep för att skapa en mall som du kan distribuera flera andra virtuella datorer som har en specifik konfiguration. Mallen kallas en *generaliserad bild*.

Om du bara vill skapa en virtuell dator från en disk behöver du inte använda Sysprep. I stället kan du skapa den virtuella datorn från en *specialiserad avbildning*. Information om hur du skapar en virtuell dator från en specialiserad disk finns i:

- [Skapa en virtuell dator från en särskild disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en specialiserad VHD-disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Om du vill skapa en generaliserad avbildning måste du köra Sysprep. Mer information finns i [Så här använder du Sysprep: En introduktion](https://technet.microsoft.com/library/bb457073.aspx). 

Alla roller eller program som är installerade på en Windows-baserad dator stöder inte generaliserade avbildningar. Så innan du kör den här proceduren, se till att Sysprep stöder rollen för datorn. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalisera en virtuell hårddisk

>[!NOTE]
> När du `sysprep.exe` har kört i följande steg inaktiverar du den virtuella datorn. Slå inte på den igen förrän du skapar en avbildning från den i Azure.

1. Logga in på den virtuella datorn för Windows.
1. Kör **Kommandotolken** som administratör. 
1. Ändra katalogen `%windir%\system32\sysprep`till . Kör sedan `sysprep.exe`.
1. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.

    ![Verktyg för systemförberedelse](media/prepare-for-upload-vhd-image/syspre.png)
1. Välj **Avstängning** **i avstängningsalternativ**.
1. Välj **OK**.
1. När Sysprep är klar stänger du av den virtuella datorn. Använd inte **Starta om** för att stänga av den virtuella datorn.

Nu är den virtuella hårddisken klar att laddas upp. Mer information om hur du skapar en virtuell dator från en generaliserad disk finns i [Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa en ny virtuell dator i Azure](sa-upload-generalized.md).


>[!NOTE]
> En anpassad *unattend.xml-fil* stöds inte. Även om vi `additionalUnattendContent` stöder egenskapen, som ger endast begränsat stöd för att lägga till [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) alternativ i *unattend.xml-filen* som Azure-etableringsagenten använder. Du kan till exempel använda [ytterligareUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) för att lägga till FirstLogonCommands och LogonCommands. Mer information finns [i exemplet med ytterligareUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Slutför de rekommenderade konfigurationerna
Följande inställningar påverkar inte uppladdning av virtuella hårddiskar. Vi rekommenderar dock starkt att du har konfigurerat dem.

* Installera [Azure Virtual Machine Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sedan kan du aktivera VM-tillägg. Vm-tilläggen implementerar de flesta kritiska funktioner som du kanske vill använda med dina virtuella datorer. Du behöver tilläggen, till exempel för att återställa lösenord eller konfigurera RDP. Mer information finns i [översikt över Azure Virtual Machine Agent](../extensions/agent-windows.md).
* När du har skapat den virtuella datorn i Azure rekommenderar vi att du placerar sidfilen på den *tidsmässiga enhetsvolymen* för att förbättra prestanda. Du kan ställa in filplaceringen på följande sätt:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Om en datadisk är kopplad till den virtuella datorn är den tidsmässiga enhetsvolymens bokstav vanligtvis *D*. Den här beteckningen kan vara olika beroende på dina inställningar och antalet tillgängliga enheter.
  * Vi rekommenderar att du inaktiverar skriptblockerare som kan tillhandahållas av antivirusprogram. De kan störa och blockera skript för Windows-etableringsagent som körs när du distribuerar en ny virtuell dator från avbildningen.
  
## <a name="next-steps"></a>Nästa steg
* [Ladda upp en Windows VM-avbildning till Azure for Resource Manager-distributioner](upload-generalized-managed.md)
* [Felsöka aktiveringsproblem för Azure Windows VM](troubleshoot-activation-problems.md)

