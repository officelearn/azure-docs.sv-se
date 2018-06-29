---
title: Förbereda en Windows-VHD att överföra till Azure | Microsoft Docs
description: Hur du förbereder en Windows-VHD eller VHDX innan du laddar upp till Azure
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 2d7ee7050f430efea64d9988adf4f5a603128de2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053457"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbereda en Windows-VHD eller VHDX för att överföra till Azure
Innan du laddar upp en Windows-datorer (VM) från lokal till Microsoft Azure måste du förbereda den virtuella hårddisken (VHD eller VHDX). Azure stöder endast generering 1 virtuella datorer som är i VHD-format och har en fast storlek disk. Den maximala storleken för den virtuella Hårddisken är 1,023 GB. Du kan konvertera en generation 1 virtuell dator från VHDX-filsystemet till virtuell Hårddisk och från en dynamiskt expanderande disk till en fast storlek. Men du kan inte ändra en virtuell dator generation. Mer information finns i [bör jag skapa en generation 1 eller 2 virtuella datorn i Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Mer information om principen för support för Azure VM finns [Microsoft server programvarusupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Anvisningarna i den här artikeln gäller för 64-bitarsversionen av Windows Server 2008 R2 och senare Windows server-operativsystem. Information om den körs 32-bitars version av operativsystemet i Azure finns [stöd för 32-bitars operativsystem i virtuella Azure-datorer](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Omvandla den virtuella disken till VHD- och med fast storlek 
Om du behöver konvertera din virtuella hårddisk till formatet som krävs för Azure kan du använda en av metoderna i det här avsnittet. Säkerhetskopiera den virtuella datorn innan du kör konverteringen virtuell disk och se till att fungerar Windows-VHD korrekt på den lokala servern. Lös eventuella fel i Virtuellt datorn innan du försöker konvertera eller överför den till Azure.

Skapa en virtuell dator som använder den konvertera disken när du har konverterat disken. Starta och logga in på den virtuella datorn och slutför förberedde överför den virtuella datorn.

### <a name="convert-disk-using-hyper-v-manager"></a>Konvertera disk med hjälp av Hyper-V Manager
1. Öppna Hyper-V Manager och välj den lokala datorn till vänster. Klicka på menyn ovanför datorlistan **åtgärd** > **redigera Disk**.
2. På den **hitta virtuell hårddisk** skärmen, leta upp och markera din virtuella hårddisk.
3. På den **Välj åtgärd** skärmen och välj sedan **konvertera** och **nästa**.
4. Om du behöver konvertera från VHDX väljer **VHD** och klicka sedan på **nästa**
5. Om du måste konvertera från en dynamiskt expanderande disk, väljer **fast storlek** och klicka sedan på **nästa**
6. Leta upp och välj en sökväg för att spara den nya VHD-filen till.
7. Klicka på **Slutför**.

>[!NOTE]
>Kommandon i den här artikeln måste köras på en upphöjd PowerShell-session.

### <a name="convert-disk-by-using-powershell"></a>Konvertera disk med hjälp av PowerShell
Du kan konvertera en virtuell disk med hjälp av den [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) i Windows PowerShell. Välj **kör som administratör** när du startar PowerShell. 

Följande exempelkommando konverteras från VHDX till virtuell Hårddisk och från en dynamiskt expanderande disk till en fast storlek:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
I det här kommandot ersätter du värdet för ”-sökvägen” med sökvägen till den virtuella hårddisken som du vill konvertera och värdet för ”-DestinationPath” med den nya sökvägen och namnet på den konvertera disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK diskformat
Om du har en Windows VM-avbildning i den [VMDK-filformat](https://en.wikipedia.org/wiki/VMDK), konvertera den till en virtuell Hårddisk med hjälp av den [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). Mer information finns i artikeln blogg [konvertera en VMware VMDK till Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

På den virtuella datorn som du vill överföra till Azure, kör du alla kommandon i följande steg från ett [upphöjt kommandotolksfönster](https://technet.microsoft.com/library/cc947813.aspx):

1. Ta bort alla statiska beständiga vägar i routningstabellen:
   
   * Om du vill visa routningstabellen kör `route print` i Kommandotolken.
   * Kontrollera den **beständiga vägar** avsnitt. Om en beständig väg använder [ta bort vägen](https://technet.microsoft.com/library/cc739598.apx) att ta bort den.
2. Ta bort WinHTTP-proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Ange SAN-principen disk [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
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
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Ange den privilegierade profilen till den **högpresterande**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Kontrollera Windows-tjänster
Kontrollera att var och en av följande Windows-tjänster har angetts till den **Windows standardvärdena**. Det här är det minsta antalet tjänster som måste ställas in för att se till att den virtuella datorn är ansluten. Om du vill återställa inställningar för Start, kör du följande kommandon:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Uppdatera registerinställningarna för fjärrskrivbord
Kontrollera att följande inställningar är korrekt konfigurerade för anslutning till fjärrskrivbord:

>[!Note] 
>Du får ett felmeddelande när du kör den **Set-ItemProperty-sökvägen ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal tjänster - namnet &lt;objektnamn&gt; &lt;värdet&gt;** i följande steg. Felmeddelandet kan ignoreras. Det innebär bara att domänen inte gör att konfigurationen med hjälp av ett grupprincipobjekt.
>
>

1. Remote Desktop Protocol (RDP) är aktiverat:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. RDP-porten är korrekt konfigurerad (standard port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    När du distribuerar en virtuell dator, skapa standardregler mot port 3389. Om du vill ändra portnumret kan du göra det när den virtuella datorn har distribuerats i Azure.

3. Lyssnaren lyssnar på varje gränssnitt:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Konfigurera autentisering på nätverksnivå-läge för RDP-anslutningar:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Keep alive-värdet:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Återansluta:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Begränsa antalet samtidiga anslutningar:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Om det finns några självsignerade certifikat som är knutna till RDP-lyssnaren kan du ta bort dem:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Detta är att se till att du kan ansluta i början när du distribuerar den virtuella datorn. Du kan också granska detta på ett senare skede när den virtuella datorn har distribuerats i Azure om det behövs.

9. Om den virtuella datorn ska ingå i en domän, kontrollerar du följande inställningar och kontrollera att de tidigare inställningarna inte återställs. De principer som måste vara markerad är följande:
    
    - RDP aktiveras:

         Datorn Datorkonfiguration\Principer\Windows Settings\Administrative Templates\ komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar:
         
         **Tillåt användare att fjärransluta med hjälp av fjärrskrivbord**

    - NLA Grupprincip:

        Settings\Administrative Templates\Components\Remote för fjärrskrivbord fjärrskrivbordssession\Säkerhet: 
        
        **Kräv användare autentisering för fjärranslutningar genom att använda autentisering på nätverksnivå**
    
    - Behålla Alive-inställningar:

        Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar: 
        
        **Konfigurera intervall för keep-alive-anslutningen**

    - Återansluta inställningar:

        Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar: 
        
        **Automatisk återanslutning**

    - Begränsa antalet anslutningsinställningar:

        Datorn Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Windows-komponenter\Fjärrskrivbordstjänster\Värdserver för fjärrskrivbordssession\Anslutningar: 
        
        **Begränsa antalet anslutningar**

## <a name="configure-windows-firewall-rules"></a>Konfigurera regler för Windows-brandväggen
1. Aktivera Windows-brandväggen på tre profiler (domän, Standard och offentlig):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Kör följande kommando i PowerShell för att tillåta WinRM via tre brandväggsprofiler (domän, privata och offentliga) och aktivera PowerShell Remote-tjänsten:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Aktivera följande brandväggsregler som tillåter RDP-trafik 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Aktivera regeln File and Printer Sharing så att den virtuella datorn kan svara på ping-kommandot i det virtuella nätverket:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Om den virtuella datorn ska ingå i en domän, kontrollerar du följande inställningar för att se till att de tidigare inställningarna inte återställs. AD-principer som måste vara markerad är följande:

    - Aktivera Windows-brandväggen profiler

        Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows datorbrandvägg: **skydda alla nätverksanslutningar**

       Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows datorbrandvägg: **skydda alla nätverksanslutningar**

    - Aktivera RDP 

        Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows datorbrandvägg: **Tillåt inkommande undantag för fjärrskrivbord**

        Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows datorbrandvägg: **Tillåt inkommande undantag för fjärrskrivbord**

    - Aktivera ICMP-V4

        Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Domain Profile\Windows datorbrandvägg: **Tillåt ICMP-undantag**

        Datorkonfiguration\Principer\Windows Settings\Administrative mallar\Nätverk\Nätverksanslutningar\Förhindra Connection\Windows Firewall\Standard Profile\Windows datorbrandvägg: **Tillåt ICMP-undantag**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Kontrollera den virtuella datorn är felfri, säker och går att nå med RDP 
1. Kontrollera att disken är felfri och konsekvent, gör en kontrollera disken vid nästa omstart VM:

    ```PowerShell
    Chkdsk /f
    ```
    Kontrollera att rapporten visar en ren och hälsosam disk.

2. Ange inställningar för startkonfigurationsdata (BCD). 

    > [!Note]
    > Kontrollera att du kan köra dessa kommandon på en CMD-kommandotolk och **inte** på PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Kontrollera att Windows Management Instrumentation-databasen är konsekvent. Du kan göra detta genom att köra följande kommando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Om databasen är skadad finns [WMI: databasen skadas eller inte](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

4. Kontrollera att det inte något annat program som använder port 3389. Den här porten används för RDP-tjänst i Azure. Du kan köra **netstat - anob** att se vilka portar i används på den virtuella datorn:

    ```PowerShell
    netstat -anob
    ```

5. Om Windows-VHD som du vill överföra är en domänkontrollant, följer du dessa steg:

    A. Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) att förbereda disken.

    B. Se till att du vet DSRM-lösenordet om du behöver starta den virtuella datorn i DSRM på någon punkt. Du kanske vill referera till den här länken för att den [DSRM-lösenordet](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Kontrollera att det inbyggda administratörskontot och lösenord är kända för dig. Du kanske vill återställa det aktuella lokala administratörslösenordet och se till att du använder det här kontot för att logga in på Windows via RDP-anslutning. Den här behörigheten åtkomst styrs av grupprincipobjektet ”Tillåt inloggning genom Fjärrskrivbordstjänster”. Du kan visa det här objektet i den lokala Redigeraren för under:

    Datorn Datorkonfiguration\Windows datorkonfiguration\windowsinställningar\säkerhetsinställningar\avancerad Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter

7. Kontrollera att följande AD principer för att kontrollera att du inte blockerar din RDP-åtkomst via RDP eller från nätverket:

    - Datorn Datorkonfiguration\Windows datorkonfiguration\windowsinställningar\säkerhetsinställningar\avancerad Säkerhetsinställningar\Lokala principer\user Rights Assignment\Deny åtkomst till den här datorn från nätverket

    - Datorn Datorkonfiguration\Windows datorkonfiguration\windowsinställningar\säkerhetsinställningar\avancerad Säkerhetsinställningar\Lokala principer\user Rights Assignment\Deny inloggning via Fjärrskrivbordstjänster


8. Starta om den virtuella datorn och kontrollera att Windows är fortfarande felfri kan nås via RDP-anslutning. Nu kan du vill skapa en virtuell dator i din lokala Hyper-V att kontrollera att den virtuella datorn startar helt och sedan testa om det är RDP kan nås.

9. Ta bort eventuella extra Transport Driver Interface filter, till exempel programvara som analyserar TCP paket eller extra brandväggar. Du kan också granska detta på ett senare skede när den virtuella datorn har distribuerats i Azure om det behövs.

10. Avinstallera andra tredje parts programvara och drivrutiner som är relaterade till fysiska komponenter eller någon annan virtualiseringsteknik används.

### <a name="install-windows-updates"></a>Installera uppdateringar för Windows
Den bästa konfigurationen är att **har korrigeringsnivå för datorn senast**. Om det inte är möjligt, kontrollerar du att följande uppdateringar är installerade:

|                       |                   |           |                                       Minsta filversion x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Komponent               | Binär            | Windows 7 och Windows Server 2008 R2 | Windows 8 och Windows Server 2012             | Windows 8.1 och Windows Server 2012 R2 | Windows 10 och Windows Server 2016 RS1 | Windows 10 RS2             |
| Storage                 | disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | Storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | NTFS.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | MSDSM.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | MPIO.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Nätverk                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | Mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | Tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | HTTP.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Kärna                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Fjärrskrivbordstjänster | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | TERMSRV.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Säkerhet                | På grund av WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### När du ska använda sysprep <a id="step23"></a>    

Sysprep är en process som du kan köra i en windows-installation som återställs installationen av systemet och ger en ”out of i box experience” genom att ta bort alla personliga data och återställa flera komponenter. Du gör detta om du vill skapa en mall som du kan distribuera flera andra virtuella datorer som har en viss konfiguration. Detta kallas en **generaliserad avbildning**.

Om du vill i stället endast att skapa en virtuell dator från en disk kan du inte använda sysprep. I så fall kan du bara kan skapa den virtuella datorn från som kallas en **specialiserad avbildning**.

Mer information om hur du skapar en virtuell dator från en särskild disk finns:

- [Skapa en virtuell dator från en särskild disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en särskild VHD-disk](https://review.docs.microsoft.com/en-us/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Om du vill skapa en generaliserad avbildning måste du köra sysprep. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx). 

Stöder den här generalisering inte varje roll eller program som är installerad på en Windows-baserad dator. Så innan du kör den här proceduren finns i följande artikel för att se till att rollen för den datorn som stöds av sysprep. Mer information [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Steg för att generalisera en virtuell Hårddisk

>[!NOTE]
> Stäng av den virtuella datorn när du har kört sysprep.exe som anges i följande steg och inte aktiverar det igen förrän du har skapat en avbildning från den i Azure.

1. Logga in på Windows VM.
2. Kör **kommandotolk** som administratör. 
3. Ändra katalogen till: **%windir%\system32\sysprep**, och kör sedan **sysprep.exe**.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.

    ![Systemförberedelseverktyget](media/prepare-for-upload-vhd-image/syspre.png)
4. I **avstängningsalternativ**väljer **avstängning**.
5. Klicka på **OK**.
6. Stäng den virtuella datorn när Sysprep är klar. Använd inte **starta om** att stänga av den virtuella datorn.
7. Den virtuella Hårddisken är nu redo att överföras. Mer information om hur du skapar en virtuell dator från en generaliserad disk finns [överför en generaliserad virtuell Hårddisk och använda den för att skapa en ny virtuella datorer i Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Fullständig rekommenderade konfigurationer
Följande inställningar påverkar inte VHD-överföring. Vi rekommenderar dock att du har konfigurerat dem.

* Installera den [virtuella Azure-datorer Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du kan aktivera VM-tillägg. VM-tillägg implementerar de flesta av de viktiga funktioner som du kanske vill använda med din virtuella dator, till exempel återställa lösenord, konfigurera RDP och så vidare. Mer information finns i:

    - [VM-Agent och tillägg – del 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [VM-Agent och tillägg – del 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* Dump loggen kan vara användbar vid felsökning av problem med Windows krascher. Aktivera Logginsamling Dump:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Om några fel under procedurmässig steg i den här artikeln, innebär det att registernycklarna som redan finns. I så fall kan du använda följande kommandon i stället:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  När den virtuella datorn skapas i Azure, rekommenderar vi att du placerar växlingsfilens storlek på ”Temporala” enheten för att förbättra prestanda. Du kan konfigurera detta på följande sätt:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Om det finns data diskar som är kopplad till den virtuella datorn, är Temporal enhet enhetsbeteckning vanligtvis ”d”. Den här beteckningen kan vara olika beroende på antalet enheter som är tillgängliga och de inställningar som du gör.

## <a name="next-steps"></a>Nästa steg
* [Ladda upp en Windows VM-avbildning till Azure för Resource Manager distributioner](upload-generalized-managed.md)
* [Felsöka problem med Windows Azure virtuella aktivering](troubleshoot-activation-problems.md)

