---
title: CMD och PowerShell i virtuella Azure Windows-datorer | Microsoft-dokument
description: Så här använder du CMD- och PowerShell-kommandon i SAC i virtuella Azure Windows-datorer
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167034"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-kommandon – CMD och PowerShell

Det här avsnittet innehåller exempelkommandon för att utföra vanliga uppgifter i scenarier där du kan behöva använda SAC för att komma åt din Virtuella Windows-dator, till exempel när du behöver felsöka rdp-anslutningsfel.

SAC har inkluderats i alla versioner av Windows sedan Windows Server 2003 men är inaktiverat som standard. SAC förlitar `sacdrv.sys` sig på `Special Administration Console Helper` kärndrivrutinen, tjänsten (`sacsvr`) och `sacsess.exe` processen. Mer information finns i [Verktyg och inställningar för tjänster för nödadministration](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC kan du ansluta till din kör OS via seriell port. När du startar CMD `sacsess.exe` från `cmd.exe` SAC, startar inom din kör OS. Du kan se det i Aktivitetshanteraren om du RDP till din virtuella dator samtidigt som du är ansluten till SAC via seriell konsolfunktionen. Den CMD du kommer åt `cmd.exe` via SAC är samma som du använder när du är ansluten via RDP. Alla samma kommandon och verktyg är tillgängliga, inklusive möjligheten att starta PowerShell från den CMD-instansen. Det är en stor skillnad mellan SAC och Windows Recovery Environment (WinRE) i att SAC låter dig hantera din kör OS, där WinRE stövlar till en annan, minimal OS. Medan virtuella Azure-datorer inte stöder möjligheten att komma åt WinRE, med den seriella konsolfunktionen, kan virtuella Azure-datorer hanteras via SAC.

Eftersom SAC är begränsat till en skärmbuffert på 80x24 utan att rulla bakåt lägger du `| more` till kommandon för att visa utdata en sida i taget. Används `<spacebar>` för att se `<enter>` nästa sida eller för att se nästa rad.

`SHIFT+INSERT`är genvägen för inklistring för seriekonsolfönstret.

På grund av SAC:s begränsade skärmbuffert kan längre kommandon vara enklare att skriva ut i en lokal textredigerare och sedan klistras in i SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera registerinställningar för Windows
### <a name="verify-rdp-is-enabled"></a>Kontrollera att RDP är aktiverat
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Den andra nyckeln (under \Principer) finns bara om den relevanta grupprincipinställningen har konfigurerats.

### <a name="enable-rdp"></a>Aktivera RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

Den andra nyckeln (under \Principer) skulle bara behövas om den relevanta grupprincipinställningen hade konfigurerats. Värdet skrivs om vid nästa grupprincipuppdatering om det är konfigurerat i grupprincipen.

## <a name="manage-windows-services"></a>Hantera Windows-tjänster

### <a name="view-service-state"></a>Visa tjänsttillstånd
`sc query termservice`
###  <a name="view-service-logon-account"></a>Visa tjänstinloggningskonto
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ange tjänstinloggningskonto
`sc config termservice obj= "NT Authority\NetworkService"`

Ett utrymme krävs efter likhetstecknet.
### <a name="set-service-start-type"></a>Ange starttyp för tjänsten
`sc config termservice start= demand`

Ett utrymme krävs efter likhetstecknet. Möjliga startvärden `boot` `system`inkluderar `auto` `demand`, `disabled` `delayed-auto`, , , , , .
### <a name="set-service-dependencies"></a>Ange tjänstberoenden
`sc config termservice depend= RPCSS`

Ett utrymme krävs efter likhetstecknet.
### <a name="start-service"></a>Starta tjänsten
`net start termservice`

eller

`sc start termservice`
### <a name="stop-service"></a>Stoppa tjänsten
`net stop termservice`

eller

`sc stop termservice`
## <a name="manage-networking-features"></a>Hantera nätverksfunktioner
### <a name="show-nic-properties"></a>Visa nätverkskortsegenskaper
`netsh interface show interface`
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Visa IPSec-konfiguration
`netsh nap client show configuration`
### <a name="enable-nic"></a>Aktivera nätverkskort
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ange nätverkskort för att använda DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

För mer `netsh`information om , [klicka här](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Virtuella Azure-datorer bör alltid konfigureras i gästoperativsystemet för att använda DHCP för att hämta en IP-adress. Azure-statisk IP-inställningen använder fortfarande DHCP för att ge den statiska IP till den virtuella datorn.
### <a name="ping"></a>Pinga
`ping 8.8.8.8`
### <a name="port-ping"></a>Port ping
Installera telnet-klienten

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testa anslutning

`telnet bing.com 80`

Så här tar du bort telnet-klienten

`dism /online /Disable-Feature /FeatureName:TelnetClient`

När det är begränsat till metoder som är tillgängliga i Windows som standard kan PowerShell vara en bättre metod för att testa portanslutning. Mer om du vill ta del av avsnittet PowerShell nedan finns exempel.
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Visa Windows-brandväggsregel
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`netsh advfirewall set allprofiles state off`

Du kan använda det här kommandot vid felsökning för att tillfälligt utesluta Windows-brandväggen. Det kommer att aktiveras vid nästa omstart eller när du aktiverar den med kommandot nedan. Stoppa inte tjänsten Windows Firewall (MPSSVC) eller BFE (Base Filtering Engine) för att utesluta Windows-brandväggen. Om du stoppar MPSSVC eller BFE blockeras all anslutning.
### <a name="enable-windows-firewall"></a>Aktivera Windows-brandväggen
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Lägga till lokal användare i lokal grupp
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Verifiera användarkonto är aktiverat
`net user <username> | find /i "active"`

Virtuella Azure-datorer som skapats från den allmänna avbildningen har det lokala administratörskontot bytt namn till det namn som angavs under etablering av virtuella datorer. Så det brukar `Administrator`inte vara.
### <a name="enable-user-account"></a>Aktivera användarkonto
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Visa egenskaper för användarkonton
`net user <username>`

Exempel på intresserader från ett lokalt administratörskonto:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Visa lokala grupper
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
### <a name="query-event-log-errors"></a>Fel i frågehändelseloggen
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Ändra `/c:10` till önskat antal händelser som ska returneras eller flytta det för att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Frågehändelselogg efter händelse-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Frågehändelselogg efter händelse-ID och leverantör
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Frågehändelselogg efter händelse-ID och leverantör under de senaste 24 timmarna
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Används `604800000` för att se tillbaka 7 dagar istället för 24 timmar.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Frågehändelselogg efter händelse-ID, leverantör och EventData under de senaste 7 dagarna
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-applications"></a>Lista installerade program
`wmic product get Name,InstallDate | sort /r | more`

Sorterar `sort /r` fallande efter installationsdatum för att göra det enkelt att se vad som nyligen installerades. Används `<spacebar>` för att gå vidare till `<enter>` nästa sida med utdata eller för att flytta en rad framåt.
### <a name="uninstall-an-application"></a>Avinstallera ett program
`wmic path win32_product where name="<name>" call uninstall`

Ersätt `<name>` med namnet som returneras i kommandot ovan för det program som du vill ta bort.

## <a name="file-system-management"></a>Hantering av filsystem
### <a name="get-file-version"></a>Hämta filversion
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

I det här exemplet returneras filversionen av den virtuella NIC-drivrutinen, som är netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på Windows-versionen.
### <a name="scan-for-system-file-corruption"></a>Sök efter systemfilskorruption
`sfc /scannow`

Se även [Reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Sök efter systemfilskorruption
`dism /online /cleanup-image /scanhealth`

Se även [Reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportera filbehörigheter till textfil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Spara filbehörigheter i ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Återställa filbehörigheter från ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Sökvägen när `/restore` du använder måste vara den överordnade mappen `/save`i mappen som du angav när du använder . I det `\RSA` här exemplet är `\MachineKeys` den överordnade `/save` mappen som anges i exemplet ovan.
### <a name="take-ntfs-ownership-of-a-folder"></a>Ta NTFS-ägarskap för en mapp
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Bevilja NTFS-behörigheter till en mapp rekursivt
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Hantera enheter
### <a name="remove-non-present-pnp-devices"></a>Ta bort PNP-enheter som inte finns tillgängliga
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Hantera grupprincip
### <a name="force-group-policy-update"></a>Uppdatera grupprincipen Tvinga
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diverse uppgifter
### <a name="show-os-version"></a>Visa OS-version
`ver`

eller

`wmic os get caption,version,buildnumber /format:list`

eller

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Visa installationsdatum för operativsystem
`systeminfo | find /i "original"`

eller

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Visa senaste starttid
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Visa tidszon
`systeminfo | find /i "time zone"`

eller

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Starta om Windows
`shutdown /r /t 0`

Genom `/f` att lägga till tvingas program som körs att stängas utan att användarna varnas.
### <a name="detect-safe-mode-boot"></a>Identifiera start för felsäkert läge
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Windows-kommandon - PowerShell

Om du vill köra PowerShell i SAC skriver du:

`powershell <enter>`

> [!CAUTION]
> Ta bort PSReadLine-modulen från PowerShell-sessionen innan du kör några andra PowerShell-kommandon. Det finns ett känt problem där extra tecken kan introduceras i text klistras in från Urklipp om PSReadLine körs i en PowerShell-session i SAC.

Kontrollera först om PSReadLine har lästs in. Den läses in som standard på Windows Server 2016, Windows 10 och senare versioner av Windows. Det skulle bara finnas på tidigare Windows-versioner om det hade installerats manuellt.

Om det här kommandot återgår till en fråga utan utdata lästes modulen inte in och du kan fortsätta använda PowerShell-sessionen i SAC som vanligt.

`get-module psreadline`

Om kommandot ovan returnerar PSReadLine-modulversionen kör du följande kommando för att ta bort den. Det här kommandot tar inte bort eller avinstallerar modulen, det tar bara bort den från den aktuella PowerShell-sessionen.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera registerinställningar för Windows
### <a name="verify-rdp-is-enabled"></a>Kontrollera att RDP är aktiverat
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Den andra nyckeln (under \Principer) finns bara om den relevanta grupprincipinställningen har konfigurerats.
### <a name="enable-rdp"></a>Aktivera RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Den andra nyckeln (under \Principer) skulle bara behövas om den relevanta grupprincipinställningen hade konfigurerats. Värdet skrivs om vid nästa grupprincipuppdatering om det är konfigurerat i grupprincipen.
## <a name="manage-windows-services"></a>Hantera Windows-tjänster
### <a name="view-service-details"></a>Visa serviceinformation
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`kan användas men inkluderar inte tjänstinloggningskontot. `Get-WmiObject win32-service`Gör.
### <a name="set-service-logon-account"></a>Ange tjänstinloggningskonto
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

När du använder ett `NT AUTHORITY\LocalService` `NT AUTHORITY\NetworkService`annat `LocalSystem`tjänstkonto än , eller anger du kontolösenordet som det sista (åttonde) argumentet efter kontonamnet.
### <a name="set-service-startup-type"></a>Ange starttyp för tjänsten
`set-service termservice -startuptype Manual`

`Set-service`accepterar `Automatic` `Manual`, `Disabled` eller för starttyp.
### <a name="set-service-dependencies"></a>Ange tjänstberoenden
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Starta tjänsten
`start-service termservice`
### <a name="stop-service"></a>Stoppa tjänsten
`stop-service termservice`
## <a name="manage-networking-features"></a>Hantera nätverksfunktioner
### <a name="show-nic-properties"></a>Visa nätverkskortsegenskaper
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

eller

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`finns tillgänglig i 2012 +, för 2008R2 användning `Get-WmiObject`.
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Aktivera nätverkskort
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

eller

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`finns tillgänglig i 2012 +, för 2008R2 användning `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Ange nätverkskort för att använda DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`finns tillgänglig på 2012+. För 2008R2 `Get-WmiObject`användning . Virtuella Azure-datorer bör alltid konfigureras i gästoperativsystemet för att använda DHCP för att hämta en IP-adress. Azure-statisk IP-inställningen använder fortfarande DHCP för att ge IP till den virtuella datorn.
### <a name="ping"></a>Pinga
`test-netconnection`

> [!NOTE]
> Cmdlet för skrivförlopp kanske inte fungerar med det här kommandot. Som en begränsning kan `$ProgressPreference = "SilentlyContinue"` du köra i PowerShell för att inaktivera förloppsindikatorn.

eller

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`utan några parametrar kommer `internetbeacon.msedge.net`att försöka pinga . Den finns tillgänglig på 2012+. För 2008R2 `Get-WmiObject` använd som i det andra exemplet.
### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

eller

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`finns tillgänglig på 2012+. För 2008R2 användning`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`resolve-dnsname bing.com`

eller

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`finns tillgänglig på 2012+. För 2008R2 `System.Net.DNS`användning .
### <a name="show-windows-firewall-rule-by-name"></a>Visa Windows-brandväggsregel efter namn
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Visa Windows-brandväggsregeln efter port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

eller

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`finns tillgänglig på 2012+. För 2008R2 `hnetcfg.fwpolicy2` använd COM-objektet.
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`finns tillgänglig på 2012+. För 2008R2 `netsh advfirewall` använd enligt beskrivningen i CMD-avsnittet ovan.
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Verifiera användarkonto är aktiverat
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

eller

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`finns tillgänglig på 2012+. För 2008R2 `Get-WmiObject`användning . I det här exemplet visas det inbyggda `S-1-5-21-*-500`lokala administratörskontot, som alltid har SID . Virtuella Azure-datorer som skapats från den allmänna avbildningen har det lokala administratörskontot bytt namn till det namn som angavs under etablering av virtuella datorer. Så det brukar `Administrator`inte vara.
### <a name="add-local-user-to-local-group"></a>Lägga till lokal användare i lokal grupp
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Aktivera lokalt användarkonto
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

I det här exemplet kan det inbyggda lokala `S-1-5-21-*-500`administratörskontot, som alltid har SID. Virtuella Azure-datorer som skapats från den allmänna avbildningen har det lokala administratörskontot bytt namn till det namn som angavs under etablering av virtuella datorer. Så det brukar `Administrator`inte vara.
### <a name="view-user-account-properties"></a>Visa egenskaper för användarkonton
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

eller

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`finns tillgänglig på 2012+. För 2008R2 `Get-WmiObject`användning . I det här exemplet visas det inbyggda `S-1-5-21-*-500`lokala administratörskontot, som alltid har SID .
### <a name="view-local-groups"></a>Visa lokala grupper
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`finns tillgänglig på 2012+. För 2008R2 `Get-WmiObject`användning .
## <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
### <a name="query-event-log-errors"></a>Fel i frågehändelseloggen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Ändra `/c:10` till önskat antal händelser som ska returneras eller flytta det för att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Frågehändelselogg efter händelse-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Frågehändelselogg efter händelse-ID och leverantör
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Frågehändelselogg efter händelse-ID och leverantör under de senaste 24 timmarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Används `604800000` för att se tillbaka 7 dagar istället för 24 timmar. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Frågehändelselogg efter händelse-ID, leverantör och EventData under de senaste 7 dagarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-software"></a>Lista installerad programvara
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Avinstallera programvara
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Hantering av filsystem
### <a name="get-file-version"></a>Hämta filversion
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

I det här exemplet returneras filversionen av den virtuella NIC-drivrutinen, som heter netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på Windows-versionen.
### <a name="download-and-extract-file"></a>Ladda ner och extrahera fil
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Det här exemplet `c:\bin` skapar en mapp och hämtar och extraherar sysinternals-paketen med verktyg till `c:\bin`.
## <a name="miscellaneous-tasks"></a>Diverse uppgifter
### <a name="show-os-version"></a>Visa OS-version
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Visa installationsdatum för operativsystem
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Visa senaste starttid
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Visa Windows-drifttid
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Returnerar drifttid `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`som `49:16:48:00.00`till exempel .
### <a name="restart-windows"></a>Starta om Windows
`restart-computer`

Genom `-force` att lägga till tvingas program som körs att stängas utan att användarna varnas.
## <a name="instance-metadata"></a>Instansmetadata

Du kan fråga Azure-instansmetadata från din Virtuella Azure-dator för att visa information som osType, Plats, vmSize, vmId, namn, resourceGroupName, subscriptionId, privateIpAddress och publicIpAddress.

Att fråga instansmetadata kräver felfri anslutning till gästnätverk, eftersom det gör ett REST-anrop via Azure-värden till instansmetadatatjänsten. Så om du kan fråga instansmetadata, som talar om för dig att gästen kan kommunicera via nätverket till en Azure-värd tjänst.

Mer information finns i [Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Instansmetadata
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>OS-typ (instansmetadata)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Plats (instansmetadata)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Storlek (instansmetadata)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-ID (instansmetadata)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM-namn (instansmetadata)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Namn på resursgrupp (instansmetadata)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Prenumerations-ID (instansmetadata)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Taggar (instansmetadata)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Placeringsgrupp-ID (instansmetadata)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domän för plattformsfel (instansmetadata)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domän för uppdatering av plattform (instansmetadata)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Privat IP-adress iPv4 (instansmetadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 public IP-adress (instansmetadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-undernätsadress/prefix (instansmetadata)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP-adress (instansmetadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-adress (instansmetadata)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Nästa steg
* Den huvudsakliga seriell konsol Windows dokumentationssida finns [här](serial-console-windows.md).
* Seriekonsolen är också tillgänglig för [virtuella Linux-datorer.](serial-console-linux.md)
* Läs mer om [startdiagnostik](boot-diagnostics.md).
