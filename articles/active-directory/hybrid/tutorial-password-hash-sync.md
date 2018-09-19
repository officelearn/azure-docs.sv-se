---
title: 'Självstudie: Integrera en enda AD-skog till Azure med lösenordets hash-synkronisering (PHS) | Microsoft Docs'
description: Visar hur du ställer in en hybridmiljö identitet med hjälp av hash-synkronisering av lösenord.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 81c88c90ff24cb5e0ab143237fb08678067ea993
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311712"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Självstudie: Integrera en enda AD-skog med hjälp av lösenordets hash-synkronisering (PHS)

![Skapa](media/tutorial-password-hash-sync/diagram.png)

Självstudien vägleder dig genom att skapa en hybridmiljö identitet med hjälp av hash-synkronisering av lösenord.  Den här miljön kan sedan användas för att testa eller för att få mer bekant med hur en hybrididentitet fungerar.

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som krävs för den här kursen
- En dator med [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) installerad.  Vi rekommenderar att du gör detta på antingen en [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) eller en [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) dator.
- En [externt nätverkskort](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) så att den virtuella datorn att kommunicera med internet.
- En [Azure-prenumeration](https://azure.microsoft.com/free)
- En kopia av Windows Server 2016

> [!NOTE]
> Den här självstudien använder PowerShell-skript så att du kan skapa självstudiekursen miljö inom den snabbaste tid.  Var och en av skript som använder variabler som deklareras i början av skript.  Du kan och bör ändra variabler för att avspegla din miljö.
>
>Skript som används för skapa en allmän Active Directory-miljö innan du installerar Azure AD Connect.  De är relevanta för alla självstudier.
>
> Kopior av PowerShell-skript som används i den här självstudien finns på Github [här](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Det första som vi behöver göra för att få vår hybrid identity miljö och körs är att skapa en virtuell dator som ska användas som vår lokala Active Directory-server.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Slutföra distributionen av operativsystemet
För att du är klar med att skapa den virtuella datorn kan behöva du slutför installationen av operativsystemet.

1. Hyper-V-hanteraren dubbelklickar du på den virtuella datorn
2. Klicka på knappen Start.
3.  Du uppmanas att ”tryck på valfri tangent för att starta från CD- eller DVD”. Gå vidare och gör.
4. På Windows Server-startskärm väljer du språk och klickar på **nästa**.
5. Klicka på **installera nu**.
6. Ange licensnyckeln och klicka på **nästa**.
7. Kontrollera ** jag accepterar licensvillkoren och klicka på **nästa**.
8. Välj **anpassad: Installera Windows endast (Avancerat)**
9. Klicka på **Nästa**
10. När installationen är klar startas den virtuella datorn, logga in och kör Windows uppdaterar för att säkerställa att den virtuella datorn är den mest aktuella.  Installera de senaste uppdateringarna.

## <a name="install-active-directory-prerequisites"></a>Installera Active Directory-krav
Nu när vi har en virtuell dator för kan behöva vi göra några saker innan du installerar Active Directory.  Vi behöver det vill säga att byta namn på den virtuella datorn, ange en statisk IP-adress och DNS-information och installera Remote Server Administration tools.   Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Skapa en Windows Server AD-miljö
Nu när vi har den virtuella datorn skapas och den har bytt namn och har en statisk IP-adress, kan vi gå vidare och installera och konfigurera Active Directory Domain Services.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Skapa en Windows Server AD-användare
Nu när vi har vår Active Directory-miljö kan behöver vi ett testkonto.  Det här kontot kommer att skapas i vår lokala AD-miljö och sedan synkroniseras med Azure AD.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Skapa en Azure AD-klient
Nu ska vi skapa en Azure AD-klient så att vi kan synkronisera våra användare till molnet.  Skapa en ny Azure AD-klientorganisation, gör du följande.

1. Bläddra till den [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj den **plus-ikonen (+)** och Sök efter **Azure Active Directory**.
3. Välj **Azure Active Directory** i sökresultatet.
4. Välj **Skapa**.</br>
![Skapa](media/tutorial-password-hash-sync/create1.png)</br>
5. Ange en **namn på organisationen** tillsammans med den **ursprungliga domännamnet**. Välj sedan **Skapa**. Detta skapar din katalog.
6. När den har slutförts klickar du på den **här** länken för att hantera katalogen.

## <a name="create-a-global-administrator-in-azure-ad"></a>Skapa en global administratör i Azure AD
Nu när vi har en Azure AD-klient ska vi skapa ett globalt administratörskonto.  Det här kontot används för att skapa ett Azure AD Connector-konto under Azure AD Connect-installationen.  Azure AD-anslutningskontot används för att skriva information till Azure AD.   Konto för att skapa den globala administratören göra följande.

1.  Under **hantera**väljer **användare**.</br>
![Skapa](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Välj **alla användare** och välj sedan **+ ny användare**.
3.  Ange ett namn och användarnamn för den här användaren. Det här är din globala administratör för klienten. Du kan också ändra den **katalogroll** till **Global administratör.** Du kan också visa det tillfälliga lösenordet. När du är klar väljer du **skapa**.</br>
![Skapa](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. När den har slutförts, öppna en ny webbläsare och logga in på myapps.microsoft.com med hjälp av det nya globala administratörskontot och det tillfälliga lösenordet.
5. Ändra lösenordet för den globala administratören till något som du kommer ihåg.

## <a name="download-and-install-azure-ad-connect"></a>Ladda ned och installera Azure AD Connect
Nu är det dags att ladda ned och installera Azure AD Connect.  När det har installerats kör vi via Snabbinstallation.  Gör följande:

1. Ladda ned [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navigera till och dubbelklicka på **AzureADConnect.msi**.
3. På välkomstskärmen markerar du kryssrutan för att acceptera licensvillkoren och klickar sedan på **Fortsätt**.  
4. På skärmen Standardinställningar klickar du på **Använd standardinställningar**.</br>  
![Skapa](media/tutorial-password-hash-sync/express1.png)</br>
5. På skärmen Anslut till Azure AD, anger du användarnamnet och lösenordet global administratör för Azure AD. Klicka på **Nästa**.  
6. På skärmen Anslut till AD DS anger du användarnamnet och lösenordet för ett företagsadministratörskonto. Klicka på **Nästa**.  
7. Klicka på **Installera** på skärmen Klart att konfigurera.
8. När installationen är klar klickar du på **Avsluta**.
9. När installationen har slutförts kan du logga ut och logga in igen innan du använder Synchronization Service Manager eller Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Kontrollera användare skapas och synkronisering sker
Vi kommer nu att verifiera att de användare som vi hade våra lokala katalog har synkroniserats och finns nu i Azure AD-klient.  Tänk på att det kan ta några timmar att slutföra.  Verifiera användarna synkroniseras gör du följande.


1. Bläddra till den [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Till vänster, Välj **Azure Active Directory**
3. Under **hantera**väljer **användare**.
4. Kontrollera att du ser de nya användarna i våra klient</br>
![Synkronisering](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testa logga in med något av våra användare

1.  Bläddra till [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya innehavaren.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
![Kontrollera](media/tutorial-password-hash-sync/verify1.png)</br>

Du har nu har installationen en hybridmiljö identitet som du kan använda för att testa och bekanta dig med Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg


- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)|
