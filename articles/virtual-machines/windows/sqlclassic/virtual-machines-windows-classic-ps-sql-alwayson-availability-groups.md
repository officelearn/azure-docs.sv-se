---
title: "Konfigurera Always On-tillgänglighetsgrupp på en virtuell dator i Azure med hjälp av PowerShell | Microsoft Docs"
description: "Den här kursen använder resurser som har skapats med den klassiska distributionsmodellen. Du kan använda PowerShell för att skapa en tillgänglighetsgrupp alltid på i Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b99cf767fb931d3f7fe14fcbe7990126244613ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurera Always On-tillgänglighetsgrupp på en virtuell Azure-dator med PowerShell
> [!div class="op_single_selector"]
> * [Klassiska: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassiska: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Innan du börjar bör du överväga att du kan nu slutföra den här aktiviteten i Azure resource manager-modellen. Vi rekommenderar Azure resource manager-modellen för nya distributioner. Se [SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen.

Virtuella Azure-datorer (VM) kan hjälpa databasadministratörer att för att sänka kostnaderna för ett SQL Server-system med hög tillgänglighet. Den här kursen visar hur du implementerar en tillgänglighetsgrupp med hjälp av SQL Server alltid aktiverad slutpunkt till slutpunkt i en Azure-miljö. I slutet av kursen består lösningen SQL Server alltid aktiverad i Azure av följande element:

* Ett virtuellt nätverk som innehåller flera undernät, inklusive en frontend- och ett backend-undernät.
* En domänkontrollant med en Active Directory-domän.
* Två SQL Server-datorer som har distribuerats till backend-undernät och anslutna till Active Directory-domän.
* Ett tre Windows redundanskluster med Nodmajoritet kvorummodellen.
* En tillgänglighetsgrupp med två replikerna med synkront genomförande av en tillgänglighetsdatabas.

Det här scenariot är ett bra alternativ för dess enkelhet på Azure, inte för kostnadseffektivitet eller andra faktorer. Du kan till exempel minimera antalet virtuella datorer för en tillgänglighetsgrupp med två-replik att spara på beräkningstimmar i Azure med hjälp av domänkontrollanten som filresursvittne för kvorum i ett kluster med två noder. Den här metoden minskar antalet VM med en i konfigurationen ovan.

Den här kursen är avsedd att visa de steg som krävs för att skapa lösningen som beskrivs ovan, utan utarbeta på information om varje steg. I stället för att tillhandahålla konfigurationssteg GUI används därför PowerShell-skript för att ta dig snabbt igenom varje steg. Den här kursen förutsätter följande:

* Du har redan ett Azure-konto med den virtuella dator prenumerationen.
* Du har installerat den [Azure PowerShell-cmdlets](/powershell/azure/overview).
* Du har redan en god förståelse av Always On-Tillgänglighetsgrupper för lokala lösningar. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Anslut till din Azure-prenumeration och skapa det virtuella nätverket
1. Importera modulen för Azure i PowerShell-fönstret på den lokala datorn, ladda ned filen publishing till din dator och ansluta din PowerShell-session till din Azure-prenumeration genom att importera de hämta publiceringsinställningarna.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Den **Get-AzurePublishSettingsFile** kommandot automatiskt genererar ett certifikat med Azure och hämtas till din dator. En webbläsare öppnas automatiskt och du uppmanas att ange autentiseringsuppgifterna för Microsoft-konto för din Azure-prenumeration. Den hämtade **.publishsettings** filen innehåller all information du behöver hantera Azure-prenumerationen. När du har sparat filen till en lokal katalog, importerar du den med hjälp av den **importera AzurePublishSettingsFile** kommando.

   > [!NOTE]
   > .Publishsettings-filen innehåller dina autentiseringsuppgifter (ej kodade) som används för att administrera dina Azure-prenumerationer och tjänster. Rekommenderade säkerhetsmetoder för den här filen är att lagra den tillfälligt utanför katalogerna källa (till exempel i mappen Libraries\Documents) och tas bort när importen är klar. En obehörig användare som får åtkomst till .publishsettings-fil kan du redigera, skapa och ta bort Azure-tjänster.

2. Definiera ett antal variabler som du använder för att skapa ditt moln IT-infrastruktur.

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

    Ta hänsyn till följande för att se till att dina kommandon lyckas senare:

   * Variabler **$storageAccountName** och **$dcServiceName** måste vara unikt eftersom de används att identifiera moln konto och molnet lagringsservern, respektive på Internet.
   * De namn som du anger för variabler **$affinityGroupName** och **$virtualNetworkName** konfigureras i virtuella nätverk configuration dokumentet som du vill använda senare.
   * **$sqlImageName** uppdaterade namnet på VM-avbildning som innehåller SQL Server 2012 Service Pack 1 Enterprise Edition.
   * För enkelhetens skull **Contoso! 000** är samma lösenord som ska användas i hela kursen.

3. Skapa en tillhörighetsgrupp.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Skapa ett virtuellt nätverk genom att importera en konfigurationsfil.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurationsfilen innehåller följande XML-dokumentet. I korthet det anger ett virtuellt nätverk kallas **ContosoNET** i tillhörighetsgruppen kallas **ContosoAG**. Den har adressutrymmet **10.10.0.0/16** och har två undernät **10.10.1.0/24** och **10.10.2.0/24**, vilket är främre undernätet och bakre undernät respektive. Främre undernätet är där du kan placera klientprogram, till exempel Microsoft SharePoint. Den bakre undernätet är där du ska placera SQL Server-datorer. Om du ändrar den **$affinityGroupName** och **$virtualNetworkName** variabler tidigare, måste du också ändra motsvarande namn nedan.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

5. Skapa ett lagringskonto som är kopplad till den tillhörighetsgrupp som du skapat och ange den som det aktuella storage-kontot i din prenumeration.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Skapa Domänkontrollantservern i den nya cloud service och tillgänglighet uppsättningen.

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

    Kommandona via rörledningar göra följande:

   * **Nya AzureVMConfig** skapar en VM-konfiguration.
   * **Lägg till AzureProvisioningConfig** ger konfigurationsparametrar för en fristående Windows server.
   * **Lägg till AzureDataDisk** lägger till datadisk som du vill använda för att lagra Active Directory-data med alternativet cachelagring inställd på None.
   * **Nya AzureVM** skapar en ny molntjänst och skapar den nya Azure VM i ny molntjänst.

7. Vänta tills den nya VM att helt etablerad och hämta filen för remote desktop till arbetskatalogen. Eftersom den nya Azure VM tar lång tid att etablera, den `while` slingan fortsätter att avsöka den nya virtuella datorn tills den är redo för användning.

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

Domänkontrollantservern nu etablerats. Du måste konfigurera Active Directory-domänen på den här Domänkontrollantservern. Lämna fönstret PowerShell öppen på den lokala datorn. Du använder det igen för att skapa två SQL Server-datorer.

## <a name="configure-the-domain-controller"></a>Konfigurera en domänkontrollant
1. Ansluta till Domänkontrollantservern genom att starta remote desktop fil. Använd datorn administratörens användarnamn AzureAdmin lösenord **Contoso! 000**, som du angav när du skapade den nya virtuella datorn.
2. Öppna ett PowerShell-fönster i administratörsläge.
3. Kör följande **DCPROMO. EXE** kommando för att ställa in den **corp.contoso.com** domän, med datakataloger på enhet M.

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

    När kommandot har slutförts startas den virtuella datorn om automatiskt.

4. Ansluta till Domänkontrollantservern igen genom att starta remote desktop fil. Den här tiden kan logga in som **CORP\Administrator**.
5. Öppna ett PowerShell-fönster i administratörsläge och importera Active Directory PowerShell-modulen med hjälp av följande kommando:

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

    **CORP\Install** används för att konfigurera allt relaterade till SQL Server-tjänstinstanser, failover-kluster och tillgänglighetsgruppen. **CORP\SQLSvc1** och **CORP\SQLSvc2** används som SQL Server-tjänstkontona för två SQL Server-datorer.
7. Kör följande kommandon för att ge **CORP\Install** behörigheter att skapa datorobjekt i domänen.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Det GUID som anges ovan är GUID för typ av objekt. Den **CORP\Install** konto måste den **läsa alla egenskaper** och **skapa datorobjekt** behörighet att skapa Active direkt objekt för failover-kluster. Den **läsa alla egenskaper** behörigheter har redan tilldelats CORP\Install som standard, så du inte behöver bevilja explicit. Mer information om behörigheter som krävs för att skapa kluster för växling vid fel finns [stegvis Guide för Failover-kluster: Konfigurera konton i Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Nu när du är klar med att konfigurera Active Directory och användarobjekt kan du skapa två virtuella SQL Server-datorer och Anslut dem till den här domänen.

## <a name="create-the-sql-server-vms"></a>Skapa SQL Server-datorer
1. Fortsätta att använda PowerShell-fönster som är öppna på den lokala datorn. Definiera följande ytterligare variabler:

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

    IP-adressen **10.10.0.4** vanligtvis har tilldelats den första virtuella dator som du skapar i den **10.10.0.0/16** undernätet för din virtuella Azure-nätverket. Du bör kontrollera att det här är adressen till din Domänkontrollantservern genom att köra **IPCONFIG**.
2. Kör följande skickas kommandon för att skapa den första virtuella datorn i failover-kluster med namnet **ContosoQuorum**:

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

    Observera följande om ovanstående kommando:

   * **Nya AzureVMConfig** skapar en VM-konfiguration med önskad tillgänglighetsuppsättningen. Följande virtuella datorer kommer att skapas med samma tillgänglighetsuppsättningen så att de är anslutna till samma tillgänglighetsuppsättning.
   * **Lägg till AzureProvisioningConfig** ansluter till den virtuella datorn i Active Directory-domänen som du skapade.
   * **Ange AzureSubnet** placerar den virtuella datorn i den bakre undernät.
   * **Nya AzureVM** skapar en ny molntjänst och skapar den nya Azure VM i ny molntjänst. Den **DnsSettings** parametern anger att DNS-servern för servrar i en ny molntjänst har IP-adressen **10.10.0.4**. Det här är IP-adressen för Domänkontrollantservern. Den här parametern krävs för att aktivera de nya virtuella datorerna i Molntjänsten att ansluta till Active Directory-domänen har. Utan den här parametern måste du manuellt ange IPv4-inställningar i den virtuella datorn att använda Domänkontrollantservern som den primära DNS-servern efter den virtuella datorn har etablerats och sedan ansluta den virtuella datorn till Active Directory-domänen.
3. Kör följande skickas kommandon för att skapa SQL Server-VMs, med namnet **ContosoSQL1** och **ContosoSQL2**.

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

   * **Nya AzureVMConfig** använder samma tillgänglighetsuppsättningen som Domänkontrollantservern och använder SQL Server 2012 Service Pack 1 Enterprise Edition bilden i galleriet för virtuella datorer. Anger också operativsystemets disk till Läs-cacheservrar (ingen skrivcache). Vi rekommenderar att du migrerar databasfilerna till en separat disk som du ansluter till den virtuella datorn och konfigurera utan läsning eller skrivning till cacheminnet. Dock är det bästa alternativet att ta bort skrivcache på operativsystemets disk eftersom du inte ta bort skrivskyddade cachelagring på operativsystemets disk.
   * **Lägg till AzureProvisioningConfig** ansluter till den virtuella datorn i Active Directory-domänen som du skapade.
   * **Ange AzureSubnet** placerar den virtuella datorn i den bakre undernät.
   * **Lägg till AzureEndpoint** lägger till slutpunkter för åtkomst så att klientprogram kan komma åt dessa tjänster SQL Server-instanser på Internet. Olika portar ges till ContosoSQL1 och ContosoSQL2.
   * **Nya AzureVM** skapar den nya SQL Server-VM i samma molntjänst som ContosoQuorum. Du måste placera de virtuella datorerna i samma molntjänst om du vill att de ska vara i samma tillgänglighetsuppsättning.
4. Vänta varje virtuell dator för att helt etablerad och varje virtuell dator för att hämta dess remote desktop-fil till arbetskatalogen. Den `for` loop går igenom de tre nya virtuella datorerna och kör kommandon i de översta klammerparenteser för dem.

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

    SQL Server-datorer har nu etablerats och körs, men de är installerade med SQL Server med standardalternativ.

## <a name="initialize-the-failover-cluster-vms"></a>Initiera redundanskluster virtuella datorer
I det här avsnittet måste du ändra de tre servrarna som du vill använda i klustret och SQL Server-installationen. Närmare bestämt:

* Alla servrar: du måste installera den **redundanskluster** funktion.
* Alla servrar: du måste lägga till **CORP\Install** som datorn **administratör**.
* ContosoSQL1 och ContosoSQL2 endast: du måste lägga till **CORP\Install** som en **sysadmin** roll i standarddatabasen.
* ContosoSQL1 och ContosoSQL2 endast: du måste lägga till **NT AUTHORITY\System** som en inloggning med följande behörigheter:

  * ALTER någon tillgänglighetsgrupp
  * Ansluta SQL
  * Visa-Servertillstånd
* ContosoSQL1 och ContosoSQL2 endast: den **TCP** protokollet är redan aktiverat på SQL Server-VM. Du behöver dock fortfarande öppna brandväggen för fjärråtkomst av SQL Server.

Nu är du redo att börja. Från och med **ContosoQuorum**, Följ stegen nedan:

1. Ansluta till **ContosoQuorum** genom att starta remote desktop-filer. Använd datorn administratörsanvändarnamn **AzureAdmin** och lösenord **Contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutit till **corp.contoso.com**.
3. Vänta på att SQL Server-installationen ska slutföras kör initieringen av automatiserade uppgifter innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen redundanskluster i Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Logga ut från ContosoQuorum. Du är klar med den här servern nu.

        logoff.exe

Därefter initiera **ContosoSQL1** och **ContosoSQL2**. Följ stegen nedan, som är identiska för både SQL Server-datorer.

1. Ansluta till de två SQL Server-datorer genom att starta remote desktop-filer. Använd datorn administratörsanvändarnamn **AzureAdmin** och lösenord **Contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutit till **corp.contoso.com**.
3. Vänta på att SQL Server-installationen ska slutföras kör initieringen av automatiserade uppgifter innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen redundanskluster i Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Importera PowerShell-providern för SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Lägg till **CORP\Install** som rollen sysadmin för standardinstansen för SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Lägg till **NT AUTHORITY\System** som loggar in med de tre behörigheter som beskrivs ovan.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Öppna i brandväggen för fjärråtkomst av SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Logga ut från både virtuella datorer.

         logoff.exe

Slutligen är du redo att konfigurera tillgänglighetsgruppen. Du använder PowerShell-providern för SQL Server för att utföra allt arbete på **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurera tillgänglighetsgruppen
1. Ansluta till **ContosoSQL1** igen genom att starta remote desktop-filer. I stället för att logga in med hjälp av datorkontot kan logga in med hjälp av **CORP\Install**.
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
4. Importera PowerShell-providern för SQL Server.

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
7. Hämta **CreateAzureFailoverCluster.ps1** från [Skapa kluster för växling vid fel för Always On-Tillgänglighetsgrupper i Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) till lokala arbetskatalogen. För att skapa ett fungerande kluster ska du använda det här skriptet. Viktig information om hur Windows-redundanskluster samverkar med Azure-nätverk finns i [hög tillgänglighet och katastrofåterställning återställning för SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Ändra till arbetskatalogen och skapa klustret med det hämta skriptet.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Aktivera Always On-Tillgänglighetsgrupper för SQL Server-standardinstanser på **ContosoSQL1** och **ContosoSQL2**.

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
10. Skapa en katalog och bevilja behörighet för SQL Server-tjänstkonton. Du använder den här katalogen för att förbereda tillgänglighetsdatabasen på den sekundära repliken.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Skapa en databas på **ContosoSQL1** kallas **MyDB1**ta både en fullständig säkerhetskopia och en säkerhetskopia och återställa dem på **ContosoSQL2** med den **WITH NORECOVERY** alternativet.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Skapa tillgängligheten grupp slutpunkterna på SQL Server-datorer och ange åtkomstbehörighet för slutpunkter.

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
14. Slutligen skapar tillgänglighetsgruppen och ansluter till sekundär replik i tillgänglighetsgruppen.

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
Du har nu har implementerat SQL Server alltid aktiverad genom att skapa en tillgänglighetsgrupp i Azure. Om du vill konfigurera en lyssnare för den här tillgänglighetsgruppen finns [konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Annan information om hur du använder SQL Server i Azure, se [SQL Server på Azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
