---
title: Virtuella Azure-datorn seriekonsolen | Microsoft Docs
description: Dubbelriktad seriekonsolen för virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: e891e9c9fd87f370f0c98639ff0c6fc5b8cc81af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuella seriekonsolen (förhandsgranskning) 


Seriekonsolen för virtuell dator i Azure ger tillgång till en textbaserad konsol för Linux och Windows-datorer. Den här seriell anslutning är att COM1 seriell port för den virtuella datorn och ger åtkomst till den virtuella datorn och inte är relaterade till nätverk för virtuella datorer / operativsystem systemtillstånd. Åtkomst till seriekonsol för en virtuell dator kan gör du för närvarande endast via Azure-portalen och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

> [!Note] 
> Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Tjänsten är för närvarande **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgängligt för globala Azure-regioner. Nu är seriekonsolen inte tillgängliga Azure Government, Azure Tyskland och Azure Kina molnet.

 

## <a name="prerequisites"></a>Förutsättningar 

* Virtuell dator måste ha [starta diagnostik](boot-diagnostics.md) aktiverad 
* Det konto som använder seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [starta diagnostik](boot-diagnostics.md) storage-konto. 

## <a name="open-the-serial-console"></a>Öppna seriekonsolen
seriekonsolen för virtuella datorer är endast tillgänglig via [Azure-portalen](https://portal.azure.com). Nedan följer du stegen för att komma åt seriekonsolen för virtuella datorer via portalen 

  1. Öppna Azure-portalen
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. På översiktssidan för den virtuella datorn öppnas.
  4. Rulla stöd + avsnittet om felsökning och klicka på alternativet seriekonsolen (förhandsversion). Nya rutan med seriekonsolen öppnas och anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Inaktivera funktionen
Funktionen seriekonsolen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn starta diagnostik.

## <a name="serial-console-security"></a>Seriekonsolen säkerhet 

### <a name="access-security"></a>Åtkomstsäkerhet 
Åtkomst till seriekonsol är begränsad till användare som har [VM deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller senare åtkomst till den virtuella datorn. Om din AAD-klient kräver Multi-Factor Authentication sedan åtkomst till seriekonsolen måste också MFA eftersom dess åtkomst [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data skickas tillbaka och tillbaka är krypterad under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsolen för närvarande är inloggad på [starta diagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggar för den virtuella datorn. Åtkomst till dessa loggar ägs och styrs av administratören för Azure virtuella datorer.  

>[!CAUTION] 
När inga lösenord för konsolen loggas om kommandon i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller andra former av personligt identifierbar Information (PII), skrivs de till startdiagnostikinställningar för virtuell dator loggar, tillsammans med alla andra synliga text, som en del av implementeringen av seriella konsolen Rulla tillbaka funktioner. Dessa loggar är cirkulär och bara personer med läsbehörighet till diagnostik storage-konto har åtkomst till dem, men vi rekommenderar följande bästa praxis att använda Remote Desktop för något som kan innebära hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsol och en annan användare har begär åtkomst till att samma virtuella dator, kommer att kopplas från den första användaren och andra användare som är anslutna på ett sätt som den första användaren Ständiga eller ut ur den fysiska konsolen och en ny användaren placerad.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas! Möjlighet att Påtvinga en logga ut vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande Översikt. För Windows, det är en automatisk tidsgräns har aktiverats i SAC, men du kan konfigurera terminal timeout-inställningen för Linux. 


## <a name="accessing-serial-console-for-windows"></a>Åtkomst till seriekonsolen för Windows 
Senare Windows Server-avbildningar i Azure har [särskilda administrationskonsolen](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) aktiverade som standard. SAC stöds på Windows serverversioner, men är inte tillgängligt i klientversioner (till exempel Windows 10, Windows 8 eller Windows 7). Om du vill aktivera seriekonsolen för Windows-datorer som skapats med hjälp av Använd Feb2018 eller lägre bilder följande steg: 

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Starta om systemet för SAC-konsolen måste vara aktiverat

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Om nödvändiga SAC kan aktiveras offline 

1. Ansluta windows disken som du vill använda SAC som konfigurerats för som en datadisk till en befintlig virtuell dator. 
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Hur vet jag om SAC är aktiverad eller inte 

Om [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) aktiveras inte seriekonsolen visas inte SAC-prompten. Den kan visa en VM hälsoinformation i vissa fall eller det skulle vara tomt.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Aktiverar startmenyn ska visas i seriekonsolen 

Om du behöver aktivera Windows-startprogrammet uppmanas du för att visa i seriekonsolen, kan du lägga till följande ytterligare alternativ Windows startprogram.

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Starta om datorn för startmenyn aktiveras

> [!NOTE] 
> På den här platsen stöd för funktionen nycklar inte är aktiverat, om du behöver avancerade startalternativ använda bcdedit/set {aktuella} onetimeadvancedoptions på, se [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) mer information

## <a name="windows-commands---cmd"></a>Kommandon i Windows - CMD 

Det här avsnittet innehåller exempel på kommandon för att utföra vanliga aktiviteter i scenarier där du kan behöva använda SAC att komma åt den virtuella datorn, till exempel när du behöver felsöka RDP-anslutningsfel.

SAC har inkluderats i alla versioner av Windows sedan Windows Server 2003, men är inaktiverad som standard. SAC förlitar sig på den `sacdrv.sys` kernel-drivrutinen den `Special Administration Console Helper` service (`sacsvr`), och `sacsess.exe` process. Mer information finns i [nödsituation Management Services-verktyg och inställningar för](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC kan du ansluta till ditt operativsystem via serieport. När du startar CMD SAC, `sacsess.exe` startar `cmd.exe` i ditt operativsystem. Du kan se att aktiviteten Manager om RDP till den virtuella datorn på samma gång du är ansluten till SAC via seriekonsolen-funktionen. CMD som du kommer åt via SAC är samma `cmd.exe` du använder när du är ansluten via RDP. Samma är kommandon och verktyg tillgängliga, inklusive möjligheten att starta PowerShell från den CMD-instansen. Som en största skillnaden mellan SAC och Windows Recovery Environment (WinRE) i den SAC är att låta dig att hantera ditt operativsystem där WinRE startar i en annan, minimal OS. Virtuella Azure-datorer kan hanteras via SAC medan virtuella Azure-datorer inte stöder möjlighet att komma åt WinRE, med funktionen seriekonsol.

Eftersom SAC är begränsad till en 80 x 24 skärmbufferten med Ingen rullning tillbaka till `| more` till kommandon för att visa sidan utdata en i taget. Använd `<spacebar>` att se nästa sida eller `<enter>` att se nästa rad.  

`SHIFT+INSERT` är klistra in genväg för seriekonsolfönstret.

På grund av SACS begränsad skärmbufferten längre kommandon kan vara enklare att skriva ut i en lokal textredigerare och sedan klistras in i SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera Windows-registerinställningar
#### <a name="verify-rdp-is-enabled"></a>Kontrollera RDP är aktiverad
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Den andra nyckeln (under \Policies) kommer endast finnas om relevanta grupprincipinställningen är konfigurerad.

#### <a name="enable-rdp"></a>Aktivera RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Den andra nyckeln (under \Policies) behövs bara om den relevanta grupprincipinställningen hade konfigurerats. Värdet ska skrivas vid nästa uppdatering av grupprinciper, om den har konfigurerats i grupprincipen.

### <a name="manage-windows-services"></a>Hantera Windows-tjänster

#### <a name="view-service-state"></a>Visa tjänstens status
`sc query termservice`
####  <a name="view-service-logon-account"></a>Visa inloggningskonto
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Ange inloggningskontot för tjänsten 
`sc config termservice obj= "NT Authority\NetworkService"`

Ett utrymme krävs efter likhetstecknet.
#### <a name="set-service-start-type"></a>Ange starttypen för tjänsten
`sc config termservice start= demand` 

Ett utrymme krävs efter likhetstecknet. Möjliga startvärden är `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Ange beroenden för tjänsten
`sc config termservice depend= RPCSS`

Ett utrymme krävs efter likhetstecknet.
#### <a name="start-service"></a>Starta tjänsten
`net start termservice`

eller

`sc start termservice`
#### <a name="stop-service"></a>Stoppa tjänsten
`net stop termservice`

eller

`sc stop termservice`
### <a name="manage-networking-features"></a>Hantera nätverksfunktioner
#### <a name="show-nic-properties"></a>Visa egenskaper för nätverkskort
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Visa IP-egenskaper
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Visa IPSec-konfiguration
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Aktivera NIC
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Ange NIC för att använda DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Virtuella Azure-datorer ska alltid vara konfigurerade i gäst-OS för att använda DHCP för att erhålla en IP-adress. Azure statiska IP-inställningen fortfarande använder DHCP för att ge statiska IP-Adressen till den virtuella datorn.
#### <a name="ping"></a>Pinga
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Port ping  
Installera telnet-klienten

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testa anslutning

`telnet bing.com 80`

Ta bort telnet-klienten

`dism /online /Disable-Feature /FeatureName:TelnetClient`

När begränsat till metoder som är tillgängliga i Windows som standard, kan det vara en bättre metod för att testa anslutningen för port PowerShell. Se avsnittet PowerShell nedan exempel.
#### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Visa Windows-brandväggsregel
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`netsh advfirewall set allprofiles state off`

Du kan använda det här kommandot när du felsöker tillfälligt utesluta Windows-brandväggen. Kommer det att aktivera vid nästa omstart eller när du enaable den med hjälp av kommandot nedan. Inte stoppa tjänsten Windows-brandväggen (MPSSVC) eller Base filtrering motorn (BFE) tjänsten sätt att utesluta Windows-brandväggen. Stoppa MPSSVC eller BFE leder till att alla anslutningar som är blockerade.
#### <a name="enable-windows-firewall"></a>Aktivera Windows-brandväggen
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Hantera användare och grupper
#### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Lägg till lokal användare i gruppen
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Kontrollera att användarkontot är aktiverat
`net user <username> | find /i "active"`

Det lokala administratörskontot som ändras till namnet som angetts under etablering av virtuell dator har virtuella Azure-datorer skapas från generaliserad avbildning. Så blir det vanligtvis inte `Administrator`.
#### <a name="enable-user-account"></a>Aktivera konto
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Visa egenskaperna för användarkontot
`net user <username>`

Exempelrader intressanta från ett lokalt administratörskonto:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Visa lokala grupper
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
#### <a name="query-event-log-errors"></a>Frågefel händelseloggen
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Ändra `/c:10` att antalet händelser som ska returneras eller flytta den att returnera alla händelser som matchar filtret.
#### <a name="query-event-log-by-event-id"></a>Händelseloggen för fråga efter händelse-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Händelseloggen för fråga efter händelse-ID och Provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga händelseloggen efter händelse-ID och providern efter de senaste 24 timmarna
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` att leta upp 7 dagar i stället för 24 timmar.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelseloggen av händelse-ID och providern EventData under de senaste 7 dagarna
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
#### <a name="list-installed-applications"></a>Visa installerade program
`wmic product get Name,InstallDate | sort /r | more`

Den `sort /r` installationsdatum sorterar fallande genom att göra det lättare att se vad nyligen har installerats. Använd `<spacebar>` att gå vidare till nästa sida i utdata, eller `<enter>` till en rad i förväg.
#### <a name="uninstall-an-application"></a>Avinstallera ett program
`wmic path win32_product where name="<name>" call uninstall`

Ersätt `<name>` med namnet returneras i kommandot ovan för programmet du vill ta bort.

### <a name="file-system-management"></a>Filen System Management
#### <a name="get-file-version"></a>Hämta filversion
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Det här exemplet returnerar versionen av den virtuella drivrutin NIC netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på vilken version av Windows.
#### <a name="scan-for-system-file-corruption"></a>Sök efter skadade systemfiler
`sfc /scannow`

Se även [reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Sök efter skadade systemfiler
`dism /online /cleanup-image /scanhealth`

Se även [reparera en Windows-avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Exportera filen behörigheter till textfil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Spara filbehörigheter ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Återställ behörigheter för filer från ACL-fil
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Sökvägen när du använder `/restore` måste vara den överordnade mappen till den mapp du angav när du använder `/save`. I det här exemplet `\RSA` är överordnad den `\MachineKeys` mappen som anges i den `/save` exemplet ovan.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Ta NTFS ägare till en mapp
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Bevilja NTFS-behörighet till en mapp rekursivt
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Hantera enheter
#### <a name="remove-non-present-pnp-devices"></a>Ta bort icke-presentera PNP-enheter
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Hantera en Grupprincip
#### <a name="force-group-policy-update"></a>Tvinga uppdatering av Grupprincip
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Diverse uppgifter
#### <a name="show-os-version"></a>Visa OS-version
`ver`

eller 

`wmic os get caption,version,buildnumber /format:list`

eller 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Visa OS installationsdatum
`systeminfo | find /i "original"`

eller 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Visa senaste starten
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Visa tidszon
`systeminfo | find /i "time zone"`

eller

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Starta om Windows
`shutdown /r /t 0`

Lägga till `/f` tvingar program som körs avslutas utan att användarna.
#### <a name="detect-safe-mode-boot"></a>Identifiera Start i felsäkert läge
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Kommandon i Windows - PowerShell

För att köra PowerShell i SAC, när du har nått en kommandotolk, skriver du:

`powershell <enter>`

>[!CAUTION]
Ta bort modulen PSReadLine från PowerShell-sessionen innan du kör andra PowerShell-kommandon. Det finns ett känt problem där extra tecken kan vara introduceras i text har klistrats in från Urklipp om PSReadLine körs i PowerShell-sessionen i SAC.

Kontrollera först om PSReadLine har lästs in. Det har lästs in som standard på Windows Server 2016, Windows 10 och senare versioner av Windows. Den kan bara finnas på tidigare versioner av Windows om den hade installerats manuellt. 

Om det här kommandot returnerar till en fråga med inga utdata, sedan modulen lästes inte in och du kan fortsätta använda PowerShell-session i SAC som vanligt.

`get-module psreadline`

Om ovanstående kommando returnerar Modulversion PSReadLine, kör du följande kommando för att ta bort den. Det här kommandot tar inte bort eller avinstallera modul, det endast tar bort den från den aktuella PowerShell-sessionen.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Visa och redigera Windows-registerinställningar
#### <a name="verify-rdp-is-enabled"></a>Kontrollera RDP är aktiverad
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Den andra nyckeln (under \Policies) kommer endast finnas om relevanta grupprincipinställningen är konfigurerad.
#### <a name="enable-rdp"></a>Aktivera RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Den andra nyckeln (under \Policies) behövs bara om den relevanta grupprincipinställningen hade konfigurerats. Värdet ska skrivas vid nästa uppdatering av grupprinciper, om den har konfigurerats i grupprincipen.
### <a name="manage-windows-services"></a>Hantera Windows-tjänster
#### <a name="view-service-details"></a>Visa information om tjänsten
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` kan användas men inte innehåller inloggningskontot för tjänsten. `Get-WmiObject win32-service` stöder.
#### <a name="set-service-logon-account"></a>Ange inloggningskontot för tjänsten
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

När du använder ett tjänstkonto än `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, eller `LocalSystem`, ange lösenordet för som sista argument (åttonde) efter namnet på kontot.
#### <a name="set-service-startup-type"></a>Ange tjänstens starttyp
`set-service termservice -startuptype Manual`

`Set-service` accepterar `Automatic`, `Manual`, eller `Disabled` för starttyp.
#### <a name="set-service-dependencies"></a>Ange beroenden för tjänsten
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Starta tjänsten
`start-service termservice`
#### <a name="stop-service"></a>Stoppa tjänsten
`stop-service termservice`
### <a name="manage-networking-features"></a>Hantera nätverksfunktioner
#### <a name="show-nic-properties"></a>Visa egenskaper för nätverkskort
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

eller 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` är tillgängligt i 2012 +, för 2008R2 `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Visa IP-egenskaper
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Aktivera NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

eller

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` är tillgängligt i 2012 +, för 2008R2 `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Ange NIC för att använda DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` är tillgänglig på 2012 +. 2008R2 använder `Get-WmiObject`. Virtuella Azure-datorer ska alltid vara konfigurerade i gäst-OS för att använda DHCP för att erhålla en IP-adress. Azure statiska IP-inställningen fortfarande använder DHCP för att ge IP-Adressen till den virtuella datorn.
#### <a name="ping"></a>Pinga
`test-netconnection`

eller

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` utan några parametrar kommer att försöka skicka ping `internetbeacon.msedge.net`. Det är tillgängligt på 2012 +. 2008R2 använder `Get-WmiObject` i det andra exemplet.
#### <a name="port-ping"></a>Ping-port
`test-netconnection -ComputerName bing.com -Port 80`

eller

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` är tillgänglig på 2012 +. För 2008R2 användning `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Testa DNS-namnmatchning
`resolve-dnsname bing.com` 

eller 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` är tillgänglig på 2012 +. 2008R2 använder `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Visa Windows-brandväggsregel efter namn
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Visa Windows-brandväggsregel av port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

eller

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` är tillgänglig på 2012 +. 2008R2 använder den `hnetcfg.fwpolicy2` COM-objekt. 
#### <a name="disable-windows-firewall"></a>Inaktivera Windows-brandväggen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` är tillgänglig på 2012 +. 2008R2 använder `netsh advfirewall` som refereras till i avsnittet CMD ovan.
### <a name="manage-users-and-groups"></a>Hantera användare och grupper
#### <a name="create-local-user-account"></a>Skapa lokalt användarkonto
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Kontrollera att användarkontot är aktiverat
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

eller 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` är tillgänglig på 2012 +. 2008R2 använder `Get-WmiObject`. Det här exemplet visar det inbyggda lokala administratörskontot, som alltid innehåller SID `S-1-5-21-*-500`. Det lokala administratörskontot som ändras till namnet som angetts under etablering av virtuell dator har virtuella Azure-datorer skapas från generaliserad avbildning. Så blir det vanligtvis inte `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Lägg till lokal användare i gruppen
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Aktivera lokalt användarkonto
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Det här exemplet kan det inbyggda lokala administratörskontot, som alltid innehåller SID `S-1-5-21-*-500`. Det lokala administratörskontot som ändras till namnet som angetts under etablering av virtuell dator har virtuella Azure-datorer skapas från generaliserad avbildning. Så blir det vanligtvis inte `Administrator`.
#### <a name="view-user-account-properties"></a>Visa egenskaperna för användarkontot
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

eller 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` är tillgänglig på 2012 +. 2008R2 använder `Get-WmiObject`. Det här exemplet visar det inbyggda lokala administratörskontot, som alltid innehåller SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Visa lokala grupper
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` är tillgänglig på 2012 +. 2008R2 använder `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Hantera Windows-händelseloggen
#### <a name="query-event-log-errors"></a>Frågefel händelseloggen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Ändra `/c:10` att antalet händelser som ska returneras eller flytta den att returnera alla händelser som matchar filtret.
#### <a name="query-event-log-by-event-id"></a>Händelseloggen för fråga efter händelse-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Händelseloggen för fråga efter händelse-ID och Provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Fråga händelseloggen efter händelse-ID och providern efter de senaste 24 timmarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Använd `604800000` att leta upp 7 dagar i stället för 24 timmar. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Fråga händelseloggen av händelse-ID och providern EventData under de senaste 7 dagarna
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visa eller ta bort installerade program
#### <a name="list-installed-software"></a>Lista över installerad programvara
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Avinstallera program
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Filen System Management
#### <a name="get-file-version"></a>Hämta filversion
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Det här exemplet returnerar versionen av virtuella NIC-drivrutin som heter netvsc.sys, netvsc63.sys eller netvsc60.sys beroende på vilken version av Windows.
#### <a name="download-and-extract-file"></a>Ladda ned och extrahera filen
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Det här exemplet skapas en `c:\bin` , sedan hämtar och packar upp Sysinternals-uppsättning med verktyg i `c:\bin`.
### <a name="miscellaneous-tasks"></a>Diverse uppgifter
#### <a name="show-os-version"></a>Visa OS-version
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Visa OS installationsdatum
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Visa senaste starten
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Visa Windows drifttid
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Returnerar drifttid som `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, till exempel `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Starta om Windows
`restart-computer`

Lägga till `-force` tvingar program som körs avslutas utan att användarna.
### <a name="instance-metadata"></a>Instansen Metadata

Du kan fråga Azure instans metadata från i Azure-dator för att visa information, till exempel osType, plats, vmSize, vmId, namn, resourceGroupName, subscriptionId, privateIpAddress och publicIpAddress.

Frågar instans metadata kräver felfri gäst nätverksanslutningen, eftersom den gör ett REST-anrop via Azure-värden till tjänsten instans metadata. Så om du ska kunna fråga instans metadata anger som du gästen kan kommunicera över nätverket till en Azure-värdtjänsten.

Mer information finns i [Azure instans Metadata tjänsten](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Instansen metadata
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>OS-typen (instans Metadata)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Plats (instans Metadata)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Storlek (instans Metadata)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>VM-ID (instans Metadata)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Namn på virtuell dator (instans Metadata)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Resursgruppens namn (instans Metadata)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>Prenumerations-ID (instans Metadata)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Taggar (instans Metadata)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>Placering av grupp-ID (instans Metadata)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Plattformsfeldomänen (instans Metadata)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Plattform Uppdateringsdomän (instans Metadata)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 privata IP-adress (instans Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 offentlig IP-adress (instans Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-adress för undernät / Prefix (instansen Metadata)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IPv6-IP-adress (instans Metadata)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC-adress (instans Metadata)
`$im.network.interface.macAddress`

## <a name="errors"></a>Fel
De flesta felen är tillfälliga slag och försöker igen anslutningsadressen dessa. Tabellen nedan visar en lista över fel och minskning 

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta startdiagnostikinställningar för '<VMNAME>'. Se till att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [starta diagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i ett stoppat frigjord tillstånd. Starta den virtuella datorn och försök seriekonsolen anslutningen. | Virtuell dator måste vara i startat tillstånd för att komma åt seriekonsolen
Du har inte behörighet att använda den här Virtuella seriekonsol. Kontrollera att du har minst rollbehörigheter för VM-deltagare.| Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information
Det gick inte att fastställa resursgruppen för start diagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information

## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i preview steg för seriekonsolen åtkomst, vi gå igenom några kända problem, nedan är listan över dem med möjliga lösningar 

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med virtuella scale set instans seriekonsol | Åtkomst till seriekonsol för virtual machine scale set instanser stöds inte vid tidpunkten för förhandsgranskning.
Träffa ange när anslutningen banderoll inte visar en logg i Kommandotolken | [Träffa ange händer ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Endast hälsoinformation visas när du ansluter till en Windows VM| [Windows hälsa signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Det går inte att skriva vid SAC fråga om kernel-felsökning är aktiverad | RDP till den virtuella datorn och kör `bcdedit /debug {current} off` från en upphöjd kommandotolk. Om det går inte att RDP du i stället kan koppla OS-disk till en annan virtuell dator i Azure och ändra den medan bifogas som en data-disk med `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, sedan växla tillbaka disken.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR. Hur skickar jag feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (primära) skicka feedback via azserialhelp@microsoft.com eller i den virtuella dator kategorin http://feedback.azure.com 

**FRÅGOR. Ett felmeddelande ”befintliga konsolen har motstridiga OS-typen” Windows ”med den begärda OS-typen av Linux?**

A. Detta är ett känt problem att åtgärda det genom att helt enkelt öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i bash-läge och försök igen.

**FRÅGOR. Jag kan inte komma åt seriekonsolen var kan jag filen ett supportärende?**

A. Den här förhandsgranskningsfunktionen omfattas via Azure villkoren. Stöd för detta hanteras bör via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* Seriekonsolen är också tillgängligt för [Linux](../linux/serial-console.md) virtuella datorer
* Lär dig mer om [bootdiagnostics](boot-diagnostics.md)
