---
title: Konfigurera tillgänglighets gruppen Always on på en virtuell Azure-dator med hjälp av PowerShell | Microsoft Docs
description: I den här självstudien används resurser som har skapats med den klassiska distributions modellen. Du använder PowerShell för att skapa en tillgänglighets grupp som alltid är tillgänglig i Azure.
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
ms.openlocfilehash: 380abff03ad4ee4befb645f7f992ab037d213b33
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086712"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurera tillgänglighets gruppen Always on på en virtuell Azure-dator med PowerShell
> [!div class="op_single_selector"]
> * [Klassisk: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassisk: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Innan du börjar bör du tänka på att du nu kan slutföra den här uppgiften i Azure Resource Manager-modellen. Vi rekommenderar Azure Resource Manager-modellen för nya distributioner. Se [SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).

> [!IMPORTANT]
> Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver den klassiska distributionsmodellen.

Azure Virtual Machines (VM) kan hjälpa databas administratörer att sänka kostnaderna för ett SQL Server system med hög tillgänglighet. Den här självstudien visar hur du implementerar en tillgänglighets grupp genom att använda SQL Server Always On-to-end i en Azure-miljö. I slutet av självstudien kommer SQL Server Always on-lösning i Azure att bestå av följande element:

* Ett virtuellt nätverk som innehåller flera undernät, inklusive en klient del och ett Server dels undernät.
* En domänkontrollant med en Active Directory domän.
* Två SQL Server virtuella datorer som distribueras till backend-undernätet och ansluts till Active Directorys domänen.
* Ett Windows-redundanskluster med tre noder och kvorumresursens Nodmajoritet.
* En tillgänglighets grupp med två repliker av synkront genomförande i en tillgänglighets databas.

Det här scenariot är ett bra alternativ för dess enkelhet på Azure, inte för kostnads effektivitet eller andra faktorer. Du kan till exempel minimera antalet virtuella datorer för en tillgänglighets grupp med två repliker för att spara på beräknings timmar i Azure med hjälp av domänkontrollanten som ett fil resurs vittne för kvorum i ett kluster med två noder. Den här metoden minskar antalet virtuella datorer med en från ovanstående konfiguration.

Den här självstudien är avsedd att visa de steg som krävs för att ställa in den beskrivna lösningen ovan, utan att utveckla information om varje steg. I stället för att tillhandahålla konfigurations stegen för GUI använder den därför PowerShell-skript för att snabbt ta dig igenom varje steg. Den här självstudien förutsätter följande:

* Du har redan ett Azure-konto med den virtuella dator prenumerationen.
* Du har installerat [Azure PowerShell-cmdletarna](/powershell/azure/overview).
* Du har redan en solid förståelse för Always on-tillgänglighetsgrupper för lokala lösningar. Mer information finns i [Always on Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Anslut till din Azure-prenumeration och skapa det virtuella nätverket
1. I ett PowerShell-fönster på den lokala datorn importerar du Azure-modulen, laddar ned publicerings inställnings filen till datorn och ansluter PowerShell-sessionen till din Azure-prenumeration genom att importera de hämtade publicerings inställningarna.

    ```powershell
    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
    Get-AzurePublishSettingsFile
    Import-AzurePublishSettingsFile <publishsettingsfilepath>
    ```

    Kommandot **Get-AzurePublishSettingsFile** genererar automatiskt ett hanterings certifikat med Azure och laddar ned det till datorn. En webbläsare öppnas automatiskt och du uppmanas att ange Microsoft-konto autentiseringsuppgifter för din Azure-prenumeration. Den hämtade **. publishsettings** -filen innehåller all information som du behöver för att hantera din Azure-prenumeration. När du har sparat filen i en lokal katalog importerar du den med hjälp av kommandot **import-AzurePublishSettingsFile** .

   > [!NOTE]
   > Filen. publishsettings innehåller dina autentiseringsuppgifter (avkodade) som används för att administrera dina Azure-prenumerationer och-tjänster. Den rekommenderade säkerhets metoden för den här filen är att lagra den tillfälligt utanför dina käll kataloger (till exempel i mappen Libraries\Documents) och sedan ta bort den när importen är färdig. En obehörig användare som får åtkomst till. publishsettings-filen kan redigera, skapa och ta bort dina Azure-tjänster.

2. Definiera en serie med variabler som du kommer att använda för att skapa en infrastruktur för molnet.

    ```powershell
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
    ```

    Observera följande för att se till att dina kommandon kommer att lyckas senare:

   * Variabler **$storageAccountName** och **$dcServiceName** måste vara unika eftersom de används för att identifiera ditt moln lagrings konto och din moln server på Internet.
   * De namn som du anger för variabler **$affinityGroupName** och **$virtualNetworkName** konfigureras i det virtuella nätverks konfigurations dokument som du kommer att använda senare.
   * **$sqlImageName** anger det uppdaterade namnet på den virtuella dator avbildningen som innehåller SQL Server 2012 Service Pack 1 Enterprise Edition.
   * För enkelhetens skull är **contoso! 000** samma lösen ord som används i hela självstudien.

3. Skapa en tillhörighets grupp.

    ```powershell
    New-AzureAffinityGroup `
        -Name $affinityGroupName `
        -Location $location `
        -Description $affinityGroupDescription `
        -Label $affinityGroupLabel
    ```

4. Skapa ett virtuellt nätverk genom att importera en konfigurations fil.

    ```powershell
    Set-AzureVNetConfig `
        -ConfigurationPath $networkConfigPath
    ```

    Konfigurations filen innehåller följande XML-dokument. I korthet anger den ett virtuellt nätverk med namnet **ContosoNET** i tillhörighets gruppen med namnet **ContosoAG**. Det har adress utrymmet **10.10.0.0/16** och har två undernät, **10.10.1.0/24** och **10.10.2.0/24**, som är frontend-undernät och backend-undernät. Frontend-undernätet är där du kan placera klient program som Microsoft SharePoint. Under nätet är där du placerar SQL Server virtuella datorer. Om du ändrar **$affinityGroupName** och **$virtualNetworkName** variabler tidigare måste du också ändra motsvarande namn nedan.

    ```xml
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
    ```xml

5. Create a storage account that's associated with the affinity group that you created, and set it as the current storage account in your subscription.

    ```powershell
    New-AzureStorageAccount `
        -StorageAccountName $storageAccountName `
        -Label $storageAccountLabel `
        -AffinityGroup $affinityGroupName
    Set-AzureSubscription `
        -SubscriptionName (Get-AzureSubscription).SubscriptionName `
        -CurrentStorageAccount $storageAccountName
    ```

6. Skapa domänkontrollantens server i den nya moln tjänsten och tillgänglighets uppsättningen.

    ```powershell
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
    ```

    Dessa skickas-kommandon gör följande:

   * **New-AzureVMConfig** skapar en VM-konfiguration.
   * **Add-azureprovisioningconfigg** ger konfigurations parametrar för en fristående Windows Server.
   * **Add-AzureDataDisk** lägger till den datadisk som du ska använda för att lagra Active Directory data, med alternativet cachelagring inställd på ingen.
   * **New-AzureVM** skapar en ny moln tjänst och skapar den nya virtuella Azure-datorn i den nya moln tjänsten.

7. Vänta tills den nya virtuella datorn är helt etablerad och ladda ned fjärr skrivbords filen till din arbets katalog. Eftersom den nya virtuella Azure-datorn tar lång tid att etablera, `while` fortsätter loopen att avsöka den nya virtuella datorn tills den är redo att användas.

    ```powershell
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
    ```

Domänkontrollantens Server har nu tillhandahållits. Sedan konfigurerar du Active Directorys domänen på den här domänkontrollanten. Lämna PowerShell-fönstret öppet på den lokala datorn. Du kommer att använda den igen senare för att skapa de två SQL Server virtuella datorerna.

## <a name="configure-the-domain-controller"></a>Konfigurera domänkontrollanten
1. Anslut till domänkontrollanten genom att starta fjärr skrivbords filen. Använd dator administratörens användar namn AzureAdmin och lösen ord **contoso! 000**, som du angav när du skapade den nya virtuella datorn.
2. Öppna ett PowerShell-fönster i administratörs läge.
3. Kör följande **DCPROMO.EXE** -kommando för att konfigurera **Corp.contoso.com** -domänen med data katalogerna på enhet M.

    ```powershell
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
    ```

    När kommandot har slutförts startas den virtuella datorn om automatiskt.

4. Anslut till domänkontrollanten igen genom att starta fjärr skrivbords filen. Nu loggar du in som **CORP\Administrator.**.
5. Öppna ett PowerShell-fönster i administratörs läge och importera Active Directory PowerShell-modulen med hjälp av följande kommando:

    ```powershell
    Import-Module ActiveDirectory
    ```

6. Kör följande kommandon för att lägga till tre användare i domänen.

    ```powershell
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
    ```

    **CORP\Install** används för att konfigurera allt som är relaterat till SQL Server tjänst instanser, redundansklustret och tillgänglighets gruppen. **CORP\SQLSvc1** och **CORP\SQLSvc2** används som SQL Server tjänst konton för de två SQL Server virtuella datorerna.
7. Kör sedan följande kommandon för att ge **CORP\Install** behörighet att skapa dator objekt i domänen.

    ```powershell
    Cd ad:
    $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
    $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
    $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
    $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
    $acl = Get-Acl $corp
    $acl.AddAccessRule($ace1)
    Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl
    ```

    Det GUID som anges ovan är GUID för dator objekt typen. **CORP\Install** -kontot måste ha behörighet att **läsa alla egenskaper** och **skapa dator objekt** för att skapa aktiva direkta objekt för redundansklustret. Behörigheten **läsa alla egenskaper** ges redan CORP\Install som standard, så du behöver inte bevilja den explicit. Mer information om behörigheter som krävs för att skapa redundansklustret finns i [steg-för-steg-guide för redundanskluster: Konfigurera konton i Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Nu när du har konfigurerat Active Directory och användar objekt, skapar du två SQL Server virtuella datorer och ansluter dem till den här domänen.

## <a name="create-the-sql-server-vms"></a>Skapa SQL Server virtuella datorer
1. Fortsätt att använda PowerShell-fönstret som är öppet på den lokala datorn. Definiera följande ytterligare variabler:

    ```powershell
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
    ```

    IP- **10.10.0.4** tilldelas vanligt vis till den första virtuella datorn som du skapar i under nätet **10.10.0.0/16** för ditt virtuella Azure-nätverk. Kontrol lera att det här är adressen till domänkontrollanten genom att köra **ipconfig**.
2. Kör följande skickas-kommandon för att skapa den första virtuella datorn i redundansklustret med namnet **ContosoQuorum**:

    ```powershell
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
    ```

    Tänk på följande om kommandot ovan:

   * **New-AzureVMConfig** skapar en VM-konfiguration med önskad tillgänglighets uppsättnings namn. De efterföljande virtuella datorerna skapas med samma namn på tillgänglighets uppsättningen så att de är anslutna till samma tillgänglighets uppsättning.
   * **Add-azureprovisioningconfigg** ansluter den virtuella datorn till den Active Directory domän som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i backend-undernätet.
   * **New-AzureVM** skapar en ny moln tjänst och skapar den nya virtuella Azure-datorn i den nya moln tjänsten. Parametern **DnsSettings** anger att DNS-servern för servrarna i den nya moln tjänsten har IP- **10.10.0.4**. Detta är IP-adressen för domänkontrollantens Server. Den här parametern krävs för att de nya virtuella datorerna i moln tjänsten ska kunna ansluta till den Active Directory domänen. Utan den här parametern måste du manuellt ange IPv4-inställningarna i den virtuella datorn för att kunna använda domänkontrollanten som primär DNS-server när den virtuella datorn har tillhandahållits, och sedan ansluta den virtuella datorn till den Active Directory domänen.
3. Kör följande skickas-kommandon för att skapa de virtuella datorerna SQL Server, med namnet **ContosoSQL1** och **ContosoSQL2**.

    ```powershell
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
    ```

    Observera följande om kommandona ovan:

   * **New-AzureVMConfig** använder samma tillgänglighets uppsättnings namn som domänkontrollantens Server och använder avbildningen SQL Server 2012 Service Pack 1 Enterprise Edition i galleriet för virtuella datorer. Den anger också operativ system disken för enbart läsning i cacheminnet (ingen skrivcache). Vi rekommenderar att du migrerar databasfilerna till en separat datadisk som du ansluter till den virtuella datorn och konfigurerar den utan Read-eller Write-cachelagring. Nästa steg är att ta bort skrivcache på operativ system disken eftersom du inte kan ta bort cachelagring av läsning på operativ system disken.
   * **Add-azureprovisioningconfigg** ansluter den virtuella datorn till den Active Directory domän som du skapade.
   * **Set-AzureSubnet** placerar den virtuella datorn i backend-undernätet.
   * **Add-AzureEndpoint** lägger till åtkomst slut punkter så att klient programmen kan komma åt dessa SQL Server Services-instanser på Internet. Olika portar ges till ContosoSQL1 och ContosoSQL2.
   * **New-AzureVM** skapar den nya SQL Server VM i samma moln tjänst som ContosoQuorum. Du måste placera de virtuella datorerna i samma moln tjänst om du vill att de ska vara i samma tillgänglighets uppsättning.
4. Vänta tills varje virtuell dator är helt etablerad och för varje virtuell dator kan du ladda ned dess fjärr skrivbords fil till din arbets katalog. `for`Loopen går igenom de tre nya virtuella datorerna och kör kommandona inuti klammerparenteserna på den översta nivån för var och en av dem.

    ```powershell
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
    ```

    De virtuella datorerna i SQL Server är nu etablerade och körs, men de installeras med SQL Server med standard alternativ.

## <a name="initialize-the-failover-cluster-vms"></a>Initiera de virtuella datorerna i redundansklustret
I det här avsnittet måste du ändra de tre servrar som du ska använda i redundansklustret och den SQL Server installationen. Specifikt:

* Alla servrar: du måste installera funktionen **kluster för växling vid fel** .
* Alla servrar: du måste lägga till **CORP\Install** som dator **administratör**.
* Endast ContosoSQL1 och ContosoSQL2: du måste lägga till **CORP\Install** som en **sysadmin** -roll i standard databasen.
* Endast ContosoSQL1 och ContosoSQL2: du måste lägga till **NT instans\system** som inloggning med följande behörigheter:

  * Ändra valfri tillgänglighets grupp
  * Anslut SQL
  * Visa Server tillstånd
* Endast ContosoSQL1 och ContosoSQL2: **TCP** -protokollet har redan Aktiver ats på SQL Server VM. Du måste dock fortfarande öppna brand väggen för fjärråtkomst av SQL Server.

Nu är du redo att starta. Från och med **ContosoQuorum**följer du stegen nedan:

1. Anslut till **ContosoQuorum** genom att starta fjärr skrivbords filerna. Använd dator administratörens användar namn **AzureAdmin** och lösen ord **contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrol lera att datorerna har anslutits till **Corp.contoso.com**.
3. Vänta tills den SQL Server installationen Slutför körningen av automatiserade initierings aktiviteter innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörs läge.
5. Installera funktionen för redundanskluster i Windows.

    ```powershell
    Import-Module ServerManager
    Add-WindowsFeature Failover-Clustering
    ```

6. Lägg till **CORP\Install** som lokal administratör.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    ```

7. Logga ut från ContosoQuorum. Nu är du klar med den här servern.

    ```powershell
    logoff.exe
    ```

Initiera sedan **ContosoSQL1** och **ContosoSQL2**. Följ stegen nedan, som är identiska för både SQL Server virtuella datorer.

1. Anslut till de två SQL Server virtuella datorerna genom att starta fjärr skrivbords filerna. Använd dator administratörens användar namn **AzureAdmin** och lösen ord **contoso! 000**, som du angav när du skapade de virtuella datorerna.
2. Kontrol lera att datorerna har anslutits till **Corp.contoso.com**.
3. Vänta tills den SQL Server installationen Slutför körningen av automatiserade initierings aktiviteter innan du fortsätter.
4. Öppna ett PowerShell-fönster i administratörs läge.
5. Installera funktionen för redundanskluster i Windows.

    ```powershell
    Import-Module ServerManager
    Add-WindowsFeature Failover-Clustering
    ```

6. Lägg till **CORP\Install** som lokal administratör.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    ```

7. Importera SQL Server PowerShell-providern.

    ```powershell
    Set-ExecutionPolicy -Execution RemoteSigned -Force
    Import-Module -Name "sqlps" -DisableNameChecking
    ```

8. Lägg till **CORP\Install** som sysadmin-rollen för standard SQL Server-instansen.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
    ```

9. Lägg till **NT instans\system** som en inloggning med de tre behörigheter som beskrivs ovan.

    ```powershell
    Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    ```

10. Öppna brand väggen för fjärråtkomst av SQL Server.

    ```powershell
     netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
    ```

11. Logga ut från båda virtuella datorerna.

    ```powershell
     logoff.exe
    ```

Slutligen är du redo att konfigurera tillgänglighets gruppen. Du använder SQL Server PowerShell-providern för att utföra allt arbete på **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurera tillgänglighets gruppen
1. Anslut till **ContosoSQL1** igen genom att starta fjärr skrivbords filerna. Logga in med hjälp av **CORP\Install**i stället för att logga in med hjälp av dator kontot.
2. Öppna ett PowerShell-fönster i administratörs läge.
3. Definiera följande variabler:

    ```powershell
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
    ```

4. Importera SQL Server PowerShell-providern.

    ```powershell
    Set-ExecutionPolicy RemoteSigned -Force
    Import-Module "sqlps" -DisableNameChecking
    ```

5. Ändra SQL Server tjänst konto för ContosoSQL1 till CORP\SQLSvc1.

    ```powershell
    $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
    $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
    $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
    $svc1.Stop()
    $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
    $svc1.Start();
    $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
    ```

6. Ändra SQL Server tjänst konto för ContosoSQL2 till CORP\SQLSvc2.

    ```powershell
    $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
    $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
    $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
    $svc2.Stop()
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
    $svc2.Start();
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
    ```

7. Ladda ned **CreateAzureFailoverCluster.ps1** från [skapa redundanskluster för Always on-TILLGÄNGLIGHETSGRUPPER i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) till den lokala arbets katalogen. Du använder det här skriptet för att hjälpa dig att skapa ett funktionellt kluster för växling vid fel. Viktig information om hur Windows-redundanskluster interagerar med Azure-nätverket finns i [hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Ändra till din arbets katalog och skapa redundansklustret med det nedladdade skriptet.

    ```powershell
    Set-ExecutionPolicy Unrestricted -Force
    .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
    ```

9. Aktivera Always on-tillgänglighetsgrupper för standard SQL Server instanser på **ContosoSQL1** och **ContosoSQL2**.

    ```powershell
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
    ```

10. Skapa en säkerhets kopierings katalog och bevilja behörigheter för SQL Server tjänst konton. Du använder den här katalogen för att förbereda tillgänglighets databasen på den sekundära repliken.

    ```powershell
    $backup = "C:\backup"
    New-Item $backup -ItemType directory
    net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
    icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
    ```

11. Skapa en databas på **ContosoSQL1** som kallas **MyDB1**, gör både en fullständig säkerhets kopia och en logg säkerhets kopiering och Återställ dem på **CONTOSOSQL2** med alternativet **med NORECOVERY** .

    ```powershell
    Invoke-SqlCmd -Query "CREATE database $db"
    Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
    Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
    Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
    Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
    ```

12. Skapa tillgänglighets gruppens slut punkter på SQL Server virtuella datorer och ange rätt behörigheter för slut punkterna.

    ```powershell
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
    ```

13. Skapa tillgänglighets replikerna.

    ```powershell
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
    ```

14. Skapa slutligen tillgänglighets gruppen och Anslut den sekundära repliken till tillgänglighets gruppen.

    ```powershell
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
    ```

## <a name="next-steps"></a>Nästa steg
Nu har du implementerat SQL Server Always on genom att skapa en tillgänglighets grupp i Azure. Information om hur du konfigurerar en lyssnare för den här tillgänglighets gruppen finns i [Konfigurera en ILB-lyssnare för Always on-tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Mer information om hur du använder SQL Server i Azure finns [SQL Server på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).
