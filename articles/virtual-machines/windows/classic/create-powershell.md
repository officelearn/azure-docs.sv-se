---
title: Skapa en Windows VM med PowerShell | Microsoft Docs
description: "Skapa Windows-datorer med hjälp av Azure PowerShell och den klassiska distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 75c6cf17ee269ae169d9f2f748d0985ca07e454e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Skapa en Windows-dator med PowerShell och den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [Azure portal – Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dessa steg visar hur du anpassar en uppsättning Azure PowerShell-kommandon som kan skapa och förkonfigurera en Windows-baserad Azure virtuella med hjälp av en byggblock-metod. Du kan använda den här processen att snabbt skapa en kommandouppsättning för en ny Windows-baserad virtuell dator och expandera en befintlig distribution eller skapa flera uppsättningar som snabbt skapar en anpassad utveckling och testning eller IT-teknikern miljö.

Här följer en Fyll-i-tomrum metod för att skapa uppsättningar med Azure PowerShell. Den här metoden kan vara användbart om du är nybörjare på PowerShell eller om du bara vill veta vilka värden du anger för lyckad konfiguration. Avancerade PowerShell-användare kan ta kommandon och ersätta deras egna värden för variabler (rader som börjar med ”$”).

Om du inte redan har gjort så, följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) att installera Azure PowerShell på den lokala datorn. Öppna en kommandotolk i Windows PowerShell.

## <a name="step-1-add-your-account"></a>Steg 1: Lägg till ditt konto
1. I PowerShell-Kommandotolken skriver **Add-AzureAccount** och på **RETUR**. 
2. Skriv e-postadressen som är associerade med din Azure-prenumeration och klicka på **Fortsätt**. 
3. Skriv in lösenordet för ditt konto. 
4. Klicka på **logga in**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Steg 2: Ange din prenumeration och storage-konto
Ange din Azure-prenumeration och storage-konto genom att köra dessa kommandon i Kommandotolken för Windows PowerShell. Ersätt allt inom citattecken, inklusive den < och > tecken, med rätt namn.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Du kan hämta rätt prenumerationsnamn från egenskapen SubscriptionName för utdata från den **Get-AzureSubscription** kommando. Du kan hämta rätt lagringskontonamnet från egenskapen Label för utdata från den **Get-AzureStorageAccount** kommando när du kör den **Välj AzureSubscription** kommando.

## <a name="step-3-determine-the-imagefamily"></a>Steg 3: Bestäm ImageFamily
Därefter måste du bestämma ImageFamily eller etiketten för den specifika bilden som motsvarar den Azure-dator som du vill skapa. Du kan hämta listan över tillgängliga ImageFamily värden med det här kommandot.

    Get-AzureVMImage | select ImageFamily -Unique

Här följer några exempel på ImageFamily värden för Windows-baserade datorer:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise på Windows Server 2012

Om du hittar den bild som du söker efter, öppnar du en ny instans av textredigerare ditt val eller PowerShell Integrated Scripting Environment (ISE). Kopiera följande till ny textfil eller PowerShell ISE ersätter ImageFamily-värdet.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

I vissa fall är avbildningens namn i egenskapen Label i stället för ImageFamily-värdet. Om du inte kan hitta den bild som du söker efter med egenskapen ImageFamily lista avbildningar av egenskapen etikett med det här kommandot.

    Get-AzureVMImage | select Label -Unique

Om du hittar rätt avbildningen med det här kommandot kan du öppna en ny instans av textredigerare ditt val eller PowerShell ISE. Kopiera följande till ny textfil eller PowerShell ISE ersätter värdet etikett.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Steg 4: Skapa dina kommandot set
Skapa resten av kommandot genom kopiering lämplig uppsättning block nedan i den nya textfilen eller av ISE och fylla i variabelvärdena och tar bort den < och > tecken. Finns två [exempel](#examples) i slutet av den här artikeln för en uppfattning av slutresultatet.

Starta ditt kommando anges genom att välja ett av dessa två kommandon (krävs).

Alternativ 1: Ange ett namn för virtuell dator och en storlek.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Alternativ 2: Ange ett namn, storlek och namn på tillgänglighetsuppsättning.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

InstanceSize-värden för D-, DS- eller G-serien virtuella datorer finns i [virtuell dator och Molntjänststorlekar för Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Om du har ett Enterprise-avtal med Software Assurance och vill dra nytta av Windows Server [Hybrid Använd förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/), lägga till den **- LicenseType** parametern till den  **Nya AzureVMConfig** cmdlet, skicka värdet **Windows_Server** för vanliga användningsfall.  Kontrollera att du använder en bild som du har överfört; Du kan inte använda en standardiserad avbildning från galleriet med Hybrid använda förmånen.
> 
> 

Du kan också ange det lokala administratörskontot och lösenord för en fristående Windows-dator.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Välj ett starkt lösenord. Du kan kontrollera dess styrkan [lösenord layout: med starka lösenord](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Du kan också ange det lokala administratörskontot och lösenordet och domänen, och namn och lösenord för ett domänkonto för att lägga till Windows-dator till en befintlig Active Directory-domän.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

För ytterligare före konfigurationsalternativ för Windows-baserade virtuella datorer, se syntaxen för den **Windows** och **WindowsDomain** parameteruppsättningar [Lägg till AzureProvisioningConfig ](/powershell/module/azure/add-azureprovisioningconfig).

Du kan också tilldela den virtuella datorn en specifik IP-adress, som kallas en statisk DIP.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Du kan kontrollera att en specifik IP-adress är tillgänglig med:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Du kan också tilldela den virtuella datorn till ett specifikt undernät i Azure-nätverk.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Du kan också lägga till en enda datadisk till den virtuella datorn.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Ange $hcaching till ”None” för en Active Directory-domänkontrollant.

Du kan också lägga till den virtuella datorn till en befintlig belastningsutjämnad uppsättning för externa trafiken.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Slutligen ska du välja en av dessa nödvändiga kommando för att skapa den virtuella datorn.

Alternativ 1: Skapa den virtuella datorn i en befintlig molntjänst.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Det korta namnet för Molntjänsten är det namn som visas i listan över molntjänster i Azure-portalen eller i listan över resursgrupper i Azure-portalen.

Alternativ 2: Skapa den virtuella datorn i en befintlig molntjänst och virtuella nätverk.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Steg 5: Kör din kommandot set
Granska uppsättningen Azure PowerShell-kommando du skapat i en textredigerare eller PowerShell ISE som består av flera block med kommandon från steg 4. Se till att du har angett alla nödvändiga variabler och att de har rätt värden. Kontrollera också att du har tagit bort alla de < och > tecken.

Om du använder en textredigerare, kopiera kommandot till Urklipp och högerklickar på dina öppna Windows PowerShell-Kommandotolken. Detta utfärda kommandot som en serie av PowerShell-kommandon och skapa din virtuella Azure-datorn. Du kan också köra kommandot i PowerShell ISE.

Om du skapar den här virtuella datorn igen eller en liknande, kan du:

* Spara det här kommandot som angetts som en PowerShell-skriptfil (*.ps1).
* Spara det här kommandot som angetts som en Azure Automation-runbook i den **Automation-konton** på Azure portal.

## <a id="examples"></a>Exempel
Här är två exempel på hur stegen ovan för att skapa Azure PowerShell-kommando anger som skapar Windows-baserade virtuella Azure-datorer.

### <a name="example-1"></a>Exempel 1
Jag behöver en PowerShell kommandouppsättning skapa den första virtuella datorn för en Active Directory-domänkontrollant som:

* Använder Windows Server 2012 R2 Datacenter-avbildning.
* Har namnet AZDC1.
* Är en fristående dator.
* Har en ytterligare datadisk på 20 GB.
* Har den statiska IP-adressen 192.168.244.4.
* Är i BackEnd-undernät för det virtuella nätverket AZDatacenter.
* Är i Azure-leksaksladan Molntjänsten.

Det här är den motsvarande Azure PowerShell-kommando som vill skapa den virtuella datorn med tomma rader mellan varje block för läsbarhet.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Exempel 2
Jag behöver en PowerShell kommandouppsättning skapa en virtuell dator för en line-of-business-server som:

* Använder Windows Server 2012 R2 Datacenter-avbildning.
* Har namnet LOB1.
* Är medlem i domänen corp.contoso.com.
* Har en ytterligare datadisk 200 GB.
* Är i undernätet FrontEnd för det virtuella nätverket AZDatacenter.
* Är i Azure-leksaksladan Molntjänsten.

Det här är den motsvarande Azure PowerShell-kommando som vill skapa den virtuella datorn.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Nästa steg
Om du behöver en OS-disk som är större än 127 GB, kan du [Expandera enhetens OS](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

