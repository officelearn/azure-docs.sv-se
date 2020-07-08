---
title: Använda Azure Premium Storage med SQL Server | Microsoft Docs
description: Den här artikeln använder resurser som skapats med den klassiska distributions modellen och ger vägledning om hur du använder Azure Premium Storage med SQL Server som körs på Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ca11fce252192cbf8e5f0bc2cfb5fcd38f5d4443
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84020888"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Använd Azure Premium Storage med SQL Server på Virtual Machines

## <a name="overview"></a>Översikt

[Azure Premium-SSD](../disks-types.md) är nästa generations lagring som ger låg latens och högt data flöde i/o. Det fungerar bäst för nyckel-i/o-intensiva arbets belastningar, till exempel SQL Server på IaaS [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln innehåller planering och vägledning för att migrera en virtuell dator som kör SQL Server att använda Premium Storage. Detta omfattar Azure-infrastruktur (nätverk, lagring) och virtuella gäst datorer i Windows. Exemplet i [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) visar en fullständig heltäckande migrering av hur du flyttar större virtuella datorer för att dra nytta av förbättrad lokal SSD-lagring med PowerShell.

Det är viktigt att förstå processen från slut punkt till slut punkt för användning av Azure Premium Storage med SQL Server på virtuella IAAS-datorer. Det här omfattar:

* Identifiering av de nödvändiga förutsättningarna för att använda Premium Storage.
* Exempel på distribution av SQL Server på IaaS till Premium Storage för nya distributioner.
* Exempel på migrering av befintliga distributioner, både fristående servrar och distributioner med SQL Always on-tillgänglighetsgrupper.
* Möjliga metoder för migrering.
* Komplett exempel från slut punkt till slut punkt som visar Azure, Windows och SQL Server steg för migrering av en befintlig Always on-implementering.

Mer bakgrunds information om SQL Server i Azure Virtual Machines finns i [SQL Server i azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Författare:** Daniel sol **teknisk granskare:** Luis Carlos Vargas sill, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Krav för Premium Storage

Det finns flera krav för att använda Premium Storage.

### <a name="machine-size"></a>Dator storlek

För att använda Premium Storage måste du använda DS-serien Virtual Machines (VM). Om du inte har använt DS-serien datorer i moln tjänsten tidigare måste du ta bort den befintliga virtuella datorn, behålla de anslutna diskarna och sedan skapa en ny moln tjänst innan du skapar den virtuella datorn som DS * roll storlek. Mer information om storlekar för virtuella datorer finns i [virtuell dator och moln tjänst storlekar för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Molntjänster

Du kan bara använda DS * VM: ar med Premium Storage när de skapas i en ny moln tjänst. Om du använder SQL Server Always on i Azure, refererar Always on Listener till den Azure Internal eller externa Load Balancer IP-adress som är associerad med en moln tjänst. Den här artikeln fokuserar på hur du migrerar när du behåller tillgänglighet i det här scenariot.

> [!NOTE]
> En DS *-serie måste vara den första virtuella dator som distribueras till den nya moln tjänsten.
>
>

### <a name="regional-vnets"></a>Regional virtuella nätverk

För DS * VM: ar måste du konfigurera den Virtual Network (VNET) som är värd för de virtuella datorerna som regionala. Detta "ökar" det virtuella nätverket är att tillåta att större virtuella datorer tillhandahålls i andra kluster och tillåter kommunikation mellan dem. I följande skärm bild visar den markerade platsen regionala virtuella nätverk, medan det första resultatet visar ett "smal" VNET.

![RegionalVNET][1]

Du kan ge ett Microsoft-Support ärende till att migrera till ett regionalt VNET. Microsoft gör en ändring. Om du vill slutföra migreringen till regionala virtuella nätverk ändrar du egenskapen AffinityGroup i nätverks konfigurationen. Exportera först nätverks konfigurationen i PowerShell och ersätt sedan egenskapen **AffinityGroup** i elementet **VirtualNetworkSite** med egenskapen **location** . Ange `Location = XXXX` var `XXXX` är en Azure-region. Importera sedan den nya konfigurationen.

Anta till exempel följande VNET-konfiguration:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Om du vill flytta detta till ett regionalt VNET i Västeuropa, ändrar du konfigurationen till följande:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Lagringskonton

Du måste skapa ett nytt lagrings konto som har kon figurer ATS för Premium Storage. Observera att användningen av Premium Storage anges på lagrings kontot, inte på enskilda virtuella hård diskar, men när du använder en DS * Series VM kan du koppla en virtuell hård disk från Premium-och standard lagrings kontona. Du kan tänka på detta om du inte vill placera OS-VHD: n på Premium Storage kontot.

Följande **New-AzureStorageAccountPowerShell-** kommando med **typen** "Premium_LRS" skapar ett Premium Storage-konto:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Cache-inställningar för virtuella hård diskar

Den största skillnaden mellan att skapa diskar som ingår i ett Premium Storage-konto är inställningen för diskcachen. För SQL Server data volym diskar rekommenderar vi att du använder "**Read caching**". För transaktions loggs volymer ska inställningen för diskcachen anges till "**ingen**". Detta skiljer sig från rekommendationerna för standard lagrings konton.

När de virtuella hård diskarna har anslutits kan inte cache-inställningen ändras. Du måste koppla från och återansluta den virtuella hård disken med en uppdaterad cache-inställning.

### <a name="windows-storage-spaces"></a>Windows lagrings utrymmen

Du kan använda [Windows lagrings utrymmen](https://technet.microsoft.com/library/hh831739.aspx) som du gjorde med tidigare standard lagring, så att du kan migrera en virtuell dator som redan använder lagrings utrymmen. Exemplet i [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (steg 9 och framåt) visar PowerShell-koden för att extrahera och importera en virtuell dator med flera anslutna virtuella hård diskar.

Lagringspooler användes med standard Azure Storage-kontot för att förbättra data flödet och minska svars tiden. Du kan hitta värde i testa lagringspooler med Premium Storage för nya distributioner, men de lägger till ytterligare komplexitet med lagrings konfigurationen.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Så här tar du reda på vilka virtuella Azure-diskar som mappar till lagringspooler

Eftersom det finns olika inställningar för cachelagring för anslutna virtuella hård diskar kan du välja att kopiera de virtuella hård diskarna till ett Premium Storage-konto. Men när du återansluter dem till den nya VM: en VM-serien kan du behöva ändra cacheinställningarna. Det är enklare att tillämpa Premium Storage rekommenderade cacheinställningar när du har separata virtuella hård diskar för SQL-datafiler och loggfiler (i stället för en enda virtuell hård disk som innehåller båda).

> [!NOTE]
> Om du har SQL Server data-och loggfiler på samma volym, är det cachealternativ du väljer beroende av i/o-åtkomst mönster för dina databas arbets belastningar. Endast testning kan demonstrera vilket alternativ för cachelagring som passar bäst för det här scenariot.
>
>

Men om du använder Windows lagrings utrymmen som består av flera virtuella hård diskar måste du titta på de ursprungliga skripten för att identifiera vilka anslutna virtuella hård diskar som finns i vilken pool som helst, så du kan sedan ange cacheinställningarna för varje disk.

Om du inte har det ursprungliga skriptet tillgängligt för att visa vilka virtuella hård diskar som mappar till lagringspoolen kan du använda följande steg för att ta reda på mappningen för disk-och lagrings enheter.

Använd följande steg för varje disk:

1. Hämta lista över diskar som är anslutna till den virtuella datorn med kommandot **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Observera DiskName och LUN.

    ![DisknameAndLUN][2]
1. Fjärr skrivbord till den virtuella datorn. Gå sedan till **dator hantering**  |  **Enhetshanteraren**  |  **disk enheter**. Titta på egenskaperna för var och en av de "Microsoft Virtual disks"

    ![VirtualDiskProperties][3]
1. LUN-numret här är en referens till det LUN-nummer som du anger när du kopplar den virtuella hård disken till den virtuella datorn.
1. För "Microsoft Virtual Disk" går du till fliken **information** . i **egenskaps** listan går du sedan till **driv rutins nyckel**. I **värdet**noterar du **förskjutningen**, som är 0002 i följande skärm bild. 0002 anger de PhysicalDisk2 som lagringspoolen refererar till.

    ![VirtualDiskPropertyDetails][4]
1. Dumpa ut de kopplade diskarna för varje lagringspool:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nu kan du använda den här informationen för att associera anslutna virtuella hård diskar till fysiska diskar i lagringspooler.

När du har mappat virtuella hård diskar till fysiska diskar i lagringspooler kan du koppla bort och kopiera dem till ett Premium Storage konto och sedan koppla dem till rätt cache-inställning. Se exemplet i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)steg 8 till 12. De här stegen visar hur du extraherar en VM-ansluten VHD-serverkonfiguration till en CSV-fil, kopierar de virtuella hård diskarna, ändrar cache-inställningarna för disk konfigurationen och slutligen distribuerar om den virtuella datorn som en virtuell dator med DS-serien med alla anslutna diskar.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Bandbredd för VM-lagring och data flöde för VHD-lagring

Mängden lagrings prestanda beror på storleken på de virtuella datorerna i DS * och VHD-storlekarna. De virtuella datorerna har olika avdrag för antalet virtuella hård diskar som kan anslutas och den maximala bandbredd som de stöder (MB/s). För de angivna bandbredds numren, se [storlekar för virtuella datorer och moln tjänster för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ökad IOPS uppnås med större disk storlekar. Du bör tänka på detta när du tycker om din migrerings Sök väg. Mer information [finns i tabellen för IOPS-och disk typer](../disks-types.md#premium-ssd).

Tänk slutligen på att de virtuella datorerna har olika maximala disk bandbredder som de stöder för alla diskar som är anslutna. Under hög belastning kan du fylla den maximala disk bandbredden som är tillgänglig för den virtuella dator rollen. Till exempel en Standard_DS14 stöder upp till 512 MB/s; med tre P30-diskar kan du därför fylla disk bandbredden för den virtuella datorn. I det här exemplet kan data flödes gränsen överskridas beroende på blandningen av Läs-och skriv-IOs.

## <a name="new-deployments"></a>Nya distributioner

Följande två avsnitt visar hur du kan distribuera SQL Server virtuella datorer till Premium Storage. Som tidigare nämnts behöver du inte nödvändigt vis placera OS-disken på Premium Storage. Du kan välja att göra detta om du inte vill placera några intensiva IO-arbetsbelastningar på OS-VHD: n.

Det första exemplet demonstrerar användningen av befintliga Azure Gallery-avbildningar. Det andra exemplet visar hur du använder en anpassad virtuell dator avbildning som du har i ett befintligt standard lagrings konto.

> [!NOTE]
> Dessa exempel förutsätter att du redan har skapat ett regionalt VNET.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Skapa en ny virtuell dator med Premium Storage med Galleri avbildning

Exemplet nedan visar hur du placerar OS-VHD: n på Premium Storage och ansluter Premium Storage virtuella hård diskar. Du kan dock också placera OS-disken på ett standard lagrings konto och sedan ansluta virtuella hård diskar som finns i ett Premium Storage konto. Båda scenarierna visas.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Steg 1: skapa ett Premium Storage konto

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Steg 2: skapa en ny moln tjänst

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Steg 3: reservera en moln tjänst VIP (valfritt)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Steg 4: skapa en VM-behållare

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Steg 5: placera OS VHD på standard-eller Premium Storage

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Steg 6: skapa en virtuell dator

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified '-DiskSizeInGB 1023', this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Skapa en ny virtuell dator för att använda Premium Storage med en anpassad avbildning

Det här scenariot visar var du har befintliga anpassade avbildningar som finns på ett standard lagrings konto. Som det anges om du vill placera OS-VHD: n på Premium Storage måste du kopiera avbildningen som finns i standard lagrings kontot och överföra dem till en Premium Storage innan den kan användas. Om du har en avbildning lokalt kan du också använda den här metoden för att kopiera direkt till Premium Storage-kontot.

#### <a name="step-1-create-storage-account"></a>Steg 1: skapa lagrings konto

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Steg 2 Skapa moln tjänst

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Steg 3: Använd en befintlig avbildning

Du kan använda en befintlig avbildning. Du kan också [ta en bild av en befintlig dator](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observera att datorn som du avbildningen inte behöver vara DS * Machine. När du har avbildningen visar följande steg hur du kopierar den till Premium Storage-kontot med **AzureStorageBlobCopy PowerShell-** kommandot.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Steg 4: kopiera blobben mellan lagrings konton

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Steg 5: kontrol lera kopierings statusen regelbundet:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Steg 6: Lägg till avbildnings disk till Azure disk-lagringsplatsen i prenumerationen

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Det kan hända att du fortfarande får ett disk låne fel om status rapporterna är lyckade. Vänta i det här fallet cirka 10 minuter.

#### <a name="step-7--build-the-vm"></a>Steg 7: Bygg den virtuella datorn

Här skapar du den virtuella datorn från avbildningen och ansluter två Premium Storage virtuella hård diskar:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Befintliga distributioner som inte använder tillgänglighets grupper som alltid är tillgängliga

> [!NOTE]
> För befintliga distributioner läser du först avsnittet [krav](#prerequisites-for-premium-storage) i den här artikeln.

Det finns olika överväganden för SQL Server distributioner som inte använder alltid tillgänglighets grupper och de som gör. Om du inte använder Always on och har en befintlig fristående SQL Server, kan du uppgradera till Premium Storage med hjälp av en ny moln tjänst och ett nytt lagrings konto. Överväg följande alternativ:

* **Skapa en ny SQL Server VM**. Du kan skapa en ny SQL Server VM som använder ett Premium Storage konto, enligt beskrivningen i nya distributioner. Säkerhetskopiera och återställ sedan SQL Server-konfigurationen och användar databaserna. Programmet måste uppdateras för att referera till det nya SQL Server om det används internt eller externt. Du måste kopiera alla "out of dB"-objekt som om du utförde en sida vid sida (SxS) SQL Server migrering. Detta inkluderar objekt som inloggningar, certifikat och länkade servrar.
* **Migrera en befintlig SQL Server VM**. Detta kräver att du tar SQL Server VM offline och sedan överför den till en ny moln tjänst, som inkluderar kopiering av alla anslutna virtuella hård diskar till Premium Storage-kontot. När den virtuella datorn är online refererar programmet till serverns värdnamn som tidigare. Tänk på att storleken på den befintliga disken påverkar prestanda egenskaperna. Till exempel blir en 400 GB-disk avrundad till en P20. Om du vet att du inte behöver disk prestanda kan du återskapa den virtuella datorn som en virtuell dator i VM-serien och bifoga Premium Storage virtuella hård diskar med den storlek/prestanda-specifikation som du behöver. Sedan kan du koppla från och återansluta SQL DB-filerna.

> [!NOTE]
> När du kopierar de virtuella hård diskarna bör du vara medveten om storleken, beroende på storleken betyder vad Premium Storage disk typen de använder, vilket fastställer disk prestanda specifikation. Azure avrundar upp till närmaste disk storlek, så om du har en 400 GB disk avrundas detta uppåt till en P20. Beroende på dina befintliga IO-krav för OS-VHD: n behöver du kanske inte migrera det till ett Premium Storage-konto.

Om din SQL Server används externt ändras moln tjänstens VIP-ändringar. Du måste också uppdatera slut punkter, ACL: er och DNS-inställningar.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Befintliga distributioner som använder Always on-tillgänglighetsgrupper

> [!NOTE]
> För befintliga distributioner läser du först avsnittet [krav](#prerequisites-for-premium-storage) i den här artikeln.

Från början i det här avsnittet tittar vi på hur Always on interagerar med Azure-nätverk. Vi bryter sedan migreringarna i till två scenarier: migreringar där viss nedtid kan tolereras och migreringar där du måste uppnå minimal stillestånds tid.

Lokala SQL Server Always on-tillgänglighetsgrupper använder en lyssnare lokalt som registrerar ett virtuellt DNS-namn tillsammans med en IP-adress som delas mellan en eller flera SQL-servrar. När klienterna ansluter de dirigeras genom lyssnar-IP: n till den primära SQL Server. Detta är den server som äger Always on IP-resursen vid den tidpunkten.

![DeploymentsUseAlways på][6]

I Microsoft Azure kan du bara ha en IP-adress tilldelad till ett nätverkskort på den virtuella datorn, så för att kunna uppnå samma skikt för abstraktion som lokalt, använder Azure den IP-adress som är tilldelad intern/extern belastningsutjämnare (ILB/ELB). IP-resursen som delas mellan-servrarna har angetts till samma IP som ILB/ELB. Detta publiceras i DNS och klient trafik skickas via ILB/ELB till den primära SQL Server repliken. ILB/ELB vet vilken SQL Server är primär eftersom den använder avsökningar för att avsöka den Always på IP-resursen. I det tidigare exemplet avsöker den varje nod som har en slut punkt som refereras av ELB/ILB, beroende på vilka svar som är primär SQL Server.

> [!NOTE]
> ILB och ELB är båda tilldelade till en viss Azure-moln tjänst, och därför innebär en moln migrering i Azure förmodligen att den Load Balancer IP-adressen ändras.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrera Always on-distributioner som kan tillåta viss nedtid

Det finns två strategier för att migrera Always on-distributioner som tillåter vissa avbrott:

1. **Lägg till fler sekundära repliker till ett befintligt Always on-kluster**
2. **Migrera till ett nytt Always on-kluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Lägg till fler sekundära repliker till ett befintligt Always on-kluster

En strategi är att lägga till fler zoner i tillgänglighets gruppen Always On. Du måste lägga till dessa i en ny moln tjänst och uppdatera lyssnaren med den nya IP-adressen för belastnings utjämning.

##### <a name="points-of-downtime"></a>Drifts punkter:

* Kluster validering.
* Testa alltid vid redundans för nya sekundära servrar.

Om du använder Windows-lagringspooler i den virtuella datorn för högre IO-genomflöde, kopplas dessa till varandra under en fullständig kluster validering. Verifierings testet krävs när du lägger till noder i klustret. Hur lång tid det tar att köra testet kan variera, så du bör testa detta i din representativa test miljö för att få en ungefärlig tid på hur lång tid det tar.

Du bör fastställa i vilken tid du kan utföra manuella redundans-och kaos-tester på de nyligen tillagda noderna för att säkerställa att funktioner för hög tillgänglighet alltid fungerar som förväntat.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Du bör stoppa alla instanser av SQL Server där lagringspooler används innan valideringen körs.
>
> ##### <a name="high-level-steps"></a>Övergripande steg
>

1. Skapa två nya SQL-servrar i ny moln tjänst med bifogade Premium Storage.
2. Kopiera över fullständiga säkerhets kopieringar och Återställ med **NORECOVERY**.
3. Kopiera över "out of User DB" beroende objekt, till exempel inloggningar osv.
4. Skapa en ny intern Load Balancer (ILB) eller Använd en extern Load Balancer (ELB) och ställ sedan in belastningsutjämnade slut punkter på båda nya noderna.

   > [!NOTE]
   > Kontrol lera att alla noder har rätt slut punkts konfiguration innan du fortsätter
   >
   >
5. Stoppa användar-/program åtkomst till SQL Server (om du använder lagringspooler).
6. Stoppa SQL Server motor tjänster på alla noder (om du använder lagringspooler).
7. Lägg till nya noder i klustret och kör fullständig verifiering.
8. Starta alla SQL Server-tjänster när verifieringen är klar.
9. Säkerhetskopiera transaktions loggar och Återställ användar databaser.
10. Lägg till nya noder i tillgänglighets gruppen Always on och placera replikeringen i **synkront**.
11. Lägg till IP-adressresursen för den nya moln tjänsten ILB/ELB via PowerShell för Always on baserat på exemplet på flera platser i [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). I Windows-klustring ställer du in **möjliga ägare** av **IP** -adressresursen till de nya noderna gamla. Se avsnittet "lägga till IP-adressresurs i samma undernät" i [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Redundansväxla till en av de nya noderna.
13. Gör de nya noderna till automatisk redundansväxling och testa redundansväxlingen.
14. Ta bort ursprungliga noder från tillgänglighets gruppen.

##### <a name="advantages"></a>Fördelar

* Nya SQL-servrar kan testas (SQL Server och program) innan de läggs till i Always On.
* Du kan ändra storleken på den virtuella datorn och anpassa lagringen efter dina specifika krav. Det skulle dock vara fördelaktigt att behålla alla sökvägar till SQL-filer.
* Du kan kontrol lera när överföringen av DB-säkerhetskopieringarna till de sekundära replikerna har startats. Det skiljer sig från att använda Azure **Start-AzureStorageBlobCopy-** kommandot för att kopiera virtuella hård diskar, eftersom det är en asynkron kopia.

##### <a name="disadvantages"></a>Nackdelar

* När du använder Windows-lagringspooler uppstår ett kluster avbrott under den fullständiga kluster verifieringen för de nya noderna.
* Beroende på SQL Server version och det befintliga antalet sekundära repliker kanske du inte kan lägga till fler sekundära repliker utan att ta bort befintliga sekundära.
* Det kan finnas lång tid för SQL-dataöverföring när sekundär inställningarna skulle skapas.
* Det finns ytterligare kostnader under migreringen medan nya datorer körs parallellt.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrera till ett nytt Always on-kluster

En annan strategi är att skapa ett helt nytt Always on-kluster med helt nya noder i en ny moln tjänst och sedan omdirigera klienterna så att de använder den.

##### <a name="points-of-downtime"></a>Drifts punkter

Det finns avbrott när du överför program och användare till den nya Always on-lyssnaren. Stillestånds tiden beror på:

* Den tid det tar att återställa slutgiltiga transaktions logg säkerhets kopior till databaser på nya servrar.
* Den tid det tar att uppdatera klient program för att använda ny Always on-lyssnare.

##### <a name="advantages"></a>Fördelar

* Du kan testa den faktiska produktions miljön, SQL Server och operativ systemets build-ändringar.
* Du kan anpassa lagringen och eventuellt minska storleken på den virtuella datorn. Detta kan leda till kostnads minskning.
* Du kan uppdatera SQL Server version eller version under den här processen. Du kan också uppgradera operativ systemet.
* Föregående Always on-kluster kan fungera som ett fast återställnings mål.

##### <a name="disadvantages"></a>Nackdelar

* Du måste ändra DNS-namnet på lyssnaren om du vill att båda ska vara alltid i kluster som körs samtidigt. Detta lägger till administrations kostnader under migreringen eftersom klient program strängarna måste återspegla det nya lyssnar namnet.
* Du måste implementera en mekanism för synkronisering mellan de två miljöerna för att hålla dem så nära som möjligt för att minimera de slutliga kraven för synkronisering före migreringen.
* Det finns extra kostnad under migreringen medan den nya miljön körs.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrera Always on-distributioner för minimal stillestånds tid

Det finns två strategier för migrering av Always on-distributioner för minimal nedtid:

1. **Använd en befintlig sekundär: enskild plats**
2. **Använd befintliga sekundära repliker: flera platser**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Använd en befintlig sekundär: enskild plats

En strategi för minimal nedtid är att ta en befintlig moln sekundär och ta bort den från den aktuella moln tjänsten. Kopiera sedan de virtuella hård diskarna till det nya Premium Storage-kontot och skapa den virtuella datorn i den nya moln tjänsten. Uppdatera sedan lyssnaren i kluster och redundans.

##### <a name="points-of-downtime"></a>Drifts punkter

* Det finns avbrott när du uppdaterar den sista noden med den belastningsutjämnade slut punkten.
* Din klient åter anslutning kan vara fördröjd beroende på din klient/DNS-konfiguration.
* Det finns ytterligare stillestånds tid om du väljer att ta alltid på kluster gruppen offline för att byta ut IP-adresserna. Du kan undvika detta genom att använda ett eller beroende och möjliga ägare för den tillagda IP-adressresursen. Se avsnittet "lägga till IP-adressresurs i samma undernät" i [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> När du vill att noden som ska läggas till ska partake i som en Always on-redundansrelation måste du lägga till en Azure-slutpunkt med en referens till den belastningsutjämnade uppsättningen. När du kör kommandot **Add-AzureEndpoint** för att göra detta är aktuella anslutningar fortfarande öppna, men det går inte att upprätta nya anslutningar till lyssnaren förrän belastningsutjämnaren har uppdaterats. Under testningen visade det senaste 90-120seconds. Detta bör testas.

##### <a name="advantages"></a>Fördelar

* Inga extra kostnader som uppstår under migreringen.
* En en-till-en-migrering.
* Minskad komplexitet.
* Tillåter ökad IOPS från Premium Storage SKU: er. När diskarna kopplas bort från den virtuella datorn och kopieras till den nya moln tjänsten kan ett verktyg från tredje part användas för att öka storleken på den virtuella hård disken, vilket ger högre data flöden. För att öka VHD-storlekar, se den här [Forum diskussionen](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nackdelar

* Det finns en temporär förlust av HA och DR under migreringen.
* Eftersom det här är en 1:1-migrering måste du använda en minsta VM-storlek som stöder ditt antal virtuella hård diskar, så du kanske inte kan downsize dina virtuella datorer.
* I det här scenariot används Azure **Start-AzureStorageBlobCopy-** kommandot, som är asynkron. Det finns inget service avtal för slutförd kopiering. Tiden för kopiorna varierar, men detta beror på hur mycket data som ska överföras. Kopierings tiden ökar om överföringen skickas till ett annat Azure-datacenter som stöder Premium Storage i en annan region. Om du bara har två noder bör du överväga en eventuell minskning om kopian tar längre tid än under testningen. Detta kan innefatta följande idéer.
  * Lägg till en tillfällig tredje SQL Server nod för HA innan migreringen med överenskommet avbrott.
  * Kör migreringen utanför schemalagt Azure-underhåll.
  * Se till att du har konfigurerat klustrets kvorum korrekt.  

##### <a name="high-level-steps"></a>Övergripande steg

Det här dokumentet visar inte en fullständig end to end-exempel, men [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) innehåller information som kan utnyttjas för att utföra detta.

![MinimalDowntime][8]

* Samla in disk konfiguration och ta bort noden (ta inte bort anslutna virtuella hård diskar).
* Skapa ett Premium Storage konto och kopiera virtuella hård diskar från standard lagrings kontot
* Skapa en ny moln tjänst och distribuera om den virtuella SQL2-datorn i moln tjänsten. Skapa den virtuella datorn med den kopierade ursprungliga OS-VHD: n och bifoga de kopierade virtuella hård diskarna.
* Konfigurera ILB/ELB och Lägg till slut punkter.
* Uppdatera lyssnare genom att antingen:
  * Ta alltid på gruppen offline och uppdatera Always on-lyssnaren med New ILB/ELB IP-adress.
  * Eller lägga till IP-adressresursen för nya Cloud Service-ILB/ELB via PowerShell i Windows-klustring. Ange sedan möjliga ägare av IP-adressresursen till den migrerade noden, SQL2 och ange detta som eller beroende i nätverks namnet. Se avsnittet "lägga till IP-adressresurs i samma undernät" i [bilagan](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Kontrol lera DNS-konfigurationen/spridningen till klienterna.
* Migrera SQL1 VM och gå igenom steg 2 – 4.
* Om du använder steg 5ii lägger du till SQL1 som möjlig ägare för den tillagda IP-adressresursen
* Redundanstest.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Använd befintliga sekundära repliker: flera platser

Om du har noder i fler än ett Azure-datacenter (DC) eller om du har en hybrid miljö kan du använda en Always On-konfiguration i den här miljön för att minimera stillestånds tiden.

Metoden är att ändra Always on-synkronisering till synkront för den lokala eller sekundära Azure-DOMÄNKONTROLLANTen och sedan redundansväxla till den SQL Server. Kopiera sedan de virtuella hård diskarna till ett Premium Storage-konto och distribuera om datorn till en ny moln tjänst. Uppdatera lyssnaren och växla sedan tillbaka.

##### <a name="points-of-downtime"></a>Drifts punkter

Stillestånds tiden består av tiden för redundans till den alternativa DOMÄNKONTROLLANTen och tillbaka. Det beror också på din klient-/DNS-konfiguration och din klient åter anslutning kan vara fördröjd.
Tänk på följande exempel på en hybrid Always On-konfiguration:

![MultiSite1][9]

##### <a name="advantages"></a>Fördelar

* Du kan använda en befintlig infrastruktur.
* Du har möjlighet att innan du uppgraderar Azure Storage på DR Azure-DOMÄNKONTROLLANTen först.
* DR Azure DC-lagringen kan konfigureras om.
* Det finns minst två redundanser under migreringen, med undantag för redundanstest.
* Du behöver inte flytta SQL Server data med säkerhets kopiering och återställning.

##### <a name="disadvantages"></a>Nackdelar

* Beroende på klient åtkomsten till SQL Server kan det öka svars tiden när SQL Server körs i en alternativ DOMÄNKONTROLLANT till programmet.
* Kopierings tiden för virtuella hård diskar till Premium Storage kan vara lång. Detta kan påverka ditt beslut om du vill behålla noden i tillgänglighets gruppen. Överväg detta för när logg intensiv arbets belastning körs under migreringen krävs, eftersom den primära noden måste behålla de replikerade transaktionerna i transaktions loggen. Detta kan därför växa avsevärt.
* I det här scenariot används Azure **Start-AzureStorageBlobCopy-** kommandot, som är asynkron. Det finns inget service avtal för slut för ande. Tiden för kopiorna varierar, medan detta beror på vänta i kö, det beror också på mängden data som ska överföras. Därför har du bara en nod i ditt andra data Center. du bör vidta åtgärder för att undvika att kopian tar längre tid än under testningen. De här minsknings stegen omfattar följande idéer:
  * Lägg till en tillfällig SQL-nod för HA innan migreringen med överenskommet avbrott.
  * Kör migreringen utanför schemalagt Azure-underhåll.
  * Se till att du har konfigurerat klustrets kvorum korrekt.

I det här scenariot förutsätter vi att du har dokumenterat din installation och vet hur lagrings utrymmet mappas för att göra ändringar för optimala cacheinställningar.

##### <a name="high-level-steps"></a>Övergripande steg

![Multisite2][10]

* Gör den lokala/alternativa Azure-DOMÄNKONTROLLANTen till SQL Server primär, och gör den till den andra automatiska redundansväxlingen (AFP).
* Samla in disk konfigurations information från SQL2 och ta bort noden (ta inte bort anslutna virtuella hård diskar).
* Skapa ett Premium Storage konto och kopiera virtuella hård diskar från standard lagrings kontot.
* Skapa en ny moln tjänst och skapa den virtuella SQL2-datorn med dess Premium Storage-diskar.
* Konfigurera ILB/ELB och Lägg till slut punkter.
* Uppdatera Always on-lyssnare med ny ILB-/ELB-IP-adress och redundanstest.
* Kontrol lera DNS-konfigurationen.
* Ändra AFP till SQL2 och migrera sedan SQL1 och gå igenom steg 2 – 5.
* Redundanstest.
* Ändra AFP tillbaka till SQL1 och SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Bilaga: migrerar en multisite alltid på kluster till Premium Storage

Resten av den här artikeln innehåller ett detaljerat exempel på hur du konverterar en multi-site Always on-kluster till Premium Storage. Den konverterar också lyssnaren från att använda en extern belastningsutjämnare (ELB) till en intern belastningsutjämnare (ILB).

### <a name="environment"></a>Miljö

* Windows-2k12/SQL-2k12
* 1 DB-filer på SP
* 2 x lagringspooler per nod

![Appendix1][11]

### <a name="vm"></a>DATORN

I det här exemplet ska vi demonstrera flytt från en ELB till ILB. ELB var tillgänglig före ILB, så detta visar hur du växlar till ILB under migreringen.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Förberedande steg: Anslut till prenumeration

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Steg 1: skapa ett nytt lagrings konto och en moln tjänst

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Steg 2: öka antalet tillåtna problem på resurser\<Optional>

På vissa resurser som tillhör din Always tillgänglighets grupp finns det begränsningar för hur många fel som kan inträffa under en period, där kluster tjänsten försöker starta om resurs gruppen. Vi rekommenderar att du ökar detta medan du går igenom den här proceduren, eftersom om du inte manuellt redundansväxlas och utlöser redundans genom att stänga av datorer som du kan komma nära den här gränsen.

Det skulle vara försiktig med att göra detta i Klusterhanteraren för växling vid fel genom att gå till egenskaperna för Always on-resurs gruppen:

![Appendix3][13]

Ändra Max felen till 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Steg 3: lägga till IP-adressresurs för kluster grupp\<Optional>

Om du bara har en IP-adress för kluster gruppen och detta är justerat till moln under nätet, är du uppmärksam på om du oavsiktligt tar offline alla klusternoder i molnet i nätverket, så kan klustrets IP-resurs och kluster nätverks namn inte anslutas. I den här situationen förhindrar den uppdateringar till andra kluster resurser.

#### <a name="step-4-dns-configuration"></a>Steg 4: DNS-konfiguration

Hur du implementerar en smidig över gång beror på hur DNS används och uppdateras.
När Always On är installerat skapas en resurs grupp för Windows-kluster, om du öppnar Klusterhanteraren för växling vid fel, ser du att minst en av dem har tre resurser, de två som dokumentet refererar till:

* Virtual Network namn (VNN) – DNS-namnet som klienterna ansluter till när de vill ansluta till SQL-servrar via Always On.
* IP-adressresurs – IP-adressen som är associerad med VNN, du kan ha mer än en, och i en multisitedistribution har du en IP-adress per plats/undernät.

När du ansluter till SQL Server hämtar SQL Server klient driv rutinen de DNS-poster som är associerade med lyssnaren och försöker ansluta till varje Always på tillhör ande IP-adress. Nu diskuterar vi vissa faktorer som kan påverka detta.

Antalet samtidiga DNS-poster som är associerade med lyssnar namnet beror inte bara på antalet associerade IP-adresser, men RegisterAllIpProviders'setting i kluster för växling vid fel för Always ON-VNN-resursen.

När du distribuerar Always on i Azure finns det olika steg för att skapa lyssnare och IP-adresser. du måste konfigurera "RegisterAllIpProviders" manuellt till 1. Detta skiljer sig från en lokal Always on-distribution där det redan har angetts till 1.

Om ' RegisterAllIpProviders ' är 0 visas bara en DNS-post i DNS som är associerad med lyssnaren:

![Appendix4][14]

Om ' RegisterAllIpProviders ' är 1:

![Appendix5][15]

Koden nedan dumpar ut VNN-inställningarna och ställer in den åt dig. För att ändringen ska börja gälla måste du koppla från VNN och sätta tillbaka den online. Detta gör att lyssnaren offline orsakar avbrott i klient anslutningen.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

I ett senare steg i migreringen måste du uppdatera Always on Listener med en uppdaterad IP-adress som refererar till en belastningsutjämnare, vilket innebär att en IP-adressresurs tas bort och läggs till. Efter IP-uppdateringen måste du se till att den nya IP-adressen har uppdaterats i DNS-zonen och att klienterna uppdaterar sin lokala DNS-cache.

Om dina klienter finns i ett annat nätverks segment och refererar till en annan DNS-server, måste du överväga vad som händer om överföringen av DNS-zonen under migreringen, eftersom tiden för att återansluta i programmet är begränsad minst zon överförings tiden för alla nya IP-adresser för lyssnaren. Om du använder en tids gräns här bör du diskutera och testa en stegvis zon överföring med dina Windows-team och även placera DNS-postposten i låg tid till Live (TTL) så att klienterna uppdateras. Mer information finns i [stegvisa zon överföringar](https://technet.microsoft.com/library/cc958973.aspx) och [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Som standard är TTL för DNS-posten som är associerad med lyssnaren i Always on i Azure 1200 sekunder. Du kanske vill minska detta om du befinner dig under en tids gräns under migreringen för att se till att klienterna uppdaterar sin DNS med den uppdaterade IP-adressen för lyssnaren. Du kan se och ändra konfigurationen genom att dumpa konfigurationen av VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Ju lägre HostRecordTTL, desto högre mängd DNS-trafik.

##### <a name="client-application-settings"></a>Inställningar för klient program

Om ditt SQL-klientprogram stöder .NET 4,5 SQLClient kan du använda nyckelordet "MULTISUBNETFAILOVER = TRUE". Det här nyckelordet ska tillämpas eftersom det tillåter snabbare anslutning till SQL Always on-tillgänglighetsgrupper under redundansväxlingen. Den räknar igenom alla IP-adresser som är kopplade till Always on-lyssnare parallellt, och utför en mer aggressiv TCP-anslutning för att försöka igen under en redundansväxling.

Mer information om de tidigare inställningarna finns i [MultiSubnetFailover-nyckelord och tillhör ande funktioner](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Se även [stöd för hög tillgänglighet och katastrof återställning](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Steg 5: inställningar för klusterkvorum

När du ska ta minst en SQL Server nedåt i taget bör du ändra inställningen för klusterkvorum, om du använder fil resurs vittnet (FSW) med två noder, ska du ställa in kvorumet så att det går att använda Node majoritet och använda dynamisk röstning, så att en enskild nod förblir stående.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Mer information om hur du hanterar och konfigurerar klusterkvorum finns i [Konfigurera och hantera kvorum i ett redundanskluster för Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Steg 6: extrahera befintliga slut punkter och ACL: er

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Spara den här texten i en fil.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Steg 7: ändra Redundansrelationer och replikeringsalternativ

Om du har fler än två SQL-servrar bör du ändra redundansväxlingen för en annan sekundär i en annan DOMÄNKONTROLLANT eller lokalt till "synkron" och göra den till en automatisk växlings partner (AFP), så det är så att du kan underhålla HA medan du gör ändringar. Du kan göra detta genom TSQL ändrings-SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Steg 8: ta bort sekundär virtuell dator från moln tjänsten

Du bör planera att migrera en sekundär moln-nod först. Om den här noden för närvarande är primär, bör du starta en manuell redundansväxling.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 9: ändra inställningarna för diskcachelagring i CSV-filen och spara

För data volymer ska dessa ställas in på READONLY.

För TLOG-volymer ska dessa ställas in på ingen.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Steg 10: kopiera virtuella hård diskar

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Du kan kontrol lera kopierings statusen för de virtuella hård diskarna till Premium Storage-kontot:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Vänta tills alla dessa registreras som slutförda.

För information om enskilda blobbar:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Steg 11: registrera OS-disk

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Steg 12: importera sekundär till ny moln tjänst

I koden nedan används även det tillagda alternativet här för att importera datorn och använda den kvarhållna VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Steg 13: skapa ILB på nya Cloud SVC, Lägg till belastningsutjämnade slut punkter och ACL: er

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Steg 14: uppdatera alltid på

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Ta nu bort den gamla IP-adressen för moln tjänsten.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Steg 15: kontroll av DNS-uppdatering

Nu bör du kontrol lera DNS-servrarna i SQL Server klient nätverk och se till att kluster har lagt till den extra värd posten för den tillagda IP-adressen. Om dessa DNS-servrar inte har uppdaterats, Överväg att tvinga fram en DNS-zon överföring och se till att klienterna i ett undernät kan matcha både på IP-adresser, så du behöver inte vänta på automatisk DNS-replikering.

#### <a name="step-16-reconfigure-always-on"></a>Steg 16: konfigurera om Always on

I det här skedet väntar du på att den sekundära noden som migrerades till fullständig omsynkronisering med den lokala noden och växlar till synkron replikering-noden och gör den till en AFP.  

#### <a name="step-17-migrate-second-node"></a>Steg 17: Migrera andra noden

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 18: ändra inställningarna för diskcachelagring i CSV-filen och spara

För data volymer ska cacheinställningar anges till READONLY.

För TLOG-volymer ska cache-inställningarna anges till ingen.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Steg 19: skapa ett nytt oberoende lagrings konto för sekundär nod

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Steg 20: kopiera virtuella hård diskar

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Du kan kontrol lera VHD-kopians status för alla virtuella hård diskar:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Vänta tills alla dessa registreras som slutförda.

För information om enskilda blobbar:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Steg 21: registrera OS-disk

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Steg 22: Lägg till belastningsutjämnade slut punkter och ACL: er

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Steg 23: redundanstest

Vänta tills den migrerade noden synkroniseras med den lokala noden Always On. Placera det i synkront replikeringsläget och vänta tills det är synkroniserat. Sedan växlar du från lokalt till den första noden som migrerades, vilket är AFP. När den har fungerat, ändra den senast migrerade noden till AFP.

Du bör testa redundans mellan alla noder och köra även om kaos-tester för att säkerställa att redundansväxlingen fungerar som förväntat och i en tid som Manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Steg 24: sätt tillbaka inställningarna för klusterkvorum/DNS TTL/redundans Pntrs/Sync Settings

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Lägger till IP-adressresurs i samma undernät

Om du bara har två SQL-servrar och vill migrera dem till en ny moln tjänst, men vill behålla dem i samma undernät, kan du undvika att ta bort lyssnaren offline och ta bort den ursprungliga alltid på IP-adressen och lägga till den nya IP-adressen. Om du migrerar de virtuella datorerna till ett annat undernät behöver du inte göra detta eftersom det finns ytterligare ett kluster nätverk som refererar till det under nätet.

När du har skapat den migrerade sekundära och lagt till i den nya IP-adressresursen för den nya moln tjänsten innan du redundansväxlas den befintliga primära, bör du utföra dessa steg i klustret Redundanshanteraren:

För att lägga till i IP-adress, se tillägget, steg 14.

1. För den aktuella IP-adressresursen ändrar du den möjliga ägaren till befintlig primär SQL Server, i exemplet "dansqlams4":

    ![Appendix13][23]
2. För den nya IP-adressresursen ändrar du den möjliga ägaren till migrerad sekundär SQL Server, i exemplet "dansqlams5":

    ![Appendix14][24]
3. När den här inställningen är aktive rad kan du redundansväxla, och när den sista noden migreras, ska möjliga ägare redige ras så att noden läggs till som möjlig ägare:

    ![Appendix15][25]

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure-Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server i Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
