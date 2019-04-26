---
title: Konfigurera Always On availability-gruppen på en Azure-dator med hjälp av PowerShell | Microsoft Docs
description: Den här självstudien används resurser som har skapats med den klassiska distributionsmodellen. Du kan använda PowerShell för att skapa en Always On-tillgänglighetsgrupp i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: c089d54544217cf72f81a2535ceede50d25b9b61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362194"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurera Always On-tillgänglighetsgrupp på en virtuell Azure-dator med PowerShell
> [!div class="op_single_selector"]
> * [Klassisk: ANVÄNDARGRÄNSSNITTET](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassisk: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Innan du börjar bör du överväga att du kan nu slutföra den här aktiviteten i Azure resource manager-modellen. Vi rekommenderar Azure resource manager-modellen för nya distributioner. Se [SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen.

Azure-datorer (VM) kan databasadministratörer för att sänka kostnaden för en högtillgänglig SQL Server-systemet. Den här självstudien visar hur du implementerar en tillgänglighetsgrupp med hjälp av SQL Server Always On slutpunkt till slutpunkt i en Azure-miljö. I slutet av självstudien består din SQL Server Always On-lösning i Azure av följande element:

* Ett virtuellt nätverk med flera undernät, inklusive en klientdel och en backend-undernät.
* En domänkontrollant med en Active Directory-domän.
* Två SQL Server-datorer som distribueras till backend-undernät och anslutna till Active Directory-domänen.
* Ett tre noder Windows failover-kluster med Nodmajoritet kvorummodellen.
* En tillgänglighetsgrupp med två repliker för synkront genomförande av en tillgänglighetsdatabas.

Det här scenariot är ett bra val för dess enkelhet på Azure, inte för kostnadseffektivitet eller andra faktorer. Exempelvis kan du minimera antalet virtuella datorer för en tillgänglighetsgrupp med två-replik att spara på drifttimmar av en instans i Azure med hjälp av domänkontrollanten som filresursvittnet kvorum i ett redundanskluster med två noder. Den här metoden minskar antal virtuella datorer genom en från konfigurationen ovan.

Den här kursen är avsedd att visa dig de steg som krävs för att konfigurera lösningen som beskrivs ovan, utan att utarbeta på information om varje steg. I stället för att tillhandahålla konfigurationssteg grafiskt användargränssnitt, kan det därför använder PowerShell-skript för att snabbt ta dig igenom varje steg. Den här kursen riktar sig till följande:

* Du har redan ett Azure-konto med VM-prenumeration.
* Du har installerat den [Azure PowerShell-cmdlets](/powershell/azure/overview).
* Du har redan en djupare förståelse för Always On-Tillgänglighetsgrupper för dina lokala lösningar. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Anslut till din Azure-prenumeration och skapa det virtuella nätverket
1. Importera Azure-modulen i ett PowerShell-fönster på den lokala datorn, ladda ned filen med publicera till din dator och ansluta din PowerShell-session till din Azure-prenumeration genom att importera de hämta publiceringsinställningarna.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Den **Get-AzurePublishSettingsFile** kommando automatiskt genererar ett certifikat med Azure och hämtar den till din dator. En webbläsare öppnas automatiskt och du uppmanas att ange autentiseringsuppgifterna för Microsoft-konto för din Azure-prenumeration. Den hämtade **.publishsettings** filen innehåller all information du behöver att hantera din Azure-prenumeration. När du har sparat den här filen till en lokal katalog, importera det med hjälp av den **Import AzurePublishSettingsFile** kommando.

   > [!NOTE]
   > .Publishsettings-filen innehåller de autentiseringsuppgifter (ej kodade) som används för att administrera dina Azure-prenumerationer och tjänster. Rekommenderade säkerhetsmetoder för den här filen är att lagra den tillfälligt utanför katalogerna källa (till exempel i mappen Libraries\Documents) och tas bort när importen är klar. En obehörig användare som får åtkomst till .publishsettings-fil kan redigera, skapa och ta bort dina Azure-tjänster.

2. Definiera ett antal variabler som du använder för att skapa en molnbaserad IT-infrastruktur.

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

   * Variabler **$storageAccountName** och **$dcServiceName** måste vara unikt eftersom de används för att identifiera din cloud storage-konto och molnet server, på Internet.
   * De namn som du anger för variabler **$affinityGroupName** och **$virtualNetworkName** konfigureras i konfigurationsdokumentet för virtuellt nätverk som du ska använda senare.
   * **$sqlImageName** uppdaterade namnet på avbildningen som innehåller SQL Server 2012 Service Pack 1 Enterprise Edition.
   * För enkelhetens skull **Contoso! 000** är det lösenord som ska användas i hela självstudien.

3. Skapa en tillhörighetsgrupp.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Skapa ett virtuellt nätverk genom att importera en konfigurationsfil.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurationsfilen innehåller följande XML-dokumentet. Enkelt uttryckt anger ett virtuellt nätverk med namnet **ContosoNET** i tillhörighetsgruppen kallas **ContosoAG**. Den har adressutrymmet **10.10.0.0/16** och har två undernät, **10.10.1.0/24** och **10.10.2.0/24**, vilket är klient undernätet och backend-undernät, respektive. Främre undernätet är där du kan placera klientprogram som Microsoft SharePoint. Backend-undernät är där du ska placera SQL Server-datorer. Om du ändrar den **$affinityGroupName** och **$virtualNetworkName** variabler tidigare, måste du också ändra motsvarande namnen nedan.

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

5. Skapa ett lagringskonto som är associerad med tillhörighetsgruppen som du skapat och ange den som det aktuella storage-kontot i din prenumeration.

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

    Kommandona via rörledningar gör du följande:

   * **Ny AzureVMConfig** skapar en virtuell Datorkonfiguration.
   * **Lägg till Azureprovisioningconfigg** ger konfigurationsparametrar för en fristående Windows-server.
   * **Lägg till AzureDataDisk** lägger till datadisken som du använder för att lagra Active Directory-data med alternativet cachelagring för angetts till None.
   * **New-AzureVM** skapar en ny molntjänst och skapar den nya virtuella Azure-datorn i den nya Molntjänsten.

7. Vänta tills den nya virtuella datorn vara helt etablerade och ladda ned fjärrskrivbordsfil till din arbetskatalog. Eftersom den nya virtuella Azure-datorn tar lång tid att etablera, den `while` slingan fortsätter att avsöka den nya virtuella datorn tills den är redo att användas.

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

Domänkontrollantservern har nu etablerats. Därefter konfigurerar du Active Directory-domän på den här domain controller-servern. Lämna fönstret PowerShell öppen på den lokala datorn. Du ska använda den igen för att skapa två SQL Server-datorer.

## <a name="configure-the-domain-controller"></a>Konfigurera en domänkontrollant
1. Anslut till domain controller-servern genom att starta den remote desktop-fil. Använd datorn administratörens användarnamn AzureAdmin och lösenord **Contoso! 000**, som du angav när du skapade den nya virtuella datorn.
2. Öppna ett PowerShell-fönster i administratörsläge.
3. Kör följande **DCPROMO. EXE** kommando för att ställa in den **corp.contoso.com** domän, med datakataloger på M-enhet.

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

4. Ansluta till Domänkontrollantservern igen genom att starta den remote desktop-fil. Den här tiden kan logga in som **CORP\Administrator**.
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

    **CORP\Install** används för att konfigurera allt relaterat till SQL Server-tjänstinstanser, failover-kluster och tillgänglighetsgruppen. **CORP\SQLSvc1** och **CORP\SQLSvc2** används som SQL Server-tjänstkontona för de två SQL Server-datorer.
7. Kör följande kommandon för att ge **CORP\Install** behörighet att skapa datorobjekt i domänen.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Det GUID som anges ovan är GUID för objekttypen dator. Den **CORP\Install** konto behov den **läsa alla egenskaper** och **skapa datorobjekt** behörighet att skapa Active direkt objekten för failover-kluster. Den **läsa alla egenskaper** behörigheter har redan tilldelats CORP\Install som standard, så du inte behöver uttryckligen bevilja. Mer information om behörigheter som krävs för att skapa failover-kluster finns i [stegvis Guide för Failover-kluster: Konfigurera konton i Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Nu när du är klar med att konfigurera Active Directory och objekt du skapar två SQL Server-datorer och koppla dem till den här domänen.

## <a name="create-the-sql-server-vms"></a>Skapa SQL Server-datorer
1. Fortsätta att använda PowerShell-fönstret som är öppen på den lokala datorn. Definiera följande ytterligare variabler:

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

    IP-adressen **10.10.0.4** vanligtvis har tilldelats den första virtuella datorn som du skapar i den **10.10.0.0/16** undernät i Azure-nätverk. Du bör kontrollera att det är adressen för din domain controller-servern genom att köra **IPCONFIG**.
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

   * **Ny AzureVMConfig** skapar en VM-konfigurationen med önskade tillgänglighetsuppsättningen. De efterföljande virtuella datorerna skapas med samma tillgänglighetsuppsättningen så att de är anslutna till samma tillgänglighetsuppsättning.
   * **Lägg till Azureprovisioningconfigg** kopplar den virtuella datorn till Active Directory-domänen som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i backend-undernät.
   * **New-AzureVM** skapar en ny molntjänst och skapar den nya virtuella Azure-datorn i den nya Molntjänsten. Den **DnsSettings** parametern anger att DNS-server för servrar i den nya Molntjänsten har IP-adressen **10.10.0.4**. Det här är IP-adressen för domain controller-servern. Den här parametern krävs för att aktivera de nya virtuella datorerna i Molntjänsten att ansluta till Active Directory-domän har. Utan den här parametern måste du manuellt ange IPv4-inställningar i den virtuella datorn ska använda domain controller-servern som den primära DNS-servern när den virtuella datorn har etablerats och sedan ansluta den virtuella datorn till Active Directory-domänen.
3. Kör följande skickas kommandon för att skapa SQL Server-datorer, med namnet **ContosoSQL1** och **ContosoSQL2**.

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

   * **Ny AzureVMConfig** använder samma tillgänglighetsuppsättningen som Domänkontrollantservern och använder SQL Server 2012 Service Pack 1 Enterprise Edition-avbildning i galleriet för virtuella datorer. I exemplet anges också operativsystemdisken till Läs-cachelagring endast (ingen skrivcache). Vi rekommenderar att du migrerar databasfilerna till en separat disk som kan bifogas till den virtuella datorn och konfigurera den utan läsning eller skrivning till cacheminnet. Dock är nästan lika bra att ta bort skrivcache på operativsystemdisken eftersom du inte ta bort läscachelagring på operativsystemdisken.
   * **Lägg till Azureprovisioningconfigg** kopplar den virtuella datorn till Active Directory-domänen som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i backend-undernät.
   * **Lägg till-AzureEndpoint** lägger till åtkomst-slutpunkterna så att klientprogram kan komma åt dessa instanser med SQL Server-tjänster på Internet. Olika portar ges till ContosoSQL1 och ContosoSQL2.
   * **New-AzureVM** skapar den nya SQL Server-dator i samma molntjänst som ContosoQuorum. Du måste placera de virtuella datorerna i samma molntjänst om du vill att de ska vara i samma tillgänglighetsuppsättning.
4. Vänta för varje virtuell dator ska etableras helt och för varje virtuell dator för att ladda ned dess fjärrskrivbordsfil till din arbetskatalog. Den `for` slingan går igenom de tre nya virtuella datorerna och kör kommandon i de översta klammerparenteser för var och en av dem.

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

    SQL Server-datorer nu är etablerad och körs, men de är installerade med SQL Server med standardalternativ.

## <a name="initialize-the-failover-cluster-vms"></a>Initiera redundanskluster virtuella datorer
I det här avsnittet måste du ändra de tre servrar som du ska använda i redundansklustret och SQL Server-installationen. Närmare bestämt:

* Alla servrar: Du måste installera den **redundanskluster** funktionen.
* Alla servrar: Du måste lägga till **CORP\Install** som datorn **administratör**.
* ContosoSQL1 och ContosoSQL2 endast: Du måste lägga till **CORP\Install** som en **sysadmin** roll i standarddatabasen.
* ContosoSQL1 och ContosoSQL2 endast: Du måste lägga till **NT AUTHORITY\System** som en inloggning med följande behörigheter:

  * Ändra någon tillgänglighetsgrupp
  * Ansluta SQL
  * Visa-Servertillstånd
* ContosoSQL1 och ContosoSQL2 endast: Den **TCP** protokollet är redan aktiverat på SQL Server-dator. Du behöver dock fortfarande att öppna brandväggen för fjärråtkomst av SQL Server.

Nu är du redo att börja. Från och med **ContosoQuorum**, Följ stegen nedan:

1. Ansluta till **ContosoQuorum** genom att starta remote desktop-filer. Använder datorn administratörens användarnamn **AzureAdmin** och lösenord **Contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutits till **corp.contoso.com**.
3. Vänta tills den SQL Server-installationen ska slutföras som kör de automatiserade utförs innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen för redundanskluster i Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Logga ut från ContosoQuorum. Du är klar med den här servern nu.

        logoff.exe

Därefter initieras **ContosoSQL1** och **ContosoSQL2**. Följ stegen nedan, som är identiska för både SQL Server-datorer.

1. Ansluta till de två SQL Server-datorer genom att starta remote desktop-filer. Använder datorn administratörens användarnamn **AzureAdmin** och lösenord **Contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrollera att datorerna har anslutits till **corp.contoso.com**.
3. Vänta tills den SQL Server-installationen ska slutföras som kör de automatiserade utförs innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörsläge.
5. Installera funktionen för redundanskluster i Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Lägg till **CORP\Install** som lokal administratör.

        net localgroup administrators "CORP\Install" /Add
7. Importera PowerShell-providern för SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Lägg till **CORP\Install** som rollen sysadmin för SQL Server-standardinstans.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Lägg till **NT AUTHORITY\System** som en inloggning med tre behörigheterna som beskrivs ovan.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Öppna brandväggen för fjärråtkomst av SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Logga ut från båda virtuella datorerna.

         logoff.exe

Slutligen är du redo att konfigurera tillgänglighetsgruppen. Du använder PowerShell-providern för SQL Server för att utföra allt arbete på **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurera tillgänglighetsgruppen
1. Ansluta till **ContosoSQL1** igen genom att starta remote desktop-filer. I stället för att logga in med hjälp av datorkontot kan logga in med **CORP\Install**.
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
7. Ladda ned **CreateAzureFailoverCluster.ps1** från [skapa redundanskluster för Always On-Tillgänglighetsgrupper i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) till lokal arbetskatalog. För att skapa ett fungerande kluster ska du använda det här skriptet. Viktig information om hur Windows-redundanskluster interagerar med Azure-nätverk finns i [hög tillgänglighet och katastrofåterställning återställning för SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Ändra till din arbetskatalog och skapa redundansklustret med det hämta skriptet.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Aktivera Always On-Tillgänglighetsgrupper för SQL Server-instanserna standard på **ContosoSQL1** och **ContosoSQL2**.

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
10. Skapa en katalog och bevilja behörigheter för SQL Server service-konton. Du använder den här katalogen för att förbereda tillgänglighetsdatabas i den sekundära repliken.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Skapa en databas på **ContosoSQL1** kallas **MyDB1**ta både en fullständig säkerhetskopia och en säkerhetskopia och återställa dem på **ContosoSQL2** med den **WITH NORECOVERY**  alternativet.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Skapa tillgängligheten grupp slutpunkter på SQL Server-datorer och ange rätt behörighet för slutpunkter.

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
13. Skapa tillgänglig replik.

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
14. Slutligen skapa tillgänglighetsgruppen och ansluta till en sekundär replik i tillgänglighetsgruppen.

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
Du har nu har implementerat SQL Server Always On genom att skapa en tillgänglighetsgrupp i Azure. För att konfigurera en lyssnare för den här tillgänglighetsgruppen, se [konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Annan information om hur du använder SQL Server i Azure finns i [SQL Server på Azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
