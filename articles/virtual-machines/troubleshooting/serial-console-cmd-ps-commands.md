---
title: CMD och PowerShell i virtuella Windows-datorer i Azure | Microsoft Docs
description: Använda CMD-och PowerShell-kommandon i SAC i Azure virtuella Windows-datorer
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 77fe6f1ce416df049928697d2c166e2aba0abfe2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935220"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-kommandon – CMD och PowerShell

Det här avsnittet innehåller exempel kommandon för att utföra vanliga uppgifter i scenarier där du kan behöva använda SAC för att få åtkomst till din virtuella Windows-dator, till exempel när du behöver felsöka RDP-anslutningsfel.

SAC har inkluderats i alla versioner av Windows sedan Windows Server 2003 men är inaktiverat som standard. SAC förlitar sig `sacdrv.sys` på kernel-drivrutinen `Special Administration Console Helper` , tjänsten`sacsvr` `sacsess.exe` () och processen. Mer information finns i [verktyg och inställningar för Emergency Management Services](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

Med SAC kan du ansluta till ditt operativ system via en seriell port. När du startar cmd från SAC `sacsess.exe` startar `cmd.exe` du i det operativ system som körs. Du kan se att i aktivitets hanteraren om du använder RDP till den virtuella datorn på samma gång som du är ansluten till SAC via funktionen serie konsol. CMD som du kommer åt via SAC är samma `cmd.exe` som du använder när du är ansluten via RDP. Alla samma kommandon och verktyg är tillgängliga, inklusive möjligheten att starta PowerShell från den CMD-instansen. Det är en stor skillnad mellan SAC och Windows återställnings miljö (WinRE) i den SAC som låter dig hantera ditt drift operativ system, där WinRE startar i ett annat, minimalt operativ system. Virtuella Azure-datorer har inte stöd för möjligheten att komma åt WinRE, med funktionen för serie konsol, och virtuella Azure-datorer kan hanteras via SAC.

Eftersom SAC är begränsat till en 80x24 utan att rulla tillbaka, lägger `| more` du till kommandon för att visa utdata en sida i taget. Använd `<spacebar>` för att se nästa sida eller `<enter>` för att se nästa rad.  

`SHIFT+INSERT`är den infogade genvägen för serie konsol fönstret.

På grund av SAC: s begränsade skärmsläckare kan längre kommandon vara enklare att skriva ut i en lokal text redigerare och sedan klistras in i SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera inställningar för Windows-registret
### <a name="verify-rdp-is-enabled"></a>Verifiera att RDP är aktiverat
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Den andra nyckeln (under \Policies) kommer bara att finnas om den relevanta grup princip inställningen har kon figurer ATS.

### <a name="enable-rdp"></a>Aktivera RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Den andra nyckeln (under \Policies) behövs bara om den relevanta grup princip inställningen hade kon figurer ATS. Värdet skrivs om nästa gång grup princip uppdateras om det har kon figurer ATS i en grup princip.

## <a name="manage-windows-services"></a>Hantera Windows-tjänster

### <a name="view-service-state"></a>Visa tjänst status
`sc query termservice`
###  <a name="view-service-logon-account"></a>Visa tjänst inloggnings konto
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ange tjänst inloggnings konto 
`sc config termservice obj= "NT Authority\NetworkService"`

Ett blank steg krävs efter likhets tecknet.
### <a name="set-service-start-type"></a>Ange starttyp för tjänst
`sc config termservice start= demand` 

Ett blank steg krävs efter likhets tecknet. Möjliga start värden är `boot`, `system`, `auto` `demand` ,`disabled`,, .`delayed-auto`
### <a name="set-service-dependencies"></a>Ange tjänst beroenden
`sc config termservice depend= RPCSS`

Ett blank steg krävs efter likhets tecknet.
### <a name="start-service"></a>Starta tjänst
`net start termservice`

eller

`sc start termservice`
### <a name="stop-service"></a>Stoppa tjänsten
`net stop termservice`

eller

`sc stop termservice`
## <a name="manage-networking-features"></a>Hantera nätverksfunktioner
### <a name="show-nic-properties"></a>Visa NIC-egenskaper
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Visa IPSec-konfiguration
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Aktivera NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ange NIC för att använda DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

[Klicka här](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts)om du `netsh`vill ha mer information om.

Virtuella Azure-datorer måste alltid konfigureras i gäst operativ systemet för att använda DHCP för att hämta en IP-adress. Den statiska IP-inställningen i Azure använder fortfarande DHCP för att ge den statiska IP-adressen till den virtuella datorn.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port-ping  
Installera Telnet-klienten

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testa anslutning

`telnet bing.com 80`

Ta bort Telnet-klienten

`dism /online /Disable-Feature /FeatureName:TelnetClient`

När det gäller metoder som är tillgängliga i Windows som standard, kan PowerShell vara en bättre metod för att testa port anslutningen. Se PowerShell-avsnittet nedan för exempel.
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Visa regel för Windows-brandvägg
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`netsh advfirewall set allprofiles state off`

Du kan använda det här kommandot när du felsöker för att tillfälligt utesluta Windows-brandväggen. Den aktive ras vid nästa omstart eller när du aktiverar den med hjälp av kommandot nedan. Stoppa inte tjänsten Windows Firewall service (MPSSVC) eller tjänsten för bas filtrerings motor (BFE) på ett sätt som kan utesluta Windows-brandväggen. Om du stoppar MPSSVC eller BFE leder det till att alla anslutningar blockeras.
### <a name="enable-windows-firewall"></a>Aktivera Windows-brandväggen
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användar konto
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Lägg till lokal användare i lokal grupp
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Kontrol lera att användar kontot är aktiverat
`net user <username> | find /i "active"`

Virtuella Azure-datorer som skapas från en generaliserad avbildning kommer att ha det lokala administratörs kontot bytt namn till det namn som angavs under VM-etableringen. Därför är det vanligt vis inte `Administrator`.
### <a name="enable-user-account"></a>Aktivera användar konto
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Visa egenskaper för användar konto
`net user <username>`

Exempel rader av intresse från ett lokalt administratörs konto:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Visa lokala grupper
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Hantera händelse loggen i Windows
### <a name="query-event-log-errors"></a>Händelse logg fel för fråga
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Ändra `/c:10` till önskat antal händelser för att returnera eller flytta den för att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Fråga händelse logg efter händelse-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Fråga händelse logg efter händelse-ID och Provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga händelse logg efter händelse-ID och Provider under de senaste 24 timmarna
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` för att se tillbaka 7 dagar i stället för 24 timmar.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelse logg efter händelse-ID, Provider och EventData under de senaste 7 dagarna
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-applications"></a>Visa lista över installerade program
`wmic product get Name,InstallDate | sort /r | more`

De `sort /r` sorterar fallande efter installations datum så att det blir enkelt att se vad som nyligen har installerats. Använd `<spacebar>` för att gå vidare till nästa sida med utdata eller `<enter>` för att gå en rad.
### <a name="uninstall-an-application"></a>Avinstallera ett program
`wmic path win32_product where name="<name>" call uninstall`

Ersätt `<name>` med namnet som returneras i ovanstående kommando för det program som du vill ta bort.

## <a name="file-system-management"></a>Hantering av fil system
### <a name="get-file-version"></a>Hämta fil version
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Det här exemplet returnerar fil versionen av den virtuella nätverkskort driv rutinen, som är netvsc. sys, netvsc63. sys eller netvsc60. sys beroende på Windows-versionen.
### <a name="scan-for-system-file-corruption"></a>Söker efter skadad system fil
`sfc /scannow`

Se även [Reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Söker efter skadad system fil
`dism /online /cleanup-image /scanhealth`

Se även [Reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportera fil behörigheter till textfil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Spara fil behörigheter till ACL-filen
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Återställ fil behörigheter från ACL-filen
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Sökvägen när du använder `/restore` måste vara den överordnade mappen till den mapp som du angav när du `/save`använde. I det här exemplet `\RSA` är överordnad till `\MachineKeys` den mapp som anges `/save` i exemplet ovan.
### <a name="take-ntfs-ownership-of-a-folder"></a>Ta NTFS-ägande av en mapp
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Ge NTFS-behörigheter till en mapp rekursivt
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Hantera enheter
### <a name="remove-non-present-pnp-devices"></a>Ta bort icke-befintliga PNP-enheter
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Hantera grupprincip
### <a name="force-group-policy-update"></a>Framtvinga uppdatering av grup princip
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diverse aktiviteter
### <a name="show-os-version"></a>Visa OS-version
`ver`

eller 

`wmic os get caption,version,buildnumber /format:list`

eller 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Visa OS-installations datum
`systeminfo | find /i "original"`

eller 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Visa senaste start tid
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Visa tidszon
`systeminfo | find /i "time zone"`

eller

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Starta om Windows
`shutdown /r /t 0`

Om `/f` du lägger till tvingas körning av program att stängas utan varnings användare.
### <a name="detect-safe-mode-boot"></a>Identifiera start i fel säkert läge
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows-kommandon – PowerShell

Om du vill köra PowerShell i SAC, efter att du har kommit till en kommando tolk, skriver du:

`powershell <enter>`

> [!CAUTION]
> Ta bort PSReadLine-modulen från PowerShell-sessionen innan du kör andra PowerShell-kommandon. Det finns ett känt problem där extra tecken kan läggas till i text som klistrats in från Urklipp om PSReadLine körs i en PowerShell-session i SAC.

Kontrol lera först om PSReadLine har lästs in. Den läses in som standard på Windows Server 2016, Windows 10 och senare versioner av Windows. Den finns bara på tidigare Windows-versioner om den hade installerats manuellt. 

Om det här kommandot returnerar en prompt utan utdata har modulen inte lästs in och du kan fortsätta att använda PowerShell-sessionen i SAC som vanligt.

`get-module psreadline`

Om kommandot ovan returnerar PSReadLine-modulens version kör du följande kommando för att ta bort den. Det här kommandot tar inte bort eller avinstallerar modulen, den laddar bara bort den från den aktuella PowerShell-sessionen.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera inställningar för Windows-registret
### <a name="verify-rdp-is-enabled"></a>Verifiera att RDP är aktiverat
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Den andra nyckeln (under \Policies) kommer bara att finnas om den relevanta grup princip inställningen har kon figurer ATS.
### <a name="enable-rdp"></a>Aktivera RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Den andra nyckeln (under \Policies) behövs bara om den relevanta grup princip inställningen hade kon figurer ATS. Värdet skrivs om nästa gång grup princip uppdateras om det har kon figurer ATS i en grup princip.
## <a name="manage-windows-services"></a>Hantera Windows-tjänster
### <a name="view-service-details"></a>Visa tjänst information
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`kan användas men inkluderar inte tjänstens inloggnings konto. `Get-WmiObject win32-service`stämm.
### <a name="set-service-logon-account"></a>Ange tjänst inloggnings konto
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

När du använder ett annat tjänst konto `NT AUTHORITY\LocalService`än `NT AUTHORITY\NetworkService`, eller `LocalSystem`, anger du konto lösen ordet som det sista (åttonde) argumentet efter konto namnet.
### <a name="set-service-startup-type"></a>Ange starttyp för tjänst
`set-service termservice -startuptype Manual`

`Set-service`accepterar `Automatic`, `Manual` eller`Disabled` för start metod.
### <a name="set-service-dependencies"></a>Ange tjänst beroenden
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Starta tjänst
`start-service termservice`
### <a name="stop-service"></a>Stoppa tjänsten
`stop-service termservice`
## <a name="manage-networking-features"></a>Hantera nätverksfunktioner
### <a name="show-nic-properties"></a>Visa NIC-egenskaper
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

eller 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`är tillgängligt i 2012 + för 2008R2-användning `Get-WmiObject`.
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Aktivera NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

eller

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`är tillgängligt i 2012 + för 2008R2-användning `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Ange NIC för att använda DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`är tillgängligt på 2012 +. För användning `Get-WmiObject`av 2008R2. Virtuella Azure-datorer måste alltid konfigureras i gäst operativ systemet för att använda DHCP för att hämta en IP-adress. Den statiska IP-inställningen i Azure använder fortfarande DHCP för att ge IP-adressen till den virtuella datorn.
### <a name="ping"></a>Ping
`test-netconnection`

eller

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`utan parametrar kommer att försöka pinga `internetbeacon.msedge.net`. Den är tillgänglig på 2012 +. För 2008R2 används `Get-WmiObject` som i det andra exemplet.
### <a name="port-ping"></a>Port-ping
`test-netconnection -ComputerName bing.com -Port 80`

eller

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`är tillgängligt på 2012 +. För 2008R2-användning`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`resolve-dnsname bing.com` 

eller 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`är tillgängligt på 2012 +. För användning `System.Net.DNS`av 2008R2.
### <a name="show-windows-firewall-rule-by-name"></a>Visa regel för Windows-brandvägg efter namn
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Visa regel för Windows-brandvägg per port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

eller

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`är tillgängligt på 2012 +. För 2008R2 använder du `hnetcfg.fwpolicy2` com-objektet. 
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`är tillgängligt på 2012 +. För 2008R2- `netsh advfirewall` användning som det hänvisas till i avsnittet cmd ovan.
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användar konto
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Kontrol lera att användar kontot är aktiverat
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

eller 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`är tillgängligt på 2012 +. För användning `Get-WmiObject`av 2008R2. Det här exemplet visar det inbyggda lokala administratörs kontot, som alltid har SID `S-1-5-21-*-500`. Virtuella Azure-datorer som skapas från en generaliserad avbildning kommer att ha det lokala administratörs kontot bytt namn till det namn som angavs under VM-etableringen. Därför är det vanligt vis inte `Administrator`.
### <a name="add-local-user-to-local-group"></a>Lägg till lokal användare i lokal grupp
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Aktivera lokalt användar konto
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Det här exemplet aktiverar det inbyggda lokala administratörs kontot, som alltid har SID `S-1-5-21-*-500`. Virtuella Azure-datorer som skapas från en generaliserad avbildning kommer att ha det lokala administratörs kontot bytt namn till det namn som angavs under VM-etableringen. Därför är det vanligt vis inte `Administrator`.
### <a name="view-user-account-properties"></a>Visa egenskaper för användar konto
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

eller 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`är tillgängligt på 2012 +. För användning `Get-WmiObject`av 2008R2. Det här exemplet visar det inbyggda lokala administratörs kontot, som alltid har SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Visa lokala grupper
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`är tillgängligt på 2012 +. För användning `Get-WmiObject`av 2008R2.
## <a name="manage-the-windows-event-log"></a>Hantera händelse loggen i Windows
### <a name="query-event-log-errors"></a>Händelse logg fel för fråga
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Ändra `/c:10` till önskat antal händelser för att returnera eller flytta den för att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Fråga händelse logg efter händelse-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Fråga händelse logg efter händelse-ID och Provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga händelse logg efter händelse-ID och Provider under de senaste 24 timmarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` för att se tillbaka 7 dagar i stället för 24 timmar. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelse logg efter händelse-ID, Provider och EventData under de senaste 7 dagarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-software"></a>Visa lista över installerade program
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Avinstallera program vara
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Hantering av fil system
### <a name="get-file-version"></a>Hämta fil version
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Det här exemplet returnerar fil versionen av den virtuella nätverkskort driv rutinen, som heter netvsc. sys, netvsc63. sys eller netvsc60. sys beroende på Windows-versionen.
### <a name="download-and-extract-file"></a>Ladda ned och extrahera fil
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Det här exemplet skapar `c:\bin` en mapp och hämtar och extraherar sedan Sysinternals-serien med `c:\bin`verktyg till.
## <a name="miscellaneous-tasks"></a>Diverse aktiviteter
### <a name="show-os-version"></a>Visa OS-version
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Visa OS-installations datum
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Visa senaste start tid
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Visa Windows-drift tid
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Returnerar drift tid `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, till exempel `49:16:48:00.00`. 
### <a name="restart-windows"></a>Starta om Windows
`restart-computer`

Om `-force` du lägger till tvingas körning av program att stängas utan varnings användare.
## <a name="instance-metadata"></a>Metadata för instans

Du kan fråga Azure instance metadata från din virtuella Azure-dator om du vill visa information som osType, Location, vmSize, vmId, Name, resourceGroupName, subscriptionId, privateIpAddress och publicIpAddress.

Frågor om instansen av instansen kräver en felfri gäst nätverks anslutning, eftersom det gör ett REST-anrop via Azure-värden till instansens metadatatjänst. Så om du kan fråga efter instansen metadata så att gästen kan kommunicera via nätverket till en Azure-värdbaserad tjänst.

Mer information finns i [Azure instance metadata service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadata för instans
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>OS-typ (instansens metadata)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Plats (instansens metadata)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Storlek (instansens metadata)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-ID (instansens metadata)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM-namn (instansens metadata)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Resurs grupps namn (instansens metadata)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Prenumerations-ID (instansens metadata)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Taggar (instansens metadata)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Placerings gruppens ID (instansens metadata)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Plattforms Fels domän (instansens metadata)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Plattforms uppdaterings domän (instansens metadata)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 privat IP-adress (instans-metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Offentlig IPv4-IP-adress (instans-metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adress/prefix för IPv4-undernät (instans-metadata)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP-adress (instans-metadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-adress (instans-metadata)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Nästa steg
* Huvud sidan för Windows-dokumentation för serie konsolen finns [här](serial-console-windows.md).
* Seriekonsolen är också tillgängligt för [Linux](serial-console-linux.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
