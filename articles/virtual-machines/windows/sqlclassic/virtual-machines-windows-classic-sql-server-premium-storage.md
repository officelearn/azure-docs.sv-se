---
title: Använda Azure Premium Storage med SQL Server | Microsoft-dokument
description: Den här artikeln använder resurser som skapats med den klassiska distributionsmodellen och ger vägledning om hur du använder Azure Premium Storage med SQL Server som körs på virtuella Azure-datorer.
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
ms.openlocfilehash: 479f9abc667e20a136da5f6231e78a1e4052f087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965676"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Använd Azure Premium Storage med SQL Server på Virtual Machines

## <a name="overview"></a>Översikt

[Azure premium SSD är](../disks-types.md) nästa generation av lagring som ger låg latens och hög dataflöde IO. Det fungerar bäst för viktiga IO-intensiva arbetsbelastningar, till exempel SQL Server på [virtuella](https://azure.microsoft.com/services/virtual-machines/)IaaS-datorer .

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln innehåller planering och vägledning för att migrera en virtuell dator som kör SQL Server för att använda Premium Storage. Detta inkluderar Azure-infrastruktur (nätverk, lagring) och gäst windows VM steg. Exemplet i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) visar en fullständig omfattande övergång från till för hur du flyttar större virtuella datorer för att dra nytta av förbättrad lokal SSD-lagring med PowerShell.

Det är viktigt att förstå processen från på nytt för att använda Azure Premium Storage med virtuella SQL Server på virtuella IAAS-datorer. Det här omfattar:

* Identifiering av förutsättningarna för att använda Premium Storage.
* Exempel på distribution av SQL Server på IaaS till Premium Storage för nya distributioner.
* Exempel på migrering av befintliga distributioner, både fristående servrar och distributioner med SQL Always On Availability Groups.
* Möjliga migrationsmetoder.
* Fullständigt end-to-end-exempel som visar Azure-, Windows- och SQL Server-steg för migreringen av en befintlig Alltid på implementering.

Mer bakgrundsinformation om SQL Server i virtuella Azure-datorer finns [i SQL Server i Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Författare:** Daniel Sol **Tekniska Granskare:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Förutsättningar för Premium-lagring

Det finns flera förutsättningar för att använda Premium Storage.

### <a name="machine-size"></a>Maskinens storlek

För att använda Premium Storage måste du använda virtuella datorer i DS-serien (VM). Om du inte har använt DS-seriedatorer i molntjänsten tidigare måste du ta bort den befintliga virtuella datorn, behålla de anslutna diskarna och sedan skapa en ny molntjänst innan du återskapar den virtuella datorn som DS*-rollstorlek. Mer information om storlekar för virtuella datorer finns i [Storlekar för virtuella datorer och molntjänster för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Molntjänster

Du kan bara använda virtuella DS*-datorer med Premium Storage när de skapas i en ny molntjänst. Om du använder SQL Server Always On i Azure refererar Alltid på lyssnaren till Azures interna eller externa belastningsutjämnings IP-adress som är associerad med en molntjänst. Den här artikeln fokuserar på hur du migrerar samtidigt som tillgängligheten bibehålls i det här scenariot.

> [!NOTE]
> En DS*-serie måste vara den första virtuella datorn som distribueras till den nya molntjänsten.
>
>

### <a name="regional-vnets"></a>Regionala virtuella nät

För virtuella datorer med DS* måste du konfigurera virtuella nätverk (VNET) som är värd för dina virtuella datorer så att det är regionalt. Detta "breddar" det virtuella nätverket är att låta de större virtuella datorerna etableras i andra kluster och tillåta kommunikation mellan dem. I följande skärmbild visar den markerade platsen regionala VNETs, medan det första resultatet visar ett "smalt" VNET.

![RegionalVNET][1]

Du kan höja en Microsoft-supportbiljett för att migrera till ett regionalt VNET.You can raise a Microsoft support ticket to migration to a regional VNET. Microsoft gör sedan en förändring. Om du vill slutföra migreringen till regionala VNETs ändrar du egenskapen AffinityGroup i nätverkskonfigurationen. Exportera först nätverkskonfigurationen i PowerShell och ersätt sedan egenskapen **AffinityGroup** i elementet **VirtualNetworkSite** med **egenskapen Location.** Ange `Location = XXXX` `XXXX` var som är en Azure-region. Importera sedan den nya konfigurationen.

Med tanke på följande VNET-konfiguration kan du till exempel överväga följande VNET-konfiguration:

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

Om du vill flytta detta till ett regionalt VNET i Västeuropa ändrar du konfigurationen till följande:
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

Du måste skapa ett nytt lagringskonto som är konfigurerat för Premium Storage. Observera att användningen av Premium Storage är inställd på lagringskontot, inte på enskilda virtuella hårddiskar, men när du använder en VM-serie i DS*-serien kan du koppla virtuella hårddiskar från Premium- och Standardlagringskonton. Du kan överväga detta om du inte vill placera OS VHD på Premium Storage-konto.

Följande kommandot **New-AzureStorageAccountPowerShell** med typen **"Premium_LRS"** skapar ett Premium Storage-konto:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Cacheinställningar för vhds

Den största skillnaden mellan att skapa diskar som ingår i ett Premium Storage-konto är diskcacheinställningen. För SQL Server Data-volymdiskar rekommenderar vi att du använder**Read Caching**. För transaktionsloggvolymer ska diskcachen anges till '**Ingen**'. Detta skiljer sig från rekommendationerna för Standard Storage-konton.

När de virtuella hårddiskarna har anslutits kan cacheinställningen inte ändras. Du måste koppla från och sätta tillbaka den virtuella hårddisken med en uppdaterad cacheinställning.

### <a name="windows-storage-spaces"></a>Windows lagringsutrymmen

Du kan använda [Windows Lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) som du gjorde med tidigare standardlagring, vilket gör att du kan migrera en virtuell dator som redan använder lagringsutrymmen. Exemplet i [tillägg](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (steg 9 och framåt) visar Powershell-koden för att extrahera och importera en virtuell dator med flera anslutna virtuella hårddiskar.

Lagringspooler användes med Standard Azure-lagringskonto för att förbättra dataflödet och minska svarstiden. Du kan hitta värde i testning av lagringspooler med Premium Storage för nya distributioner, men de lägger till ytterligare komplexitet med lagringsinställningar.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Ta reda på vilken Azure Virtual Disks-mappning till lagringspooler

Eftersom det finns olika rekommendationer för cacheinställning för kopplade virtuella hårddiskar kan du välja att kopiera de virtuella hårddiskarna till ett Premium Storage-konto. Men när du sätter tillbaka dem till den nya virtuella datorn i DS-serien kan du behöva ändra cacheinställningarna. Det är enklare att tillämpa de rekommenderade cacheinställningarna för Premium Storage när du har separata virtuella hårddiskar för SQL-datafiler och loggfiler (i stället för en enda virtuell hårddisk som innehåller båda).

> [!NOTE]
> Om du har SQL Server-data och loggfiler på samma volym beror cachelagringsalternativet du väljer på IO-åtkomstmönster för databasarbetsbelastningarna. Endast testning kan visa vilket cachelagringsalternativ som är bäst för det här scenariot.
>
>

Men om du använder Windows Storage Spaces som består av flera virtuella hårddiskar måste du titta på dina ursprungliga skript för att identifiera vilka anslutna virtuella hårddiskar som finns i vilken specifik pool, så att du sedan kan ställa in cacheinställningarna därefter för varje disk.

Om du inte har ett originalskript tillgängligt för att visa vilka virtuella hårddiskar som mappas till lagringspoolen kan du använda följande steg för att bestämma mappningen av disk-/lagringspoolen.

Gör så här för varje disk:

1. Hämta lista över diskar som är kopplade till den virtuella datorn med kommandot **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Observera DiskName och LUN.

    ![DisknamnOchLUN][2]
1. Fjärrskrivbord till den virtuella datorn. Gå sedan till **Diskenheter** | för**Enhetshanteraren** | **för**datorhantering . Titta på egenskaperna för var och en av "Microsoft Virtual Disks"

    ![VirtualDisk-egenskaper][3]
1. LUN-numret här är en referens till det LUN-nummer som du anger när den virtuella hårddisken kopplas till den virtuella datorn.
1. För "Microsoft Virtual Disk" gå till fliken **Detaljer,** sedan i **egenskapslistan,** gå till **Drivrutinsnyckel**. I **värdet**noterar du **Offset**, som är 0002 i följande skärmbild. 0002 betecknar PhysicalDisk2 som lagringspoolen refererar till.

    ![VirtualDiskPropertyDetails][4]
1. För varje lagringspool dumpar du de associerade diskarna:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nu kan du använda den här informationen för att associera kopplade virtuella hårddiskar till fysiska diskar i lagringspooler.

När du har mappat virtuella hårddiskar till fysiska diskar i lagringspooler kan du sedan koppla från och kopiera dem till ett Premium Storage-konto och sedan bifoga dem med rätt cacheinställning. Se exemplet i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), steg 8 till 12. De här stegen visar hur du extraherar en VM-ansluten VHD-diskkonfiguration till en CSV-fil, kopierar virtuella hårddiskar, ändrar diskkonfigurationscachens inställningar och slutligen distribuerar om den virtuella datorn som en VIRTUELLD-serie med alla anslutna diskar.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM-lagringsbandbredd och VHD-lagringsdataflöde

Mängden lagringsprestanda beror på den angivna DS*VM-storleken och VHD-storlekarna. De virtuella datorerna har olika utsläppsrätter för antalet virtuella hårddiskar som kan kopplas och den maximala bandbredd som de stöder (MB/s). Information om specifika bandbreddsnummer finns i [Storlekar för virtuella datorer och molntjänster för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ökad IOPS uppnås med större diskstorlekar. Du bör tänka på detta när du tänker på din migreringsväg. Mer information [finns i tabellen för IOPS och Disktyper](../disks-types.md#premium-ssd).

Slutligen anser du att virtuella datorer har olika maximala diskbandbredder som de stöder för alla anslutna diskar. Under hög belastning kan du mätta den maximala diskbandbredden som är tillgänglig för den virtuella datorns rollstorlek. Till exempel stöder en Standard_DS14 upp till 512 MB/s. Därför, med tre P30-diskar kan du mätta diskbandbredden för den virtuella datorn. Men i det här exemplet kan dataflödesgränsen överskridas beroende på blandningen av läs- och skriv-IOs.

## <a name="new-deployments"></a>Nya distributioner

De följande två avsnitten visar hur du kan distribuera virtuella SQL Server-datorer till Premium Storage. Som tidigare nämnts behöver du inte nödvändigtvis placera OS-disken på Premium-lagring. Du kan välja att göra detta om du har för avsikt att placera några intensiva IO-arbetsbelastningar på OS VHD.

Det första exemplet visar hur du använder befintliga Azure Gallery-avbildningar. Det andra exemplet visar hur du använder en anpassad VM-avbildning som du har i ett befintligt standardlagringskonto.

> [!NOTE]
> Dessa exempel förutsätter att du redan har skapat ett regionalt VNET.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Skapa en ny virtuell dator med Premium Storage med galleriavbildning

Exemplet nedan visar hur du placerar OS VHD på premiumlagring och bifogar virtuella hårddiskar för Premium Storage. Du kan dock också placera OS-disken i ett standardlagringskonto och sedan bifoga virtuella hårddiskar som finns i ett Premium Storage-konto. Båda scenarierna visas.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Steg 1: Skapa ett Premium-lagringskonto

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Steg 2: Skapa en ny molntjänst

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Steg 3: Boka en Cloud Service VIP (valfritt)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Steg 4: Skapa en VM-behållare

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Steg 5: Placera virtuell hårddisk för os på standard- eller premiumlagring

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Steg 6: Skapa virtuell dator

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
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
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

Det här scenariot visar var du har befintliga anpassade avbildningar som finns i ett Standard Storage-konto. Som nämnts om du vill placera OS VHD på Premium Storage måste du kopiera bilden som finns i standardlagringskontot och överföra dem till en Premium Storage innan den kan användas. Om du har en lokal bild kan du också använda den här metoden för att kopiera den direkt till Premium Storage-kontot.

#### <a name="step-1-create-storage-account"></a>Steg 1: Skapa lagringskonto

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Steg 2 Skapa molntjänst

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Steg 3: Använda befintlig bild

Du kan använda en befintlig avbildning. Du kan också [ta en bild av en befintlig dator](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observera att maskinen som du avbildning inte behöver vara DS*-maskin. När du har bilden visar följande steg hur du kopierar den till Premium **Storage-kontot med PowerShell-powerleten Start-AzureStorageBlobCopy.**

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Steg 4: Kopiera Blob mellan lagringskonton

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Steg 5: Kontrollera regelbundet kopieringsstatus:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Steg 6: Lägga till avbildningsdiskett i Azure-diskarkiv i Prenumeration

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Du kanske upptäcker att även om statusrapporterna är framgångsrika kan du fortfarande få ett disklånefel. I det här fallet, vänta ca 10 minuter.

#### <a name="step-7--build-the-vm"></a>Steg 7: Bygg den virtuella datorn

Här skapar du den virtuella datorn från avbildningen och bifogar två virtuella hårddiskar för Premium Storage:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Befintliga distributioner som inte använder alltid på tillgänglighetsgrupper

> [!NOTE]
> För befintliga distributioner finns först i avsnittet [Förutsättningar i](#prerequisites-for-premium-storage) den här artikeln.

Det finns olika överväganden för SQL Server-distributioner som inte använder alltid på tillgänglighetsgrupper och de som gör det. Om du inte använder Always On och har en befintlig fristående SQL Server kan du uppgradera till Premium Storage med hjälp av ett nytt molntjänst- och lagringskonto. Tänk på följande alternativ:

* **Skapa en ny VIRTUELL SQL Server**. Du kan skapa en ny virtuell SQL Server-dator som använder ett Premium Storage-konto, som dokumenterats i Nya distributioner. Säkerhetskopiera och återställ sedan SQL Server-konfigurationen och användardatabaserna. Programmet måste uppdateras för att referera till den nya SQL Server om den används internt eller externt. Du skulle behöva kopiera alla "av db" objekt som om du gjorde en Sida vid sida (SxS) SQL Server migrering. Detta inkluderar objekt som inloggningar, certifikat och länkade servrar.
* **Migrera en befintlig VIRTUELL SQL Server**. Detta kräver att du tar den virtuella datorn för SQL Server offline och sedan överför den till en ny molntjänst, vilket inkluderar att kopiera alla anslutna virtuella hårddiskar till Premium Storage-kontot. När den virtuella datorn är online refererar programmet till servervärdens namn som tidigare. Tänk på att storleken på den befintliga disken påverkar prestandaegenskaperna. En disk på 400 GB avrundas till exempel uppåt till en P20.For example, a 400 GB disk gets rounded up to a P20. Om du vet att du inte behöver diskprestanda kan du återskapa den virtuella datorn som en VIRTUELLD DS-serie och bifoga virtuella virtuella hårddiskar i Premium Storage med den storleks-/prestandaspecifikation som du behöver. Då kan du koppla loss och sätta tillbaka SQL DB-filer.

> [!NOTE]
> När du kopierar VHD-diskarna bör du vara medveten om storleken, beroende på storleken betyder vilken Premium Storage Disk-typ de tillhör, detta avgör diskprestandaspecifikationen. Azure avrundar upp till närmaste diskstorlek, så om du har en 400 GB-disk avrundas detta uppåt till en P20. Beroende på dina befintliga IO-krav för virtuell hårddisk för operativsystemet kanske du inte behöver migrera detta till ett Premium Storage-konto.

Om din SQL Server används externt ändras molntjänsten VIP. Du måste också uppdatera slutpunkter, ACL:er och DNS-inställningar.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Befintliga distributioner som använder alltid på tillgänglighetsgrupper

> [!NOTE]
> För befintliga distributioner finns först i avsnittet [Förutsättningar i](#prerequisites-for-premium-storage) den här artikeln.

Inledningsvis i det här avsnittet tittar vi på hur Always On interagerar med Azure Networking. Vi bryter sedan ner migreringar i två scenarier: migreringar där vissa driftstopp kan tolereras och migreringar där du måste uppnå minimal stilleståndstid.

Lokala SQL Server Alltid på tillgänglighetsgrupper använder en lokal lyssnare som registrerar ett virtuellt DNS-namn tillsammans med en IP-adress som delas mellan en eller flera SQL-servrar. När klienter ansluter dirigeras de via lyssnar-IP till den primära SQL Server. Det här är den server som äger alltid på IP-resursen vid den tidpunkten.

![DistributionerAnvända Alltid på][6]

I Microsoft Azure kan du bara ha en IP-adress tilldelad till ett nätverkskort på den virtuella datorn, så för att uppnå samma abstraktionslager som lokalt använder Azure IP-adressen som har tilldelats ILB/ELB (Internal/External Load Balancers). IP-resursen som delas mellan servrarna är inställd på samma IP som ILB/ELB. Detta publiceras i DNS och klienttrafik skickas via ILB/ELB till den primära SQL Server-repliken. ILB/ELB vet vilken SQL Server som är primär eftersom den använder avsökningar för att avsöka alltid på IP-resursen. I föregående exempel avsöker den varje nod som har en slutpunkt som refereras av ELB/ILB, beroende på vilket som svaren är primär SQL Server.

> [!NOTE]
> ILB och ELB tilldelas båda till en viss Azure-molntjänst, därför innebär varje molnmigrering i Azure troligen att belastningsutjämnaren IP-ändringar.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrera alltid vid distributioner som kan tillåta vissa driftstopp

Det finns två strategier för att migrera Alltid på distributioner som möjliggör vissa driftstopp:

1. **Lägga till fler sekundära repliker i ett befintligt Always On Cluster**
2. **Migrera till ett nytt Always On Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Lägga till fler sekundära repliker i ett befintligt alltid i kluster

En strategi är att lägga till fler sekundärer i gruppen Alltid på tillgänglighet. Du måste lägga till dessa i en ny molntjänst och uppdatera lyssnaren med den nya belastningsutjämnaren IP.

##### <a name="points-of-downtime"></a>Driftstopp:

* Klustervalidering.
* Testa alltid på redundans för nya sekundärer.

Om du använder Windows Storage Pools i den virtuella datorn för högre IO-dataflöde, då dessa tas offline under en fullständig klustervalidering. Valideringstestet krävs när du lägger till noder i klustret. Hur lång tid det tar att köra testet kan variera, så du bör testa detta i din representativa testmiljö för att få en ungefärlig tid på hur lång tid det tar.

Du bör etablera tid där du kan utföra manuell redundans- och kaostestning på de nyligen tillagda noderna för att säkerställa alltid funktioner med hög tillgänglighet som förväntat.

![DeploymentUseAnyways On2][7]

> [!NOTE]
> Du bör stoppa alla instanser av SQL Server där lagringspoolerna används innan valideringen körs.
>
> ##### <a name="high-level-steps"></a>Övergripande steg
>

1. Skapa två nya SQL-servrar i ny molntjänst med ansluten Premium Storage.
2. Kopiera över fullständiga säkerhetskopior och återställ med **NORECOVERY**.
3. Kopiera över "ur användaren DB" beroende objekt, såsom inloggningar etc.
4. Skapa ny en ny intern belastningsutjämnare (ILB) eller använd en extern belastningsutjämnare (ELB) och ställ sedan in belastningsbalanserade slutpunkter på båda de nya noderna.

   > [!NOTE]
   > Kontrollera att alla noder har rätt slutpunktskonfiguration innan du fortsätter
   >
   >
5. Stoppa användar-/programåtkomst till SQL Server (om du använder lagringspooler).
6. Stoppa SQL Server Engine Services på alla noder (om du använder lagringspooler).
7. Lägg till nya noder i klustret och kör fullständig validering.
8. Starta alla SQL Server Services när valideringen har slutförts.
9. Säkerhetskopierar transaktionsloggar och återställer användardatabaser.
10. Lägg till nya noder i gruppen Alltid på tillgänglighet och placera replikering i **Synkron**.
11. Lägg till IP-adressresursen för den nya molntjänsten ILB/ELB via PowerShell för alltid på baserat på exemplet med flera webbplatser i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). I Windows-kluster anger du **de möjliga ägarna** till **IP-adressresursen** till de nya noderna gamla. Se avsnittet "Lägga till IP-adressresurs i samma undernät" i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Redundans till en av de nya noderna.
13. Gör de nya noderna Auto Redundanspartner och testa redundans.
14. Ta bort ursprungliga noder från tillgänglighetsgruppen.

##### <a name="advantages"></a>Fördelar

* Nya SQL-servrar kan testas (SQL Server och Application) innan de läggs till i Always On.
* Du kan ändra den virtuella datorns storlek och anpassa lagringen efter dina exakta behov. Det skulle dock vara fördelaktigt att hålla alla SQL-filsökvägar samma.
* Du kan styra när överföringen av DB-säkerhetskopior till sekundära repliker startas. Detta skiljer sig från att använda Azure **Start-AzureStorageBlobCopy** commandlet för att kopiera virtuella hårddiskar, eftersom det är en asynkron kopia.

##### <a name="disadvantages"></a>Nackdelar

* När du använder Windows Storage Pools finns det klusterstopp under fullständig klustervalidering för de nya ytterligare noderna.
* Beroende på SQL Server-versionen och det befintliga antalet sekundära repliker kanske du inte kan lägga till fler sekundära repliker utan att ta bort befintliga sekundärfiler.
* Det kan finnas lång SQL-dataöverföringstid när du ställer in sekundärerna.
* Det tillkommer extra kostnader under migreringen medan du har nya maskiner som körs parallellt.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrera till ett nytt Always On Cluster

En annan strategi är att skapa en helt ny Always On Cluster med helt nya noder i ny molntjänst och sedan omdirigera klienterna att använda den.

##### <a name="points-of-downtime"></a>Driftpunkter

Det finns driftstopp när du överför program och användare till den nya Alltid på lyssnaren. Driftstopp beror på:

* Den tid det tar att återställa slutförda transaktionsloggsäkerheter till databaser på nya servrar.
* Det tar att uppdatera klientprogram för att använda nya Always On lyssnare.

##### <a name="advantages"></a>Fördelar

* Du kan testa den faktiska produktionsmiljön, SQL Server och OS-byggändringar.
* Du har möjlighet att anpassa lagringen och eventuellt minska storleken på den virtuella datorn. Detta kan leda till kostnadsminskningar.
* Du kan uppdatera din SQL Server-version under den här processen. Du kan också uppgradera operativsystemet.
* Det tidigare Alltid på klustret kan fungera som ett stabilt återställningsmål.

##### <a name="disadvantages"></a>Nackdelar

* Du måste ändra DNS-namnet på lyssnaren om du vill att båda Alltid på kluster som körs samtidigt. Detta lägger till administrationskostnader under migreringen som klientprogramsträngar måste återspegla det nya lyssnarnamnet.
* Du måste implementera en synkroniseringsmekanism mellan de två miljöerna för att hålla dem så nära som möjligt för att minimera de slutliga synkroniseringskraven före migreringen.
* Det tillkommer kostnader under migreringen medan du har den nya miljön igång.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrera alltid vid distributioner för minimal stilleståndstid

Det finns två strategier för att migrera Alltid på distributioner för minimal stilleståndstid:

1. **Använda en befintlig sekundär: En webbplats**
2. **Utnyttja befintliga sekundära repliker: Flera webbplatser**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utnyttja en befintlig sekundär: Single-Site

En strategi för minimal stilleståndstid är att ta ett befintligt moln sekundärt och ta bort den från den aktuella molntjänsten. Kopiera sedan de virtuella hårddiskarna till det nya Premium Storage-kontot och skapa den virtuella datorn i den nya molntjänsten. Uppdatera sedan lyssnaren i klustring och redundans.

##### <a name="points-of-downtime"></a>Driftpunkter

* Det finns driftstopp när du uppdaterar den sista noden med slutpunkten Belastningsutjämning.
* Klientens återanslutning kan fördröjas beroende på klient-DNS-konfigurationen.
* Det finns ytterligare driftstopp om du väljer att ta gruppen Alltid på kluster offline för att byta ut IP-adresserna. Du kan undvika detta genom att använda ett ELLER-beroende och möjliga ägare för den tillagda IP-adressresursen. Se avsnittet "Lägga till IP-adressresurs i samma undernät" i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> När du vill att den tillagda noden ska delta som en Alltid på redundanspartner måste du lägga till en Azure-slutpunkt med en referens till belastningsbalanserad uppsättning. När du kör kommandot **Add-AzureEndpoint** för att göra detta kan aktuella anslutningar vara öppna, men nya anslutningar till lyssnaren kan inte upprättas förrän belastningsutjämnaren har uppdaterats. Vid testning detta sågs pågå 90-120seconds, detta bör testas.

##### <a name="advantages"></a>Fördelar

* Inga extra kostnader som uppstår under migreringen.
* En 1:1-migrering.
* Minskad komplexitet.
* Möjliggör ökad IOPS från Premium Storage SKU: er. När diskarna tas bort från den virtuella datorn och kopieras till den nya molntjänsten kan ett 3:e parts verktyg användas för att öka VHD-storleken, vilket ger högre dataflöde. För att öka VHD storlekar, se detta [forum diskussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nackdelar

* Det finns en tillfällig förlust av HA och DR under migreringen.
* Eftersom det här är en 1:1-migrering måste du använda en minsta vm-storlek som stöder antalet virtuella hårddiskar, så du kanske inte kan minska storleken på dina virtuella datorer.
* Det här scenariot skulle använda Azure **Start-AzureStorageBlobCopy** commandlet, som är asynkron. Det finns inget serviceavtal när kopian är klar. Tiden för kopiorna varierar, medan detta beror på vänta i kö det beror också på mängden data som ska överföras. Kopieringstiden ökar om överföringen går till ett annat Azure-datacenter som stöder Premium Storage i en annan region. Om du bara har 2 noder, överväga en möjlig begränsning om kopian tar längre tid än i testning. Detta skulle kunna omfatta följande idéer.
  * Lägg till en tillfällig 3:e SQL Server-nod för HA före migreringen med överenskomna driftstopp.
  * Kör migreringen utanför Azures schemalagda underhåll.
  * Kontrollera att du har konfigurerat klustrets kvorum korrekt.  

##### <a name="high-level-steps"></a>Övergripande steg

Det här dokumentet visar inte ett fullständigt exempel från till, men [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) innehåller information som kan utnyttjas för att utföra detta.

![Minimaldowntime][8]

* Samla in diskkonfiguration och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett Premium Storage-konto och kopiera virtuella hårddiskar från standardlagringskontot
* Skapa ny molntjänst och distribuera om SQL2-virtuella datorn i den molntjänsten. Skapa den virtuella datorn med den kopierade ursprungliga os-hårddisken och bifoga de kopierade virtuella hårddiskarna.
* Konfigurera ILB / ELB och lägg till slutpunkter.
* Uppdatera Lyssnaren av antingen:
  * Med alltid på grupp offline och uppdatera Alltid på lyssnaren med nya ILB / ELB IP-adress.
  * Eller lägga till IP-adressresursen för nya Molntjänsten ILB/ELB via PowerShell i Windows-klustring. Ange sedan de möjliga ägarna för IP-adressresursen till den migrerade noden, SQL2, och ange detta som ELLER-beroende i nätverksnamnet. Se avsnittet "Lägga till IP-adressresurs i samma undernät" i [tillägget](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Kontrollera DNS-konfigurationen/spridningen till klienterna.
* Migrera SQL1 VM och gå igenom steg 2 – 4.
* Om du använder steg 5ii lägger du till SQL1 som en möjlig ägare för den tillagda IP-adressresursen
* Testa redundans.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utnyttja befintliga sekundära repliker: Multi-Site

Om du har noder i mer än ett Azure datacenter (DC) eller om du har en hybridmiljö kan du använda en Alltid på-konfiguration i den här miljön för att minimera driftstopp.

Metoden är att ändra synkroniseringen Alltid vid synkronisering till synkron för den lokala eller sekundära Azure DC och sedan växla över till den SQL Server. Kopiera sedan de virtuella hårddiskarna till ett Premium Storage-konto och distribuera om datorn till en ny molntjänst. Uppdatera lyssnaren och växla sedan tillbaka.

##### <a name="points-of-downtime"></a>Driftpunkter

Driftstoppet består av tiden för att växla över till den alternativa DC och tillbaka. Det beror också på din klient/DNS-konfiguration och klientåteranslutningen kan fördröjas.
Tänk på följande exempel på en hybrid always on-konfiguration:

![MultiSite1][9]

##### <a name="advantages"></a>Fördelar

* Du kan använda befintlig infrastruktur.
* Du har möjlighet att föruppgradera Azure-lagringen på DR Azure DC först.
* DR Azure DC-lagringen kan konfigureras om.
* Det finns minst två redundans under migreringen, exklusive testundansväxlingar.
* Du behöver inte flytta SQL Server-data med säkerhetskopiering och återställning.

##### <a name="disadvantages"></a>Nackdelar

* Beroende på klientåtkomst till SQL Server kan det finnas en ökad svarstid när SQL Server körs i en alternativ DOMÄNKONTROLLANT till programmet.
* Kopieringstiden för virtuella hårddiskar till Premium-lagring kan vara lång. Detta kan påverka ditt beslut om du vill behålla noden i tillgänglighetsgruppen. Tänk på detta när loggintensiva arbetsbelastningar körs under migreringen krävs, eftersom den primära noden måste behålla de oreglerade transaktionerna i transaktionsloggen. Därför kan detta växa betydligt.
* Det här scenariot skulle använda Azure **Start-AzureStorageBlobCopy** commandlet, som är asynkron. Det finns inget serviceavtal när det är klart. Tiden för kopiorna varierar, medan detta beror på vänta i kö, beror det också på mängden data som ska överföras. Därför har du bara en nod i ditt 2: a datacenter, bör du vidta åtgärder för att mildra om kopian tar längre tid än att testa. Dessa begränsningssteg innehåller följande idéer:
  * Lägg till en tillfällig 2:a SQL-nod för HA före migreringen med överenskomna driftstopp.
  * Kör migreringen utanför Azures schemalagda underhåll.
  * Kontrollera att du har konfigurerat klustrets kvorum korrekt.

Det här scenariot förutsätter att du har dokumenterat installationen och vet hur lagringen mappas för att göra ändringar för optimala diskcacheinställningar.

##### <a name="high-level-steps"></a>Övergripande steg

![Multisite2][10]

* Gör den lokala/alternativa Azure DC till SQL Server Primary och gör den till den andra automatiska redundanspartnern (AFP).
* Samla in diskkonfigurationsinformation från SQL2 och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett Premium Storage-konto och kopiera virtuella hårddiskar från standardlagringskontot.
* Skapa en ny molntjänst och skapa DEN VIRTUELLA SQL2-datorn med sina Premiums Storage-diskar anslutna.
* Konfigurera ILB / ELB och lägg till slutpunkter.
* Uppdatera alltid lyssnaren med ny ILB / ELB IP-adress och testa redundans.
* Kontrollera DNS-konfigurationen.
* Ändra AFP till SQL2 och migrera sedan SQL1 och gå igenom steg 2 – 5.
* Testa redundans.
* Växla tillbaka AFP till SQL1 och SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Tillägg: Migrera en multisite alltid på kluster till Premium Storage

Resten av den här artikeln innehåller ett detaljerat exempel på hur du konverterar ett multi-site Always On-kluster till Premium-lagring. Den konverterar också lyssnaren från att använda en extern belastningsutjämnare (ELB) till en intern belastningsutjämnare (ILB).

### <a name="environment"></a>Miljö

* Windows 2k12 / SQL 2k12 Windows 2k12 Windows 2k12 Windows 2k12 / SQL 2k12 Windows 2
* 1 DB-filer på SP
* 2 x lagringspooler per nod

![Tillägg1][11]

### <a name="vm"></a>VM:

I det här exemplet kommer vi att visa att vi går från en ELB till ILB. ELB var tillgängligt före ILB, så detta visar hur du byter till ILB under migreringen.

![Tillägg2][12]

### <a name="pre-steps-connect-to-subscription"></a>Försteg: Anslut till prenumeration

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Steg 1: Skapa nytt lagringskonto och molntjänst

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Steg 2: Öka tillåtna \<fel på valfria resurser>

På vissa resurser som tillhör gruppen Alltid på tillgänglighet finns det gränser för hur många fel som kan uppstå under en period, där klustertjänsten försöker starta om resursgruppen. Vi rekommenderar att du ökar detta medan du går igenom den här proceduren, eftersom om du inte manuellt redundans och utlöser redundans genom att stänga av maskiner kan du komma nära den här gränsen.

Det vore klokt att fördubbla felersättningen, för att göra detta i Redundansklusterhanteraren, gå till egenskaperna för resursgruppen Alltid på:

![Tillägg3][13]

Ändra maximala fel till 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Steg 3: Addition IP-adressresurs för klustergrupp \<valfri>

Om du bara har en IP-adress för klustergruppen och detta är anpassat till molnundernätet, se upp, om du av misstag tar alla klusternoder i molnet i nätverket offline kan inte kluster-IP-resursen och klusternätverksnamnet anslutas. I det här fallet förhindrar det uppdateringar till andra klusterresurser.

#### <a name="step-4-dns-configuration"></a>Steg 4: DNS-konfiguration

Implementera en smidig övergång beror på hur DNS används och uppdateras.
När Always On är installerat skapas en Windows-klusterresursgrupp, om du öppnar Redundansklusterhanteraren ser du att den åtminstone har tre resurser, de två som dokumentet refererar till är:

* VNN (Virtual Network Name) – DET DNS-namn som klienter ansluter till när de vill ansluta till SQL-servrar via Always On.
* IP-adressresurs – IP-adressen som är associerad med VNN, kan du ha mer än en, och i en konfiguration på flera webbplatser har du en IP-adress per plats/undernät.

När sql server-klientdrivrutinen ansluter till SQL Server hämtas DNS-posterna som är associerade med lyssnaren och försöker ansluta till varje Alltid på associerad IP-adress. Därefter diskuterar vi några faktorer som kan påverka detta.

Antalet samtidiga DNS-poster som är associerade med lyssnarnamnet beror inte bara på antalet ASSOCIERADE IP-adresser, utan inställningen RegisterAllIpProviders i Redundanskluster för resursen Alltid PÅ VNN.

När du distribuerar Alltid på i Azure finns det olika steg för att skapa lyssnaren och IP-adresserna, du måste manuellt konfigurera "RegisterAllIpProviders" till 1, detta skiljer sig från en lokal Alltid på distribution där den redan är inställd på 1.

Om "RegisterAllIpProviders" är 0 ser du bara en DNS-post i DNS som är associerad med lyssnaren:

![Tillägg4][14]

Om "RegisterAllIpProviders" är 1:

![Tillägg5][15]

Koden nedan dumpar ut VNN-inställningarna och ställer in den åt dig. För att ändringen ska börja gälla måste du ta VNN offline och slå på den igen. Detta tar lyssnaren offline orsakar störningar klientanslutning.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

I ett senare migreringssteg måste du uppdatera alltid på lyssnaren med en uppdaterad IP-adress som refererar till en belastningsutjämnare, vilket innebär borttagning och tillägg av IP-adressresurser. Efter IP-uppdateringen måste du se till att den nya IP-adressen har uppdaterats i DNS-zonen och att klienterna uppdaterar sin lokala DNS-cache.

Om klienterna finns i ett annat nätverkssegment och refererar till en annan DNS-server måste du överväga vad som händer med DNS Zone Transfer under migreringen, eftersom programmets återanslutningstid begränsas av minst zonöverföringstiden för en ny IP-adress adresser till lyssnaren. Om du är under tidsbegränsning här bör du diskutera och testa att tvinga fram en inkrementell zonöverföring med dina Windows-team, och även placera DNS-värdposten till en lägre Time To Live (TTL), så att klienterna uppdateras. Mer information finns i [Inkrementella zonöverföringar](https://technet.microsoft.com/library/cc958973.aspx) och [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Som standard är TTL för DNS-post som är associerad med lyssnaren i Always On i Azure 1200 sekunder. Du kanske vill minska detta om du är under tidsbegränsning under migreringen för att säkerställa att klienterna uppdaterar sin DNS med den uppdaterade IP-adressen för lyssnaren. Du kan se och ändra konfigurationen genom att dumpa konfigurationen av VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Ju lägre "HostRecordTTL", en högre mängd DNS-trafik inträffar.

##### <a name="client-application-settings"></a>Inställningar för klientprogram

Om SQL-klientprogrammet stöder SQLClient .NET 4.5 kan du använda nyckelordet MULTISUBNETFAILOVER=TRUE. Det här nyckelordet bör användas, eftersom det möjliggör snabbare anslutning till SQL Always On Availability Group under redundans. Den räknar upp genom alla IP-adresser som är associerade med Alltid på lyssnaren parallellt och utför en mer aggressiv hastighet för återförsök i TCP-anslutning under en redundansväxling.

Mer information om tidigare inställningar finns i [MultiSubnetFailover Keyword och Associated Features](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Se även [SqlClient-stöd för hög tillgänglighet, haveriberedskap](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Steg 5: Inställningar för klusterkvorum

Eftersom du kommer att ta ut minst en SQL Server ner i taget, bör du ändra klustret kvorum inställningen, om du använder File Share Witness (FSW) med två noder, bör du ställa in kvorum för att tillåta nod majoritet och använda dynamisk röstning , vilket gör det möjligt för en enda nod att förbli stående.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Mer information om hur du hanterar och konfigurerar klusterkvorumet finns [i Konfigurera och hantera kvorumet i ett Redundanskluster för Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Steg 6: Extrahera befintliga slutpunkter och åtkomstpunkter

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Spara den här texten i en fil.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Steg 7: Ändra redundanspartner och replikeringslägen

Om du har fler än två SQL-servrar bör du ändra redundansen för en annan sekundär i en annan domänkontrollant eller lokal till "Synkron" och göra den till en automatisk redundanspartner (AFP), det här är så att du behåller HA medan du gör ändringar. Du kan göra detta genom TSQL av ändra om SSMS:

![Tillägg6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Steg 8: Ta bort sekundär virtuell dator från molntjänsten

Du bör planera att migrera en sekundär molnnod först. Om den här noden för närvarande är primär bör du initiera en manuell redundans.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 9: Ändra inställningar för cachelagring av disk i CSV-filen och spara

För datavolymer bör dessa ställas in på READONLY.

För TLOG-volymer bör dessa ställas in på NONE.

![Tillägg7][17]

#### <a name="step-10-copy-vhds"></a>Steg 10: Kopiera VIRTUELLA HÅRDDISKAR

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


Du kan kontrollera kopieringsstatusen för de virtuella hårddiskarna till Premium Storage-kontot:

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

![Tillägg8][18]

Vänta tills alla dessa registreras som framgång.

För information för enskilda blobbar:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Steg 11: Registrera OS-disk

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Steg 12: Importera sekundärt till ny molntjänst

Koden nedan använder också det tillagda alternativet här kan du importera maskinen och använda det behållbara VIP.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Steg 13: Skapa ILB på nya molnstäckor, lägg till belastningsbalanserade slutpunkter och ACL:er

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

#### <a name="step-14-update-always-on"></a>Steg 14: Uppdatera alltid på

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

![Tillägg9][19]

Ta nu bort den gamla IP-adressen för molntjänsten.

![Tillägg10][20]

#### <a name="step-15-dns-update-check"></a>Steg 15: DNS-uppdateringskontroll

Du bör nu kontrollera DNS-servrar i SQL Server-klientnätverken och se till att klustring har lagt till den extra värdposten för den tillagda IP-adressen. Om dessa DNS-servrar inte har uppdaterats kan du överväga att tvinga fram en DNS-zonöverföring och se till att klienterna i det undernätet kan matcha till båda alltid på IP-adresser, så du behöver inte vänta på automatisk DNS-replikering.

#### <a name="step-16-reconfigure-always-on"></a>Steg 16: Konfigurera alltid på

Nu väntar du på den sekundära noden som migrerades till att synkroniseras helt med den lokala noden och växla till synkron replikeringsnod och göra den till AFP.  

#### <a name="step-17-migrate-second-node"></a>Steg 17: Migrera den andra noden

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 18: Ändra inställningar för cachelagring av disk i CSV-filen och spara

För datavolymer bör cacheinställningarna ställas in på READONLY.

För TLOG-volymer ska cacheinställningarna ställas in på NONE.

![Tillägg11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Steg 19: Skapa nytt oberoende lagringskonto för sekundär nod

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

#### <a name="step-20-copy-vhds"></a>Steg 20: Kopiera VIRTUELLA HÅRDDISKAR

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

Du kan kontrollera vhd-kopieringsstatus för alla virtuella hårddiskar:

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

![Tillägg12][22]

Vänta tills alla dessa registreras som framgång.

För information för enskilda blobbar:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Steg 21: Registrera OS-disk

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Steg 22: Lägga till belastningsbalanserade slutpunkter och ACL:er

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

#### <a name="step-23-test-failover"></a>Steg 23: Testa redundans

Vänta tills den migrerade noden synkroniseras med den lokala alltid på noden. Placera den i synkront replikeringsläge och vänta tills den är synkroniserad. Sedan redundans från lokalt till den första noden migreras, som är AFP. När det har fungerat ändrar du den senast migrerade noden till AFP.

Du bör testa redundans mellan alla noder och köra om kaos tester för att säkerställa redundans fungerar som förväntat och i en tid herrgård.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Steg 24: Sätt tillbaka klusterkvoruminställningar / DNS TTL / Redundans Pntrs / Sync Settings

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Lägga till IP-adressresurs i samma undernät

Om du bara har två SQL-servrar och vill migrera dem till en ny molntjänst, men vill behålla dem i samma undernät, kan du undvika att ta lyssnaren offline för att ta bort den ursprungliga alltid på IP-adressen och lägga till den nya IP-adressen. Om du migrerar de virtuella datorerna till ett annat undernät behöver du inte göra detta eftersom det finns ytterligare ett klusternätverk som refererar till det undernätet.

När du har tagit upp den migrerade sekundära och lagt till i den nya IP-adressresursen för den nya molntjänsten före redundans den befintliga primärtjänsten, bör du vidta följande åtgärder i Cluster Failover Manager:

För att lägga till i IP-adress, se tillägget, steg 14.

1. För den aktuella IP-adressresursen ändrar du den möjliga ägaren till Befintlig primär SQL-server i exemplet "dansqlams4":

    ![Tillägg13][23]
2. För den nya IP-adressresursen ändrar du den möjliga ägaren till "Migrerad sekundär SQL Server", i exemplet "dansqlams5":

    ![Tillägg14][24]
3. När detta är inställt kan du redundans, och när den sista noden migreras ska möjliga ägare redigeras så att noden läggs till som en möjlig ägare:

    ![Tillägg15][25]

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Premium-lagring](../disks-types.md)
* [Virtuella datorer](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server i virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
