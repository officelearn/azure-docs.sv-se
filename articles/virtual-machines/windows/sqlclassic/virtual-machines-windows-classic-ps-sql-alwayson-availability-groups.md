---
title: Konfigurera gruppen Alltid på tillgänglighet på en virtuell Azure-dator med PowerShell | Microsoft-dokument
description: Den här självstudien använder resurser som har skapats med den klassiska distributionsmodellen. Du använder PowerShell för att skapa en alltid på tillgänglighet grupp i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978150"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurera gruppen Alltid på tillgänglighet på en virtuell Azure-dator med PowerShell
> [!div class="op_single_selector"]
> * [Klassiskt: Användargränssnitt](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassiskt: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Innan du börjar bör du tänka på att du nu kan slutföra den här uppgiften i Azure Resource Manager-modellen. Vi rekommenderar Azure Resource Manager-modell för nya distributioner. Se [SQL Server Always On availability groups on Azure virtual machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver den klassiska distributionsmodellen.

Virtuella Azure-datorer kan hjälpa databasadministratörer att sänka kostnaden för ett SQL Server-system med hög tillgänglighet. Den här självstudien visar hur du implementerar en tillgänglighetsgrupp med hjälp av SQL Server Always On end-to-end i en Azure-miljö. I slutet av självstudien består SQL Server Always On-lösningen i Azure av följande element:

* Ett virtuellt nätverk som innehåller flera undernät, inklusive ett frontend och ett backend-undernät.
* En domänkontrollant med en Active Directory-domän.
* Två virtuella SQL Server-datorer som distribueras till backend-undernätet och som är anslutna till Active Directory-domänen.
* Ett Windows-redundanskluster med trenoder med kvorummodellen Node Majority.
* En tillgänglighetsgrupp med två synkrona-commit-repliker av en tillgänglighetsdatabas.

Det här scenariot är ett bra val för sin enkelhet på Azure, inte för dess kostnadseffektivitet eller andra faktorer. Du kan till exempel minimera antalet virtuella datorer för en tillgänglighetsgrupp med två repliker för att spara på beräkningstimmar i Azure genom att använda domänkontrollanten som kvorumfilresursvittne i ett redundanskluster för två noder. Den här metoden minskar antalet virtuella datorer med en från ovanstående konfiguration.

Den här självstudien är avsedd att visa de steg som krävs för att ställa in den beskrivna lösningen ovan, utan att utveckla detaljerna för varje steg. Därför, i stället för att tillhandahålla gui konfigurationssteg, använder den PowerShell-skript för att ta dig snabbt genom varje steg. Den här självstudien förutsätter följande:

* Du har redan ett Azure-konto med prenumerationen på den virtuella datorn.
* Du har installerat [Azure PowerShell-cmdlets](/powershell/azure/overview).
* Du har redan en gedigen förståelse för Alltid på tillgänglighetsgrupper för lokala lösningar. Mer information finns i [Alltid på tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Anslut till din Azure-prenumeration och skapa det virtuella nätverket
1. I ett PowerShell-fönster på den lokala datorn importerar du Azure-modulen, hämtar publiceringsinställningsfilen till datorn och ansluter din PowerShell-session till din Azure-prenumeration genom att importera de hämtade publiceringsinställningarna.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Kommandot **Get-AzurePublishSettingsFile** genererar automatiskt ett hanteringscertifikat med Azure och hämtar det till din dator. En webbläsare öppnas automatiskt och du uppmanas att ange Microsoft-kontouppgifterna för din Azure-prenumeration. Den nedladdade **.publishsettings-filen** innehåller all information som du behöver för att hantera din Azure-prenumeration. När du har sparat filen i en lokal katalog importerar du den med kommandot **Importera-AzurePublishSettingsFile.**

   > [!NOTE]
   > .publishsettings-filen innehåller dina autentiseringsuppgifter (okodade) som används för att administrera dina Azure-prenumerationer och tjänster. Säkerhetspraxis för den här filen är att lagra den tillfälligt utanför källkatalogerna (till exempel i mappen Bibliotek\Dokument) och sedan ta bort den när importen är klar. En obehörig användare som får åtkomst till .publishsettings-filen kan redigera, skapa och ta bort dina Azure-tjänster.

2. Definiera en serie variabler som du ska använda för att skapa din moln-IT-infrastruktur.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Var uppmärksam på följande för att se till att dina kommandon lyckas senare:

   * Variabler **$storageAccountName** och **$dcServiceName** måste vara unika eftersom de används för att identifiera ditt molnlagringskonto respektive molnserver på Internet.
   * De namn som du anger för variabler **$affinityGroupName** och **$virtualNetworkName** konfigureras i det virtuella nätverkskonfigurationsdokument som du ska använda senare.
   * **$sqlImageName** anger det uppdaterade namnet på den VM-avbildning som innehåller SQL Server 2012 Service Pack 1 Enterprise Edition.
   * För enkelhetens skull är **Contoso!000** samma lösenord som används under hela självstudien.

3. Skapa en intressegrupp.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Skapa ett virtuellt nätverk genom att importera en konfigurationsfil.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurationsfilen innehåller följande XML-dokument. I korthet anger den ett virtuellt nätverk som heter **ContosoNET** i tillhörighetsgruppen **ContosoAG**. Den har adressutrymmet **10.10.0.0/16** och har två undernät, **10.10.1.0/24** och **10.10.2.0/24**, som är det främre undernätet respektive det bakre undernätet. Det främre undernätet är där du kan placera klientprogram som Microsoft SharePoint. Det bakre undernätet är där du ska placera virtuella SQL Server-datorer. Om du ändrar **$affinityGroupName** och **$virtualNetworkName** variabler tidigare måste du också ändra motsvarande namn nedan.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Skapa ett lagringskonto som är associerat med den intressegrupp som du skapade och ange det som det aktuella lagringskontot i din prenumeration.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Skapa domänkontrollantservern i den nya molntjänst- och tillgänglighetsuppsättningen.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Dessa kommandon gör följande:

   * **New-AzureVMConfig** skapar en VM-konfiguration.
   * **Add-AzureProvisioningConfig** ger konfigurationsparametrarna för en fristående Windows-server.
   * **Add-AzureDataDisk** lägger till datadisken som du ska använda för att lagra Active Directory-data, med cachelagringsalternativet inställt på Ingen.
   * **New-AzureVM** skapar en ny molntjänst och skapar den nya Azure-virtuella datorn i den nya molntjänsten.

7. Vänta tills den nya virtuella datorn har etablerats helt och hämta fjärrskrivbordsfilen till arbetskatalogen. Eftersom den nya virtuella Azure-datorn tar `while` lång tid att etablera, fortsätter loopen att avsöka den nya virtuella datorn tills den är klar för användning.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Domänkontrollantservern har nu etablerats. Därefter konfigurerar du Active Directory-domänen på den här domänkontrollantservern. Låt PowerShell-fönstret vara öppet på den lokala datorn. Du kommer att använda den igen senare för att skapa de två virtuella SQL Server-datorerna.

## <a name="configure-the-domain-controller"></a>Konfigurera domänkontrollanten
1. Anslut till domänkontrollantservern genom att starta fjärrskrivbordsfilen. Använd datoradministratörens användarnamn AzureAdmin och lösenord **Contoso!000**, som du angav när du skapade den nya virtuella datorn.
2. Öppna ett PowerShell-fönster i administratörsläge.
3. Kör följande **DCPROMO. EXE-kommando** för att ställa in **corp.contoso.com** domän, med datakataloger på enhet M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    När kommandot är klart startas den virtuella datorn om automatiskt.

4. Anslut till domänkontrollantservern igen genom att starta fjärrskrivbordsfilen. Logga in som **CORP\Administratör**den här gången .
5. Öppna ett PowerShell-fönster i administratörsläge och importera Active Directory PowerShell-modulen med följande kommando:

        Import-Module ActiveDirectory

6. Kör följande kommandon för att lägga till tre användare i domänen.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** används för att konfigurera allt som rör SQL Server-tjänstinstanserna, redundansklustret och tillgänglighetsgruppen. **CORP\SQLSvc1** och **CORP\SQLSvc2** används som SQL Server-tjänstkonton för de två virtuella SQL Server-datorerna.
7. Kör sedan följande kommandon för att ge **CORP\Installera** behörigheterna för att skapa datorobjekt i domänen.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Det GUID som anges ovan är GUID för datorobjekttypen. **Corp\Install-kontot** behöver behörigheten **Läs alla egenskaper** och Skapa **datorobjekt** för att skapa Active Direct-objekt för redundansklustret. **Behörigheten Läs alla egenskaper** ges redan till CORP\Install som standard, så du behöver inte bevilja det uttryckligen. Mer information om behörigheter som behövs för att skapa redundansklustret finns i [Stegvisa redundansguide: Konfigurera konton i Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Nu när du har konfigurerat Active Directory och användarobjekten skapar du två virtuella SQL Server-datorer och ansluter dem till den här domänen.

## <a name="create-the-sql-server-vms"></a>Skapa virtuella SQL Server-datorer
1. Fortsätt att använda PowerShell-fönstret som är öppet på den lokala datorn. Definiera följande ytterligare variabler:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP-adressen **10.10.0.4** tilldelas vanligtvis den första virtuella datorn som du skapar i undernätet **10.10.0.0/16** i ditt virtuella Azure-nätverk. Du bör kontrollera att detta är adressen till domänkontrollantservern genom att köra **IPCONFIG**.
2. Kör följande kommandon med piped för att skapa den första virtuella datorn i redundansklustret med namnet **ContosoQuorum:**

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Observera följande angående kommandot ovan:

   * **New-AzureVMConfig** skapar en VM-konfiguration med önskat tillgänglighetsuppsättningsnamn. De efterföljande virtuella datorerna skapas med samma tillgänglighetsuppsättningsnamn så att de är kopplade till samma tillgänglighetsuppsättning.
   * **Add-AzureProvisioningConfig** ansluter den virtuella datorn till Active Directory-domänen som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i det bakre undernätet.
   * **New-AzureVM** skapar en ny molntjänst och skapar den nya Azure-virtuella datorn i den nya molntjänsten. Parametern **DnsSettings** anger att DNS-servern för servrarna i den nya molntjänsten har IP-adressen **10.10.0.4**. Det här är IP-adressen för domänkontrollantservern. Den här parametern behövs för att de nya virtuella datorerna i molntjänsten ska kunna ansluta till Active Directory-domänen. Utan den här parametern måste du manuellt ställa in IPv4-inställningarna i den virtuella datorn så att domänkontrollantservern används som primär DNS-server när den virtuella datorn har etablerats och sedan ansluta den virtuella datorn till Active Directory-domänen.
3. Kör följande kommandon för piped för att skapa virtuella SQL Server-datorer med namnet **ContosoSQL1** och **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Observera följande om kommandona ovan:

   * **New-AzureVMConfig** använder samma tillgänglighetsuppsättningsnamn som domänkontrollantservern och använder SQL Server 2012 Service Pack 1 Enterprise Edition-avbildningen i galleriet för den virtuella datorn. Det ställer också operativsystemet disken till läs-cachelagring endast (ingen skriva cachelagring). Vi rekommenderar att du migrerar databasfilerna till en separat datadisk som du ansluter till den virtuella datorn och konfigurerar den utan läs- eller skrivcachelagring. Det näst bästa är dock att ta bort skrivcache på operativsystemdisken eftersom du inte kan ta bort läsa cachelagring på operativsystemdisken.
   * **Add-AzureProvisioningConfig** ansluter den virtuella datorn till Active Directory-domänen som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i det bakre undernätet.
   * **Add-AzureEndpoint** lägger till åtkomstslutpunkter så att klientprogram kan komma åt dessa SQL Server-tjänstinstanser på Internet. Olika portar ges till ContosoSQL1 och ContosoSQL2.
   * **New-AzureVM** skapar den nya virtuella datorn för SQL Server i samma molntjänst som ContosoQuorum. Du måste placera de virtuella datorerna i samma molntjänst om du vill att de ska vara i samma tillgänglighetsuppsättning.
4. Vänta tills varje virtuell dator har etablerats helt och att varje virtuell dator hämtar sin fjärrskrivbordsfil till arbetskatalogen. Loopen `for` växlar genom de tre nya virtuella datorerna och kör kommandona inom de översta klammerparenteserna för var och en av dem.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Virtuella datorer med SQL Server är nu etablerade och körs, men de installeras med SQL Server med standardalternativ.

## <a name="initialize-the-failover-cluster-vms"></a>Initiera virtuella datorer med redundanskluster
I det här avsnittet måste du ändra de tre servrar som du ska använda i redundansklustret och SQL Server-installationen. Mer specifikt:

* Alla servrar: Du måste installera funktionen **Redundanskluster.**
* Alla servrar: Du måste lägga till **CORP\Install** som **maskinadministratör**.
* Endast ContosoSQL1 och ContosoSQL2: Du måste lägga till **CORP\Install** som en **sysadmin-roll** i standarddatabasen.
* Endast ContosoSQL1 och ContosoSQL2: Du måste lägga till **NT AUTHORITY\System** som inloggning med följande behörigheter:

  * Ändra en tillgänglighetsgrupp
  * Anslut SQL
  * Visa servertillstånd
* Endast ContosoSQL1 och ContosoSQL2: **TCP-protokollet** är redan aktiverat på DEN VIRTUELLA SQL Server-datorn. Du måste dock fortfarande öppna brandväggen för fjärråtkomst av SQL Server.

Nu är du redo att börja. Börja med **ContosoQuorum,** följ stegen nedan:

1. Anslut till **ContosoQuorum** genom att starta fjärrskrivbordsfilerna. Använd maskinadministratörens användarnamn **AzureAdmin** och lösenord **Contoso!000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutits till **corp.contoso.com**.
3. Vänta tills SQL Server-installationen är klar med de automatiska initieringsuppgifterna innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen Kluster för Windows Redundanskluster.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Logga ut från ContosoQuorum. Du är klar med den här servern nu.

        logoff.exe

Därefter initiera **ContosoSQL1** och **ContosoSQL2**. Följ stegen nedan, som är identiska för båda VIRTUELLA SQL Server-datorer.

1. Anslut till de två virtuella sql server-datorerna genom att starta fjärrskrivbordsfilerna. Använd maskinadministratörens användarnamn **AzureAdmin** och lösenord **Contoso!000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutits till **corp.contoso.com**.
3. Vänta tills SQL Server-installationen är klar med de automatiska initieringsuppgifterna innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen Kluster för Windows Redundanskluster.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Importera SQL Server PowerShell-providern.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Lägg till **CORP\Install** som sysadmin-roll för standard-SQL Server-instansen.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Lägg till **NT AUTHORITY\System** som en inloggning med de tre behörigheterna som beskrivs ovan.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Öppna brandväggen för fjärråtkomst av SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Logga ut från båda virtuella datorer.

         logoff.exe

Slutligen är du redo att konfigurera tillgänglighetsgruppen. Du ska använda SQL Server PowerShell-providern för att utföra allt arbete på **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurera tillgänglighetsgruppen
1. Anslut till **ContosoSQL1** igen genom att starta fjärrskrivbordsfilerna. I stället för att logga in med hjälp av datorkontot loggar du in med **CORP\Install**.
2. Öppna ett PowerShell-fönster i administratörsläge.
3. Definiera följande variabler:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importera SQL Server PowerShell-providern.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Ändra SQL Server-tjänstkontot för ContosoSQL1 till CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Ändra SQL Server-tjänstkontot för ContosoSQL2 till CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Hämta **CreateAzureFailoverCluster.ps1** från [Skapa redundanskluster för alltid på tillgänglighetsgrupper i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) till den lokala arbetskatalogen. Du ska använda det här skriptet för att skapa ett funktionellt redundanskluster. Viktig information om hur Windows Redundanskluster interagerar med Azure-nätverket finns [i Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Ändra till arbetskatalogen och skapa redundansklustret med det hämtade skriptet.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Aktivera alltid på tillgänglighetsgrupper för standard-SQL Server-instanser på **ContosoSQL1** och **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Skapa en säkerhetskopia och bevilja behörigheter för SQL Server-tjänstkontona. Du ska använda den här katalogen för att förbereda tillgänglighetsdatabasen på den sekundära repliken.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Skapa en databas på **ContosoSQL1** kallas **MyDB1**, ta både en fullständig säkerhetskopiering och en logg backup, och återställa dem på **ContosoSQL2** med **med norecovery** alternativet.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Skapa slutpunkter för tillgänglighetsgruppen på virtuella SQL Server-datorer och ange rätt behörigheter för slutpunkterna.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Skapa tillgänglighetsrepliker.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Slutligen, skapa tillgänglighetsgruppen och gå med den sekundära repliken till tillgänglighetsgruppen.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Nästa steg
Du har nu implementerat SQL Server Always On genom att skapa en tillgänglighetsgrupp i Azure. Information om hur du konfigurerar en lyssnare för den här tillgänglighetsgruppen finns i [Konfigurera en ILB-lyssnare för alltid på tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Mer information om hur du använder SQL Server i Azure finns i [SQL Server på virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
