---
title: Självstudiekurs - Grundläggande Active Directory lokalt och Azure AD-miljö.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793903"
---
# <a name="tutorial-basic-active-directory-environment"></a>Självstudiekurs: Grundläggande Active Directory-miljö

Den här självstudien hjälper dig att skapa en grundläggande Active Directory-miljö. 

![Skapa](media/tutorial-single-forest/diagram1.png)

Du kan använda den miljö du skapar i självstudien för att testa olika aspekter av hybrididentitetsscenarier och kommer att vara en förutsättning för några av självstudierna.  Om du redan har en befintlig Active Directory-miljö kan du använda den som ersättning.  Denna information tillhandahålls för personer som jag börjar från ingenting.

Den här självstudien består av
## <a name="prerequisites"></a>Krav
Följande är förutsättningar som krävs för den här självstudien
- En dator med [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) installerat.  Vi rekommenderar att du gör detta på en dator med antingen [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) eller [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Ett [externt nätverkskort](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) så att den virtuella datorn kan kommunicera med Internet.
- En [Azure-prenumeration](https://azure.microsoft.com/free)
- En kopia av Windows Server 2016
- [Microsoft .NET-ramverket 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Den här självstudien använder PowerShell-skript så att du kan skapa självstudiemiljön så snabbt som möjligt.  Varje skript använder variabler som deklareras i början av skripten.  Du kan och bör ändra variablerna så att de speglar din miljö.
>
>Skripten som används skapar en allmän Active Directory-miljö innan du installerar Azure AD Connect-molnetableringsagenten.  De är relevanta för alla självstudierna.
>
> Kopior av de PowerShell-skript som används i den här självstudien finns på GitHub [här](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Det första du behöver göra, för att få igång vår hybrididentitetsmiljö är att skapa en virtuell dator som kommer att användas som vår lokala Active Directory-server.  Gör följande:

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
För att slutföra skapande av den virtuella datorn kan du behöva slutföra installationen av operativsystemet.

1. Hyper-V Manager, dubbelklicka på den virtuella datorn
2. Klicka på knappen Start.
3. Du får uppmaningen ”Press any key to boot from CD or DVD” (Tryck på valfri tangent för att starta från CD eller DVD). Gör så.
4. På Windows Server-startskärmen väljer du språk och klickar på **Nästa**.
5. Klicka på **Installera nu**.
6. Ange licensnyckeln och klicka på **Nästa**.
7. Markera **I accept the license terms (Jag godkänner licensvillkoren) och klicka på **Nästa**.
8. Välj **Anpassad: Installera endast Windows (avancerat)**
9. Klicka på **Nästa**
10. När installationen är klar startar du om den virtuella datorn, loggar in och kör Windows-uppdateringar för att säkerställa att den virtuella datorn är den mest aktuella.  Installera de senaste uppdateringarna.

## <a name="install-active-directory-prerequisites"></a>Installera förutsättningar för Active Directory
Nu när du har en virtuell dator upp, måste du göra några saker innan du installerar Active Directory.  Det vill säga, du måste byta namn på den virtuella datorn, ange en statisk IP-adress och DNS-information och installera verktygen för fjärrserveradministration.   Gör följande:

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
Nu när du har skapat den virtuella datorn och den har bytt namn och har en statisk IP-adress, kan du gå vidare och installera och konfigurera Active Directory Domain Services.  Gör följande:

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
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Skapa en Windows Server AD-användare
Nu när du har vår Active Directory-miljö behöver du ett testkonto.  Det här kontot kommer att skapas i vår lokala AD-miljö och sedan synkroniseras till Azure AD.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
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


## <a name="create-an-azure-ad-tenant"></a>Skapa en Azure AD-klientorganisation
Nu måste du skapa en Azure AD-klientorganisation så att du kan synkronisera våra användare till molnet.  Skapa en ny Azure AD-klientorganisation genom att göra följande.

1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **plus-ikonen (+)** och sök efter **Azure Active Directory**.
3. Välj **Azure Active Directory** i sökresultatet.
4. Välj **Skapa**.</br>
![Skapa](media/tutorial-single-forest/create1.png)</br>
5. Ange ett **namn på organisationen** tillsammans med det **ursprungliga domännamnet**. Välj sedan **Skapa**. Detta skapar din katalog.
6. När det här har slutförts klickar du på den **här** länken för att hantera katalogen.

## <a name="create-a-global-administrator-in-azure-ad"></a>Skapa en global administratör i Azure AD
Nu när du har en Azure AD-klient skapar du ett globalt administratörskonto.  Skapa kontot för den globala administratören genom att göra följande.

1.  Under **Hantera** väljer du **Användare**.</br>
![Skapa](media/tutorial-single-forest/administrator1.png)</br>
2.  Välj **Alla användare** och sedan **+ Ny användare**.
3.  Ange ett namn och användarnamn för den här användaren. Det här är din globala administratör för klientorganisationen. Du bör även ändra **katalogrollen** till **Global administratör.** Du kan även visa det tillfälliga lösenordet. När du är klar väljer du **Skapa**.</br>
![Skapa](media/tutorial-single-forest/administrator2.png)</br>
4. När det här har slutförts öppnar du en ny webbläsare och loggar in på myapps.microsoft.com med hjälp av det nya globala administratörskontot och det tillfälliga lösenordet.
5. Ändra lösenordet för den globala administratören till något som du kommer ihåg.

## <a name="optional--additional-server-and-forest"></a>Valfritt: Ytterligare server och skog
Följande är ett valfritt avsnitt som innehåller steg för att skapa ytterligare en server och eller skog.  Detta kan användas i några av de mer avancerade självstudierna, till exempel [Pilot for Azure AD Connect till molnetablering](tutorial-pilot-aadc-aadccp.md).

Om du bara behöver ytterligare en server kan du sluta efter steget - **Skapa den virtuella datorn** och anslut servern till den befintliga domänen som skapades ovan.  

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
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

### <a name="complete-the-operating-system-deployment"></a>Slutföra distributionen av operativsystemet
För att slutföra skapande av den virtuella datorn kan du behöva slutföra installationen av operativsystemet.

1. Hyper-V Manager, dubbelklicka på den virtuella datorn
2. Klicka på knappen Start.
3. Du får uppmaningen ”Press any key to boot from CD or DVD” (Tryck på valfri tangent för att starta från CD eller DVD). Gör så.
4. På Windows Server-startskärmen väljer du språk och klickar på **Nästa**.
5. Klicka på **Installera nu**.
6. Ange licensnyckeln och klicka på **Nästa**.
7. Markera **I accept the license terms (Jag godkänner licensvillkoren) och klicka på **Nästa**.
8. Välj **Anpassad: Installera endast Windows (avancerat)**
9. Klicka på **Nästa**
10. När installationen är klar startar du om den virtuella datorn, loggar in och kör Windows-uppdateringar för att säkerställa att den virtuella datorn är den mest aktuella.  Installera de senaste uppdateringarna.

### <a name="install-active-directory-prerequisites"></a>Installera förutsättningar för Active Directory
Nu när du har en virtuell dator upp, måste du göra några saker innan du installerar Active Directory.  Det vill säga, du måste byta namn på den virtuella datorn, ange en statisk IP-adress och DNS-information och installera verktygen för fjärrserveradministration.   Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
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
### <a name="create-a-windows-server-ad-environment"></a>Skapa en Windows Server AD-miljö
Nu när du har skapat den virtuella datorn och den har bytt namn och har en statisk IP-adress, kan du gå vidare och installera och konfigurera Active Directory Domain Services.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Skapa en Windows Server AD-användare
Nu när du har vår Active Directory-miljö behöver du ett testkonto.  Det här kontot kommer att skapas i vår lokala AD-miljö och sedan synkroniseras till Azure AD.  Gör följande:

1. Öppna PowerShell ISE som administratör.
2. Kör följande skript.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Slutsats
Nu har du en miljö som kan användas för befintliga självstudier och för att testa ytterligare funktioner molnetablering ger.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
