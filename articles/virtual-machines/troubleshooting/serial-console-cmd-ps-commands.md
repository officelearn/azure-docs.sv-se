---
title: CMD och PowerShell i virtuella Azure Windows-datorer | Microsoft Docs
description: Hur du använder CMD och PowerShell-kommandon inom SAC i virtuella Azure Windows-datorer
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 55b7e45bb9e600267e1dad0e36e9a97eca9a7d40
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075006"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-kommandon - CMD och PowerShell

Det här avsnittet innehåller exempel på kommandon för att utföra vanliga uppgifter i scenarier där du kan behöva använda SAC för åtkomst till din Windows-VM, till exempel när du behöver felsöka RDP-anslutningsfel.

SAC har inkluderats i alla versioner av Windows sedan Windows Server 2003, men är inaktiverad som standard. SAC förlitar sig på den `sacdrv.sys` kerneldrivrutinen den `Special Administration Console Helper` service (`sacsvr`), och `sacsess.exe` process. Mer information finns i [Emergency Management Services-verktyg och inställningar](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC kan du ansluta till din OS som körs via seriell port. När du startar CMD SAC, `sacsess.exe` startar `cmd.exe` inom ditt operativsystem. Du kan se att i uppgiften Manager om du kör RDP till den virtuella datorn på samma gång du är ansluten till SAC via funktionen Seriell konsol. CMD som du kommer åt via SAC är samma `cmd.exe` du använder när du är ansluten via RDP. Samma är kommandon och verktyg tillgängliga, inklusive möjligheten att starta PowerShell från den CMD-instansen. Det är stor skillnad mellan SAC och Windows Recovery Environment (WinRE) i den SAC låter dig hantera ditt operativsystem där WinRE startar i en annan, minimal OS. Även om virtuella Azure-datorer inte stöder möjlighet att komma åt WinRE, med funktionen seriekonsolen kan virtuella Azure-datorer hanteras via SAC.

Eftersom SAC är begränsad till en 80 x 24 skärmbufferten med inga rullar tillbaka till `| more` till kommandon för att visa utdata en sida i taget. Använd `<spacebar>` att se nästa sida eller `<enter>` att se nästa rad.  

`SHIFT+INSERT` är klistra in genväg för seriekonsolfönstret.

På grund av SACS begränsad skärmbufferten längre kommandon kan vara enklare att skriva ut i ett lokalt textredigeringsprogram och sedan klistras in i SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera inställningar för Windows-registret
### <a name="verify-rdp-is-enabled"></a>Kontrollera RDP är aktiverad
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Den andra nyckeln (under \Policies) finns bara om relevanta grupprincipinställningen har konfigurerats.

### <a name="enable-rdp"></a>Aktiverar du RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Den andra nyckeln (under \Policies) skulle endast krävs om relevanta grupprincipinställningen hade konfigurerats. Värdet ska skrivas vid nästa uppdatering av grupprinciper, om den har konfigurerats i grupprincipen.

## <a name="manage-windows-services"></a>Hantera Windows-tjänster

### <a name="view-service-state"></a>Visa status för tjänsten
`sc query termservice`
###  <a name="view-service-logon-account"></a>Visa inloggningskonto
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ange inloggningskonto 
`sc config termservice obj= "NT Authority\NetworkService"`

Ett utrymme krävs efter likhetstecknet.
### <a name="set-service-start-type"></a>Ställ in starttypen för tjänsten
`sc config termservice start= demand` 

Ett utrymme krävs efter likhetstecknet. Av möjliga startvärden är `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Ange tjänstens beroenden
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
### <a name="show-nic-properties"></a>Visa egenskaper för nätverkskort
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Visa IPSec-konfiguration
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Aktivera NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ange nätverkskort för att använda DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Mer information om `netsh`, [Klicka här](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Virtuella Azure-datorer ska alltid konfigureras i gästoperativsystemet som du använder DHCP för att skaffa en IP-adress. Azure statiska IP-inställningen använder fortfarande DHCP för att ge den statiska IP-Adressen till den virtuella datorn.
### <a name="ping"></a>Pinga
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port-ping  
Installera telnet-klienten

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testa anslutning

`telnet bing.com 80`

Ta bort telnet-klienten

`dism /online /Disable-Feature /FeatureName:TelnetClient`

När begränsad till metoder som är tillgängliga i Windows som standard kan PowerShell vara en bättre metod för att testa portanslutningen. Se avsnittet PowerShell nedan exempel.
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Visa Windows-brandväggsregel
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`netsh advfirewall set allprofiles state off`

Du kan använda det här kommandot när du felsöker att tillfälligt utesluta Windows-brandväggen. Det kommer att aktivera vid nästa omstart eller när du aktiverar den med hjälp av kommandot nedan. Inte stoppa tjänsten Windows-brandväggen (MPSSVC) eller Base Basfiltreringsmodulen (BFE) tjänsten som sätt att utesluta Windows-brandväggen. Stoppa MPSSVC eller BFE resulterar i alla anslutningar som blockeras.
### <a name="enable-windows-firewall"></a>Aktivera Windows-brandväggen
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Lägga till lokal användare i gruppen
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Bekräfta användarkontot har aktiverats
`net user <username> | find /i "active"`

Azure virtuella datorer som skapas från generaliserad avbildning har det lokala administratörskontot som bytt namn till namnet som angavs vid etablering av virtuella datorer. Så att det inte sannolikt `Administrator`.
### <a name="enable-user-account"></a>Aktivera konto
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Visa egenskaperna för användarkontot
`net user <username>`

Exempelrader intressanta från ett lokalt administratörskonto:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Visa lokala grupper
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
### <a name="query-event-log-errors"></a>Fråga efter felen i händelseloggen
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Ändra `/c:10` till det önskade antalet händelser som ska returneras eller flytta den att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Händelseloggen för fråga efter händelse-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Händelseloggen för fråga efter händelse-ID och Provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga efter händelseloggen efter händelse-ID och providern för de senaste 24 timmarna
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` att titta tillbaka 7 dagar i stället för ett dygn.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelseloggen efter händelse-ID, leverantör och EventData under de senaste 7 dagarna
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-applications"></a>Visa lista med installerade program
`wmic product get Name,InstallDate | sort /r | more`

Den `sort /r` sorterar fallande efter installationsdatum att göra det enkelt att se vad nyligen har installerats. Använd `<spacebar>` att gå vidare till nästa sida i utdata, eller `<enter>` att gå en rad.
### <a name="uninstall-an-application"></a>Avinstallera ett program
`wmic path win32_product where name="<name>" call uninstall`

Ersätt `<name>` med namnet som returneras i kommandot ovan för programmet du vill ta bort.

## <a name="file-system-management"></a>Hantering av systemet
### <a name="get-file-version"></a>Hämta filversion
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Det här exemplet returnerar versionen av den virtuella drivrutin NIC är netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på vilken version av Windows.
### <a name="scan-for-system-file-corruption"></a>Söka efter skadade systemfiler
`sfc /scannow`

Se även [reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Söka efter skadade systemfiler
`dism /online /cleanup-image /scanhealth`

Se även [reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportera filen behörigheter till textfilen
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Spara filen behörigheter till ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Återställa behörigheter från ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Sökvägen när du använder `/restore` måste vara den överordnade mappen till den mapp som du angav när du använder `/save`. I det här exemplet `\RSA` är överordnad den `\MachineKeys` mappen som specificerats i den `/save` exemplet ovan.
### <a name="take-ntfs-ownership-of-a-folder"></a>Ta NTFS ägare till en mapp
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Bevilja NTFS-behörigheter till en mapp rekursivt
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Hantera enheter
### <a name="remove-non-present-pnp-devices"></a>Ta bort icke-presentera PNP-enheter
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Hantera en Grupprincip
### <a name="force-group-policy-update"></a>Framtvinga uppdatering av Grupprincip
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diverse uppgifter
### <a name="show-os-version"></a>Visa OS-version
`ver`

eller 

`wmic os get caption,version,buildnumber /format:list`

eller 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Visa OS installationsdatum
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

Att lägga till `/f` kommer att tvinga program som körs avslutas utan att användarna.
### <a name="detect-safe-mode-boot"></a>Identifiera Start i felsäkert läge
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Windows - kommandon i PowerShell

Om du vill köra PowerShell i SAC, när du når en CMD-kommandotolk, skriver du:

`powershell <enter>`

> [!CAUTION]
> Ta bort modulen PSReadLine från PowerShell-sessionen innan du kör andra PowerShell-kommandon. Det finns ett känt problem där extra tecken kan införas i text har klistrats in från Urklipp om PSReadLine körs i en PowerShell-session i SAC.

Kontrollera först om PSReadLine har lästs in. Den läses som standard på Windows Server 2016, Windows 10 och senare versioner av Windows. Det är bara finns i tidigare versioner av Windows om den hade installerats manuellt. 

Om det här kommandot returnerar du en kommandotolk med inga utdata, sedan modulen lästes inte in och du kan fortsätta att använda PowerShell-session i SAC som vanligt.

`get-module psreadline`

Om ovanstående kommando returnerar Modulversion PSReadLine, kör du följande kommando för att ta bort den. Det här kommandot tar inte bort eller avinstallera modulen, det bara tar bort den från den aktuella PowerShell-sessionen.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera inställningar för Windows-registret
### <a name="verify-rdp-is-enabled"></a>Kontrollera RDP är aktiverad
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Den andra nyckeln (under \Policies) finns bara om relevanta grupprincipinställningen har konfigurerats.
### <a name="enable-rdp"></a>Aktiverar du RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Den andra nyckeln (under \Policies) skulle endast krävs om relevanta grupprincipinställningen hade konfigurerats. Värdet ska skrivas vid nästa uppdatering av grupprinciper, om den har konfigurerats i grupprincipen.
## <a name="manage-windows-services"></a>Hantera Windows-tjänster
### <a name="view-service-details"></a>Visa information om tjänsten
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` kan användas men omfattar inte inloggningskontot för tjänsten. `Get-WmiObject win32-service` gör.
### <a name="set-service-logon-account"></a>Ange inloggningskonto
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

När du använder ett tjänstkonto än `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, eller `LocalSystem`, ange lösenordet för som senaste (åttonde) argument efter namnet på kontot.
### <a name="set-service-startup-type"></a>Ange tjänstens starttyp
`set-service termservice -startuptype Manual`

`Set-service` accepterar `Automatic`, `Manual`, eller `Disabled` för starttyp.
### <a name="set-service-dependencies"></a>Ange tjänstens beroenden
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Starta tjänsten
`start-service termservice`
### <a name="stop-service"></a>Stoppa tjänsten
`stop-service termservice`
## <a name="manage-networking-features"></a>Hantera nätverksfunktioner
### <a name="show-nic-properties"></a>Visa egenskaper för nätverkskort
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

eller 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` är tillgängligt i 2012 +, för användning av 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Visa IP-egenskaper
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Aktivera NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

eller

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` är tillgängligt i 2012 +, för användning av 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Ange nätverkskort för att använda DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` är tillgängligt på 2012 +. Använd för 2008R2 `Get-WmiObject`. Virtuella Azure-datorer ska alltid konfigureras i gästoperativsystemet som du använder DHCP för att skaffa en IP-adress. Azure statiska IP-inställningen använder fortfarande DHCP för att ge den IP-Adressen till den virtuella datorn.
### <a name="ping"></a>Pinga
`test-netconnection`

eller

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` utan parametrar kommer att försöka pinga `internetbeacon.msedge.net`. Det är tillgängligt på 2012 +. 2008R2 använder `Get-WmiObject` i det andra exemplet.
### <a name="port-ping"></a>Port-Ping
`test-netconnection -ComputerName bing.com -Port 80`

eller

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` är tillgängligt på 2012 +. För 2008R2 användning `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`resolve-dnsname bing.com` 

eller 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` är tillgängligt på 2012 +. Använd för 2008R2 `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Visa Windows brandväggsregel efter namn
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Visa Windows brandväggsregel av port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

eller

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` är tillgängligt på 2012 +. 2008R2 använder den `hnetcfg.fwpolicy2` COM-objektet. 
### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` är tillgängligt på 2012 +. Använd för 2008R2 `netsh advfirewall` som refereras till i avsnittet CMD ovan.
## <a name="manage-users-and-groups"></a>Hantera användare och grupper
### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Bekräfta användarkontot har aktiverats
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

eller 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` är tillgängligt på 2012 +. Använd för 2008R2 `Get-WmiObject`. Det här exemplet visar det inbyggda lokala administratörskontot, som har alltid SID `S-1-5-21-*-500`. Azure virtuella datorer som skapas från generaliserad avbildning har det lokala administratörskontot som bytt namn till namnet som angavs vid etablering av virtuella datorer. Så att det inte sannolikt `Administrator`.
### <a name="add-local-user-to-local-group"></a>Lägga till lokal användare i gruppen
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Aktivera lokalt användarkonto
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Det här exemplet aktiverar det inbyggda lokala administratörskontot, som har alltid SID `S-1-5-21-*-500`. Azure virtuella datorer som skapas från generaliserad avbildning har det lokala administratörskontot som bytt namn till namnet som angavs vid etablering av virtuella datorer. Så att det inte sannolikt `Administrator`.
### <a name="view-user-account-properties"></a>Visa egenskaperna för användarkontot
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

eller 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` är tillgängligt på 2012 +. Använd för 2008R2 `Get-WmiObject`. Det här exemplet visar det inbyggda lokala administratörskontot, som har alltid SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Visa lokala grupper
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` är tillgängligt på 2012 +. Använd för 2008R2 `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
### <a name="query-event-log-errors"></a>Fråga efter felen i händelseloggen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Ändra `/c:10` till det önskade antalet händelser som ska returneras eller flytta den att returnera alla händelser som matchar filtret.
### <a name="query-event-log-by-event-id"></a>Händelseloggen för fråga efter händelse-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Händelseloggen för fråga efter händelse-ID och Provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga efter händelseloggen efter händelse-ID och providern för de senaste 24 timmarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` att titta tillbaka 7 dagar i stället för ett dygn. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelseloggen efter händelse-ID, leverantör och EventData under de senaste 7 dagarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
### <a name="list-installed-software"></a>Lista över installerad programvara
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Avinstallera program
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Hantering av systemet
### <a name="get-file-version"></a>Hämta filversion
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Det här exemplet returnerar versionen av den virtuella NIC-drivrutin som heter netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på vilken version av Windows.
### <a name="download-and-extract-file"></a>Ladda ned och extrahera filen
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Det här exemplet skapar en `c:\bin` mappen sedan laddar ned och extraherar Sysinternals-uppsättning med verktyg i `c:\bin`.
## <a name="miscellaneous-tasks"></a>Diverse uppgifter
### <a name="show-os-version"></a>Visa OS-version
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Visa OS installationsdatum
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Visa senaste starttid
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Visa Windows drifttid
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Returnerar drifttid som `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, till exempel `49:16:48:00.00`. 
### <a name="restart-windows"></a>Starta om Windows
`restart-computer`

Att lägga till `-force` kommer att tvinga program som körs avslutas utan att användarna.
## <a name="instance-metadata"></a>Instance Metadata

Du kan fråga efter Azure-instans metadata från dina Azure virtuell dator för att visa information om till exempel osType, plats, vmSize, vmId, namn, resourceGroupName, prenumerations-ID, privateIpAddress och publicIpAddress.

Fråga instans metadata kräver felfri gäst nätverksanslutning, eftersom det gör ett REST-anrop via Azure-värden till tjänsten instans metadata. Så om du kan fråga instans metadata som talar om gästen kan kommunicera över nätverket till en Azure-värdbaserade tjänst.

Mer information finns i [tjänsten Azure Instance Metadata](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Instans-metadata
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>OS-typ (instans Metadata)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Plats (instans Metadata)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Storlek (instans Metadata)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID för virtuell dator (instans Metadata)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Namn på virtuell dator (instans Metadata)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Namn på resursgrupp (instans Metadata)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Prenumerations-ID (instans Metadata)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Taggar (instans Metadata)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Placeringsgrupp-ID (instans Metadata)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Plattformsfeldomänen (instans Metadata)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Plattformen för Uppdateringsdomänen (instans Metadata)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 privata IP-adress (instans Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 offentlig IP-adress (instans Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-adress för undernät / Prefix (instans Metadata)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6-IP-adress (instans Metadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-adress (instans Metadata)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Nästa steg
* Sidan huvudkonsolen seriell Windows-dokumentationen finns [här](serial-console-windows.md).
* Seriekonsolen är också tillgängligt för [Linux](serial-console-linux.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
