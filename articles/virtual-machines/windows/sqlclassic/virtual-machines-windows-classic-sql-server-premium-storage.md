---
title: Använd Azure Premium Storage med SQLServer | Microsoft Docs
description: Den här artikeln använder resurser som har skapats med den klassiska distributionsmodellen och ger vägledning om hur du använder Azure Premium Storage med SQL Server som körs på Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b9a668a71b0fb7b2bb57f759cc54a8d1930a0f03
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329072"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Använd Azure Premium Storage med SQL Server på virtuella datorer

## <a name="overview"></a>Översikt

[Azure premium SSD](../disks-types.md) är nästa generation av lagring som ger mindre fördröjning och högt dataflöde i/o. Det fungerar bäst för viktiga i/o-intensiva arbetsbelastningar, till exempel SQL Server på IaaS [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln innehåller planering och vägledning för att migrera en virtuell dator med SQL Server att använda Premium Storage. Detta omfattar Azure-infrastrukturen (nätverk, lagring) och Gäst Windows VM steg. I exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) visar en fullständig omfattande från slutpunkt till slutpunkt-migrering av hur du flyttar större virtuella datorer för att dra nytta av förbättrad lokal SSD-lagring med PowerShell.

Det är viktigt att förstå hur slutpunkt till slutpunkt genom att använda Azure Premium Storage med SQL Server på virtuella IAAS-datorer. Det här omfattar:

* Identifiering av förutsättningar för att använda Premium Storage.
* Exempel på distribution av SQL Server på IaaS till Premium Storage för nya distributioner.
* Exempel på migrera befintliga distributioner, både fristående servrar och distributioner med SQL Always On-Tillgänglighetsgrupper.
* Möjliga Migreringstyp.
* Fullständig från slutpunkt till slutpunkt-exempel som visar steg för Azure, Windows och SQL Server för migrering av en befintlig Always On-implementering.

Mer bakgrundsinformation om SQL Server i Azure virtuella datorer, se [SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Skapad av:** Daniel Sol **Teknisk granskare:** Luis Carlos Vargas sill, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Pettersson.

## <a name="prerequisites-for-premium-storage"></a>Krav för Premium Storage

Det finns flera förutsättningar för att använda Premium Storage.

### <a name="machine-size"></a>Storleken på datorn

Du måste använda DS-serien virtuella datorer (VM) för att använda Premium Storage. Om du inte har använt datorer i DS-serien i din molntjänst innan måste du ta bort den befintliga virtuella datorn, hålla anslutna diskar och sedan skapa en ny molntjänst innan du återskapa den virtuella datorn som DS * rollstorlek. Mer information om storlekar för virtuella datorer finns i [virtuell dator och Molntjänststorlekar för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Molntjänster

Du kan bara använda DS * virtuella datorer med Premium Storage när de skapas i en ny molntjänst. Om du använder SQL Server Always On i Azure refererar alltid på lyssnaren till Azure interna eller externa IP-Load Balancer-adressen som är associerad med en tjänst i molnet. Den här artikeln handlar om hur du migrerar samtidigt som tillgängligheten i det här scenariot.

> [!NOTE]
> En serie med DS * måste vara den första virtuella dator som har distribuerats till den nya Molntjänsten.
>
>

### <a name="regional-vnets"></a>Regional VNETS

Du måste konfigurera det virtuella nätverk (VNET) som är värd för dina virtuella datorer för att vara regionala för DS * virtuella datorer. Detta ”utökar” det virtuella nätverket är att tillåta de största virtuella datorerna som etableras i andra kluster och att kommunikationen mellan dem. I följande skärmbild visar den markerade platsen regionala virtuella nätverk, medan det första resultatet visar ett ”smal” virtuellt nätverk.

![RegionalVNET][1]

Du kan öka ett Microsoft supportärende om du vill migrera till ett regionalt virtuellt nätverk. Microsoft sedan gör en ändring. Ändra egenskapen AffinityGroup i nätverkskonfigurationen för att slutföra migreringen till regionala virtuella nätverk. Först exportera nätverkskonfigurationen i PowerShell och Ersätt sedan den **AffinityGroup** -egenskapen i den **VirtualNetworkSite** element med ett **plats** egenskapen. Ange `Location = XXXX` där `XXXX` är en Azure-region. Sedan importera den nya konfigurationen.

Till exempel överväga följande konfiguration av virtuellt nätverk:

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

Om du vill flytta det till ett regionalt virtuellt nätverk i västra Europa, ändrar du konfigurationen av följande:
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

Du behöver skapa ett nytt lagringskonto som har konfigurerats för Premium Storage. Observera att användningen av Premium Storage är inställd på storage-konto, inte på enskilda virtuella hårddiskar, men när du använder en DS * serien virtuell dator kan du bifoga VHD-filer från Premium och Standard Storage-konton. Du kan du överväga om du inte vill placera OS-VHD till Premium Storage-kontot.

Följande **New-AzureStorageAccountPowerShell** med ”Premium_LRS” **typ** skapar ett Premium Storage-konto:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHDs Cache Settings

Den största skillnaden mellan att skapa diskar som ingår i ett Premium Storage-konto är disk cache-inställningen. För SQL Server-datavolym diskar det rekommenderas att du använder ”**Läs cachelagring**'. Om transaktionen loggvolymerna, cacheinställning disk ska vara inställt på ”**ingen**'. Detta skiljer sig från rekommendationerna för Standard Storage-konton.

Cache-inställningen kan inte ändras när de virtuella hårddiskarna har bifogats. Du skulle behöva frånkoppla eller återansluta den virtuella Hårddisken med en uppdaterad cache-inställningen.

### <a name="windows-storage-spaces"></a>Lagringsutrymmen för Windows

Du kan använda [Windows lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) som du gjorde med föregående standardlagring sätt kan du migrera en virtuell dator som redan använder lagringsutrymmen. I exemplet i [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (steg 9 och framåt) visar Powershell-kod för att extrahera och importera en virtuell dator med flera anslutna virtuella hårddiskar.

Lagringspooler har använts med Standard Azure storage-konto för att förbättra dataflödet och minska svarstiden. Du kan hitta värdet i testning lagringspooler med Premium Storage för nya distributioner, men de lägger till ytterligare komplexitet med Lagringsinställningar.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Så här hittar du vilken Azure virtuella diskar mappas till lagringspooler

Eftersom det finns olika cache inställningen rekommendationer för anslutna virtuella hårddiskar, kan du välja att kopiera de virtuella hårddiskarna till ett Premium Storage-konto. När du återansluta dem till den nya DS-serien virtuella datorer kan du dock behöva alter cacheinställningarna. Det är enklare att tillämpa Premiumlagring rekommenderade cacheinställningar för när du har separata virtuella hårddiskar för SQL-datafiler och loggfiler (snarare än en enda virtuell Hårddisk som innehåller både).

> [!NOTE]
> Om du har SQL Server-data och loggfiler lagras på samma volym beror cachelagring alternativ du väljer på i/o-åtkomstmönster för arbetsbelastningen i din databas. Endast testning kan du visa vilket alternativ för cachelagring är bäst för det här scenariot.
>
>

Om du använder Windows-lagringsutrymmen som består av flera virtuella hårddiskar som du behöver titta på din ursprungliga skript för att identifiera vilket anslutna virtuella hårddiskar som i vilken specifik pool, så du sedan kan ange inställningar för cachelagring i enlighet med detta för varje disk.

Du kan använda följande steg för att fastställa mappningen disklagring/pool om du inte har ursprungliga skriptet som är tillgängliga för att visa dig som virtuella hårddiskar som mappar till lagringspoolen.

För varje disk, använder du följande steg:

1. Hämta listan över diskar som är kopplad till virtuell dator med den **Get-AzureVM** kommando:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

2. Observera Diskname och LUN.

    ![DisknameAndLUN][2]
3. Fjärrskrivbord till den virtuella datorn. Gå till **Datorhantering** | **Enhetshanteraren** | **diskenheter**. Titta på egenskaperna för var och en av de ”Microsoft virtuella diskar”

    ![VirtualDiskProperties][3]
4. LUN-numret här är en referens till LUN-numret som du anger när du ansluter den virtuella Hårddisken till den virtuella datorn.
5. För den ”Microsoft Virtual Disk” gå till den **information** fliken i den **egenskapen** listan, gå till **drivrutinen nyckeln**. I den **värdet**, Obs den **förskjutning**, vilket är 0002 i följande skärmbild. 0002 anger PhysicalDisk2 som refererar till lagringspoolen.

    ![VirtualDiskPropertyDetails][4]
6. Dumpa ut de associera diskarna för varje lagringspool:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nu kan du använda anslutna den här informationen för att associera virtuella hårddiskar till fysiska diskar i lagringspooler.

När du har mappat virtuella hårddiskar till fysiska diskar i lagringspooler som du kan koppla bort och kopiera dem till ett Premium Storage-konto kan du sedan koppla dem med rätt cache-inställningen. Se exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), steg 8 till och med 12. De här stegen visar hur du extrahera diskkonfiguration VM-ansluten virtuell Hårddisk till en CSV-fil, kopiera virtuella hårddiskar, ändra inställningar för diskkonfiguration cacheminnet och slutligen distribuera om den virtuella datorn som en serie DS VM med alla anslutna diskar.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM-lagring bandbredd och genomflödet för virtuell Hårddisk

Mängden lagringsprestanda beror på den angivna DS * VM-storleken och VHD-storlekar. De virtuella datorerna har olika tilldelningar för antalet virtuella hårddiskar som kan anslutas och maximal bandbredd som de stöder (MB/s). Specifik bandbredd siffror, se [virtuell dator och Molntjänststorlekar för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ökad IOPS uppnås med större diskstorlekar. Du bör överväga när du funderar sökvägen för migreringen. Mer information [finns i tabellen för IOPS och disktyper](../disks-types.md#premium-ssd).

Slutligen bör du att virtuella datorer har olika maximala värdet för disk-bandbredder som de har stöd för alla diskar som är anslutna. Under hög belastning kan du fylla maximala värdet för diskbandbredden som är tillgängliga för den storleken. Till exempel stöder en Standard_DS14 upp till 512 MB/s. Du kan därför fylla diskbandbredden som för den virtuella datorn med tre P30-diskar. Men i det här exemplet kan dataflöde gränserna överskrids beroende på vilken blandning av läsning och skrivning IOs.

## <a name="new-deployments"></a>Nya distributioner

I följande två avsnitt visar hur du kan distribuera SQL Server-datorer till Premium Storage. Som tidigare nämnts, behöver du inte nödvändigtvis att placera OS-disken till Premium storage. Du kan välja att göra detta om du avsikten är att placera alla i/o-intensiva arbetsbelastningar på OS-VHD.

Det första exemplet visar med avbildningar av befintliga Azure-galleriet. Det andra exemplet visar hur du använder en anpassad virtuell datoravbildning som du har i ett befintligt lagringskonto som Standard.

> [!NOTE]
> Dessa exempel förutsätter att du redan har skapat ett regionalt VNET.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Skapa en ny virtuell dator med Premium Storage med Galleriavbildningen

Exemplet nedan visar hur du placera OS-VHD till premiumlagring och kopplar VHD: er för Premium-lagring. Du kan dock också placera OS-disken i ett Standard Storage-konto och sedan koppla virtuella hårddiskar som finns i ett Premium Storage-konto. Båda scenarier.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Steg 1: Skapa ett Premium Storage-konto

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

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Steg 3: Reservera en Cloud Service VIP (valfritt)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
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

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Steg 5: Placera OS-VHD på Standard- eller Premium-lagring

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Steg 6: Skapa en virtuell dator

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

#create new Avaiability Set
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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Skapa en ny virtuell dator om du vill använda Premium Storage med en anpassad bild

Det här scenariot visar där du har befintliga anpassade avbildningar som finns i ett standardlagringskonto. Som vi redan nämnt om du vill placera OS-VHD på Premium-lagring måste du kopiera den avbildning som finns i standardlagringskontot och överför dem till ett Premium Storage innan den kan användas. Om du har en bild på plats, kan du också använda den här metoden för att kopiera som direkt till Premium Storage-kontot.

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

#### <a name="step-3-use-existing-image"></a>Steg 3: Använd befintlig avbildning

Du kan använda en befintlig avbildning. Du kan också [ta en bild av en befintlig dator](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tänk på den dator som du avbildningen inte behöver vara DS * dator. När du har avbildningen kan följande steg visar hur du kopierar den till Premium Storage-kontot med den **Start AzureStorageBlobCopy** PowerShell-kommandot.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Steg 4: Kopiering av Blob mellan Lagringskonton

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Steg 5: Kontrollera regelbundet kopieringsstatusen:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Steg 6: Lägg till bilddisk i Azure-disk lagringsplatsen i prenumerationen

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Du kanske att även om den statusrapporter om åtgärden lyckades, du kan fortfarande få ett lån diskfel. I så fall väntar du cirka 10 minuter.

#### <a name="step-7--build-the-vm"></a>Steg 7:  Skapa den virtuella datorn

Här skapar du den virtuella datorn från avbildningen och ansluta två virtuella Premium Storage-hårddiskar:

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

#create new Avaiability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Befintliga distributioner som inte använder alltid på Tillgänglighetsgrupper

> [!NOTE]
> Befintliga distributioner först finns i den [krav](#prerequisites-for-premium-storage) i den här artikeln.

Det finns olika saker för SQL Server-distributioner som inte använder alltid på Tillgänglighetsgrupper och det är möjligt. Om du inte använder AlwaysOn och har en befintlig fristående SQL Server, kan du uppgradera till Premium Storage med hjälp av ett nytt moln-tjänsten och storage-konto. Överväg följande alternativ:

* **Skapa en ny SQL Server VM**. Du kan skapa en ny SQL Server VM som använder ett Premium Storage-konto, enligt beskrivningen i nya distributioner. Sedan säkerhetskopiera och återställa SQL Server-konfigurationen och användardata databaser. Programmet måste uppdateras för att referera till den nya SQL-servern om den används internt eller externt. Du skulle behöva kopiera alla 'utanför db-objekt som om du höll på med en sida vid sida (SxS) SQL Server-migrering. Detta inkluderar objekt, till exempel inloggningar, certifikat och länkade servrar.
* **Migrera en befintlig SQL Server-VM**. Detta kräver att SQL Server-dator försätts offline och sedan överföra dem till en ny molntjänst, vilket inkluderar kopierar alla dess anslutna virtuella hårddiskar till Premium Storage-kontot. När den virtuella datorn är online, refererar programmet till värdnamnet som innan. Tänk på att storleken på den befintliga disken påverkar prestanda. Till exempel hämtar en disk på 400 GB avrundas uppåt till en P20. Om du vet att du inte behöver den diskprestandan, kan du återskapa den virtuella datorn som en virtuell dator DS-serien och koppla Premium Storage virtuella hårddiskar i specifikationen storlek/prestanda du behöver. Sedan kan du koppla från och återansluta SQL DB-filer.

> [!NOTE]
> När du kopierar VHD-diskar som du bör känna till storlek, beroende på storleken innebär vilken Premium Storage-disktyp de faller, detta bestämmer disk prestanda-specifikationen. Azure rundar upp till närmaste diskens storlek, så om du har en 400 GB disk, detta avrundas uppåt till en P20. Beroende på dina befintliga i/o-krav för OS-VHD, kan du inte behöva migrera det till ett Premium Storage-konto.

Om din SQL Server används externt, ändras cloud service VIP-Adressen. Du också behöva uppdatera slutpunkterna ACL: er och DNS-inställningar.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Befintliga distributioner som använder ständigt aktiverade Tillgänglighetsgrupper

> [!NOTE]
> Befintliga distributioner först finns i den [krav](#prerequisites-for-premium-storage) i den här artikeln.

Från början i det här avsnittet tittar vi på hur Always On samverkar med Azure Networking. Vi sedan bryta ned migreringar i till två scenarier: där vissa avbrott kan tolereras migreringar och migreringar där du måste uppnå minimal avbrottstid.

En lokal SQL Server Always On-Tillgänglighetsgrupper använder en lyssnare lokalt som registrerar en virtuell DNS-namn tillsammans med en IP-adress som delas mellan en eller flera SQL-servrar. När klienter ansluter dirigeras de via IP-Adressen för lyssnaren till den primära SQL-servern. Det här är den server som äger alltid på IP-adressresurs vid den tidpunkten.

![DeploymentsUseAlways på][6]

I Microsoft Azure som du kan ha endast en IP-adress som tilldelats ett nätverkskort på den virtuella datorn, så använder för att uppnå samma lager Abstraktionslager som körs lokalt, Azure IP-adressen som tilldelas av intern/extern belastningsutjämnare (ILB/ELB). Den IP-adressresursen som delas mellan servrarna har angetts till samma IP-Adressen som ILB/ELB. Den här är publicerade i DNS och klienttrafik överförs via ILB/ELB till den primära SQL Server-repliken. ILB/ELB vet vilket SQL Server är primär eftersom den använder avsökningar avsöker alltid på IP-adressresurs. I exemplet ovan den avsökningar varje nod som har en slutpunkt som refereras av ELB/ILB, beroende på vilket som svarar är den primära SQL-servern.

> [!NOTE]
> Den interna Belastningsutjämnaren och ELB är tilldelas till en viss Azure-molntjänst, därför alla migreringen till molnet i Azure innebär det mest sannolika att IP för Load Balancer ändras.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrera Always On-distributioner som kan vissa avbrott

Det finns två olika metoder för att migrera Always On-distributioner som tillåter att vissa avbrott:

1. **Lägg till flera sekundära repliker i ett befintligt alltid på kluster**
2. **Migrera till ett nytt alltid på kluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Lägg till flera sekundära repliker i ett befintligt alltid på kluster

En strategi är att lägga till flera sekundära databaser till den ständigt aktiverad tillgänglighetsgrupp. Du måste lägga till dem i en ny molntjänst och uppdatera lyssnaren med den nya IP-load balancer.

##### <a name="points-of-downtime"></a>Saker stillestånd:

* Klusterverifieringen.
* Testa alltid på redundans för nya sekundära databaser.

Om du använder Windows lagringspooler i den virtuella datorn för högre i/o-dataflöde, och sedan dessa tas offline under en fullständig Klustervalideringen. Verifieringstest krävs när du lägger till noder i klustret. Den tid det tar för att köra testet kan variera, så bör du testa i din testmiljö att få en ungefärlig tid av hur lång tid det tar.

Du bör tillhandahålla tid där du kan utföra manuell växling vid fel och chaos testning på de nytillagda noderna för att säkerställa alltid på hög tillgänglighet fungerar som förväntat.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Du bör avsluta alla instanser av SQL Server där de lagringspooler används innan verifieringen körs.
>
> ##### <a name="high-level-steps"></a>Steg på hög nivå
>

1. Skapa två nya SQL-servrar i ny molntjänst med anslutna Premium Storage.
2. Kopiera över fullständig säkerhetskopiering och återställning med **NORECOVERY**.
3. Kopiera över ”utanför användare DB” beroende objekt, till exempel inloggningar osv.
4. Skapa en ny en nya interna belastningsutjämnaren (ILB) eller använda en extern Load Balancer (ELB) och sedan ställa in slutpunkterna för belastningen på både nya noder.

   > [!NOTE]
   > Kontrollera alla noder har rätt slutpunktskonfigurationen innan du fortsätter
   >
   >
5. Stoppa användarprogram/åtkomst till SQL-Server (om du använder lagringspooler).
6. Stoppa SQL Server-motorn Services på alla noder (om du använder lagringspooler).
7. Lägga till nya noder för att klustret och köra fullständig verifiering.
8. När verifieringen är klar kan du starta alla SQL Server-tjänster.
9. Säkerhetskopiera transaktionsloggar och Återställ användardatabaser.
10. Lägga till nya noder till den ständigt aktiverad tillgänglighetsgrupp och placera replikering i **synkron**.
11. Lägg till IP-adressresurs i den nya Cloud Service ILB/ELB via PowerShell för Always On baserat på flera platser exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). I Windows klustring, ställer du in den **möjliga ägare** av den **IP-adress** resursen för att de nya noderna gamla. Se avsnittet ”lägga till IP-adressresurs i samma undernät” i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Redundans till en av de nya noderna.
13. Se de nya noderna automatiskt Redundanspartners och testa redundans.
14. Ta bort ursprungliga noder från Tillgänglighetsgruppen.

##### <a name="advantages"></a>Fördelar

* Ny SQL-servrar kan vara testas (SQL Server och program) innan de läggs till alltid på.
* Du kan ändra storleken på virtuella datorn och anpassa lagringen till dina exakta krav. Det skulle dock vara fördelaktigt att behålla alla sökvägar för SQL-filen samma.
* Du kan styra när överföringen av DB-säkerhetskopiering till de sekundära replikerna har startats. Detta skiljer sig från att använda Azure **Start AzureStorageBlobCopy** för att kopiera virtuella hårddiskar, eftersom det är en asynkron kopia.

##### <a name="disadvantages"></a>Nackdelar

* När du använder lagringspooler för Windows finns klusterdriftstopp under fullständig Klustervalideringen för nya ytterligare noder.
* Beroende på vilken SQL Server-Version och befintliga antalet sekundära repliker, kan du inte att kunna lägga till flera sekundära repliker utan att ta bort befintliga sekundärservrar.
* Det kan vara långa SQL dataöverföringstid när du konfigurerar de sekundära databaser.
* Det finns ytterligare kostnad under migreringen medan du har nya datorer som körs parallellt.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrera till ett nytt alltid på kluster

En annan strategi är att skapa en helt ny alltid på kluster med helt nya noder i en ny molntjänst och sedan omdirigera klienter för att använda den.

##### <a name="points-of-downtime"></a>Punkter stillestånd

Det finns driftstopp när du överför program och användare till den nya Always On-lyssnaren. Avbrottstiden beror på:

* Den tid det tar att återställa säkerhetskopior av sista transaktionsloggen till databaser på nya servrar.
* Den tid det tar att uppdatera klientprogram använder ny Always On-lyssnare.

##### <a name="advantages"></a>Fördelar

* Du kan testa faktiska produktionsmiljön, SQL Server och Operativsystemets build-ändringar.
* Har du möjlighet att anpassa lagringen och för att minska storleken på virtuell dator. Detta kan resultera i kostnader minskning.
* Under den här processen kan du uppdatera din version eller SQL Server-version. Du kan också uppgradera operativsystemet.
* Föregående alltid på klustret kan fungera som en solid rollback-mål.

##### <a name="disadvantages"></a>Nackdelar

* Du behöver ändra lyssnaren DNS-namn om du vill att båda Always On-klustren körs samtidigt. Detta lägger till administration försämras under migreringen eftersom klienten programsträngar måste spegla namnet på nya lyssnaren.
* Du måste implementera en mekanism för synkronisering mellan två miljöer så att de så nära som möjligt för att minimera de slutliga synkroniseringskrav innan migreringen.
* Det läggs till kostnaden under migreringen när du har den nya miljön som körs.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrera alltid på distributioner för minimal driftstörning

Det finns två strategier för att migrera Always On-distributioner för minimal driftstörning:

1. **Använda en befintlig sekundär: Enskild plats**
2. **Utnyttja befintliga sekundära tillgänglighetsreplik(er): Flera platser**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Använda en befintlig sekundär: Enskild plats

En strategi för minimal driftstörning är att ta ett befintligt moln sekundära och ta bort den från den aktuella Molntjänsten. Kopiera de virtuella hårddiskarna till det nya Premium Storage-kontot, och skapa den virtuella datorn i den nya Molntjänsten. Uppdatera sedan lyssnare i kluster och redundans.

##### <a name="points-of-downtime"></a>Punkter stillestånd

* Det finns driftstopp när du uppdaterar den sista noden till den belastningsutjämnade slutpunkten.
* Klient-återanslutning kan fördröjas beroende på din klient/DNS-konfiguration.
* Det finns ytterligare nedtid om du väljer att ta alltid på klustergruppen offline för att byta ut IP-adresser. Du kan undvika detta genom att använda ett eller-beroende och möjliga ägare för IP-adress har lagts till resursen. Se avsnittet ”lägga till IP-adressresurs i samma undernät” i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> När du vill att noden har lagts till partake i som en alltid på Redundanspartnern som du behöver lägga till en Azure-slutpunkt med en referens för Load Balanced set. När du kör den **Add-AzureEndpoint** för att göra detta, aktuella anslutningen förbli öppen, men nya anslutningar till lyssnaren kan inte upprättas förrän belastningsutjämnaren har uppdaterats. Testa detta har visat senaste 90-120seconds detta bör testas.

##### <a name="advantages"></a>Fördelar

* Utan extra kostnader som uppstår under migreringen.
* En migrering.
* Minskad komplexitet.
* Ger ökad IOPS från SKU: er för Premium-lagring. När diskarna är frånkopplad från den virtuella datorn och kopieras till den nya Molntjänsten en 3 part kan du använda verktyget för att öka storleken för virtuell Hårddisk, vilket ger högre genomströmning. Öka VHD-storlekar finns i den här [forumdiskussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nackdelar

* Det finns en temporär förlust av HA och DR under migreringen.
* Du måste använda en minsta storlek som har stöd antalet VHD: er, så du kan inte lågsäsong dina virtuella datorer eftersom det här är en 1:1-migrering.
* Det här scenariot använder Azure **Start AzureStorageBlobCopy** -kommandot som är asynkron. Det finns inget serviceavtal för kopiera slutförande. Tiden för kopiorna varierar även om det beror på vänta i kön som den beror också på hur mycket data du överför. Kopiera tiden ökar om överföringen ska till en annan Azure-datacenter som stöd för Premium-lagring i en annan region. Om du har bara 2 noder kan du en möjlig åtgärd om kopian tar längre tid än i testet. Det kan inbegripa följande idéer.
  * Lägg till en tillfällig 3 SQL Server-nod för hög tillgänglighet innan migreringen överenskomna avbrott.
  * Kör migrering utanför Azure schemalagt underhåll.
  * Se till att du har konfigurerat klustrets kvorum korrekt.  

##### <a name="high-level-steps"></a>Steg på hög nivå

Det här dokumentet visar inte ett komplett exempel från slutpunkt till slutpunkt, men den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) innehåller information som kan användas för att utföra detta.

![MinimalDowntime][8]

* Samla in diskkonfigurationen och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett Premium Storage-konto och kopiera virtuella hårddiskar från Standard Storage-konto
* Skapa ny molntjänst och distribuera om SQL2 VM i Molntjänsten. Skapa den virtuella datorn med hjälp av den kopierade ursprungliga OS-VHD och ansluta de kopierade virtuella hårddiskarna.
* Konfigurera ILB-/ ELB och lägga till slutpunkter.
* Uppdatera lyssnare genom att antingen:
  * Koppla från alltid på gruppen och uppdatera alltid på lyssnaren med nya ILB / ELB IP-adress.
  * Eller lägga till IP-adressresurs med nya Cloud Service ILB/ELB via PowerShell i Windows-kluster. Sedan ange de möjliga ägarna av IP-adressresursen till noden migrerade SQL2, och ange detta eller-beroende i nätverksnamn. Se avsnittet ”lägga till IP-adressresurs i samma undernät” i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Kontrollera DNS-konfiguration/spridning till klienterna.
* Migrera SQL1 virtuell dator och gå igenom steg 2 – 4.
* Om du använder stegen 5ii Lägg sedan till SQL1 som möjlig ägare för den extra IP-adressresursen
* Redundanstestningen.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utnyttja befintliga sekundära tillgänglighetsreplik(er): Flera platser

Om du har noder i mer än en Azure-datacenter (DC) eller om du har en hybridmiljö kan du använda en Always On-konfiguration i den här miljön för att minimera driftstopp.

Metoden är att ändra Always On-synkronisering till synkron för lokal eller sekundära Azure domänkontrollanten och sedan redundans över till den SQL-servern. Kopiera de virtuella hårddiskarna till ett Premium Storage-konto, och distribuera om datorn till en ny molntjänst. Uppdatera lyssnaren och växla sedan tillbaka.

##### <a name="points-of-downtime"></a>Punkter stillestånd

Avbrottstiden består av tid ska gå över till alternativa DC och tillbaka. Det beror också på din klient/DNS-konfiguration och klient-återanslutning kan fördröjas.
Överväg följande exempel visar en hybrid Always On-konfiguration:

![MultiSite1][9]

##### <a name="advantages"></a>Fördelar

* Du kan använda befintliga infrastruktur.
* Du har möjlighet att före uppgradering Azure storage på domänkontrollanten DR Azure först.
* DR Azure DC-lagring kan konfigureras.
* Det finns minst två växling vid fel under migreringen, exklusive redundanstestning.
* Du behöver inte att flytta SQL Server-data med säkerhetskopiering och återställning.

##### <a name="disadvantages"></a>Nackdelar

* Beroende på klientåtkomst till SQL Server, kan det finnas ökad latens när SQL Server körs i en annan Domänkontrollant till programmet.
* Kopiera tidpunkten för virtuella hårddiskar till Premium storage kan vara långa. Detta kan påverka ditt beslut på om du vill behålla noden i Tillgänglighetsgruppen. Tänk på detta när loggen arbetar inläsningar körs under migreringen är krävs, eftersom den primära noden måste ha unreplicated transaktioner i dess transaktionsloggen. Därför kan detta växa avsevärt.
* Det här scenariot använder Azure **Start AzureStorageBlobCopy** -kommandot som är asynkron. Det finns inget serviceavtal för slutförande. Tiden för kopiorna varierar, även om det beror på vänta i kön, det beror också på hur mycket data du överför. Därför ha bara en nod i datacentret 2, du bör vidta säkerhetsåtgärder om kopian tar längre tid än i testet. De här avhjälpande stegen är följande förslag:
  * Lägg till en tillfällig 2nd SQL nod för hög tillgänglighet innan migreringen överenskomna avbrott.
  * Kör migrering utanför Azure schemalagt underhåll.
  * Se till att du har konfigurerat klustrets kvorum korrekt.

Det här scenariot förutsätter att du har dokumenterat installationen och veta hur lagringen har mappats för att utföra ändringar för den diskens cacheinställningar.

##### <a name="high-level-steps"></a>Steg på hög nivå

![Multisite2][10]

* Gör lokalt / Azure DC för SQL Server-primära alternativ och blir andra automatisk redundans Partner (AFP).
* Samla in information om diskkonfiguration från SQL2 och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett Premium Storage-konto och kopiera virtuella hårddiskar från Standard Storage-kontot.
* Skapa en ny molntjänst och skapa SQL2 VM med dess Premier Storage-diskar som är anslutna.
* Konfigurera ILB-/ ELB och lägga till slutpunkter.
* Uppdatera alltid på lyssnaren med nya ILB / ELB IP-adress och testning av redundans.
* Kontrollera DNS-konfigurationen.
* Ändra AFP till SQL2, och sedan migrera SQL1 och gå igenom steg 2 – 5.
* Redundanstestningen.
* Växla AFP tillbaka till SQL1 och SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Tillägg: Migrera en Multisite alltid på klustret till Premium Storage

Resten av den här artikeln innehåller ett detaljerat exempel från konverteringen av en Always On-kluster på flera platser till Premium storage. Det konverterar också lyssnaren från att använda en extern belastningsutjämnare (ELB) till en intern belastningsutjämnare (ILB).

### <a name="environment"></a>Miljö

* Windows 2k12 / SQL 2k12
* 1 DB filer på SP
* 2 x lagringspooler per nod

![Appendix1][11]

### <a name="vm"></a>VM:

I det här exemplet ska vi att demonstrera flyttar från en ELB till ILB. ELB fanns innan ILB, så att det visar hur du växlar till ILB under migreringen.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pre steg: Anslut till prenumeration

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Steg 1: Skapa nytt Lagringskonto och Molntjänsten

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Steg 2: Öka tillåtna fel på resurser <Optional>

På vissa resurser som hör till din Always On Availability Group att det finns begränsningar för hur många fel som kan uppstå med en punkt där Klustertjänsten försöker starta om resursgruppen. Vi rekommenderar att du ökar detta även om du gå igenom den här proceduren sedan om du manuellt inte växling vid fel och utlösa redundans genom att stänga av datorer som du kan hämta nära den här gränsen.

Det skulle vara klokt fördubbla hur fel om du vill göra detta i Klusterhanteraren gå till egenskaperna för resursgruppen Always On:

![Appendix3][13]

Ändra maximalt antal misslyckanden till 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Steg 3: Ytterligare IP-adressresurs för klustergrupp <Optional>

Om du har endast en IP-adress för klustergruppen och detta har justerats till undernätet för molnet, var försiktig, om du av misstag kan offline alla klusternoder i molnet på nätverket och sedan klustrets IP-resurs och Klusternätverksnamnet inte är längre ska anslutas. I så fall kan förhindras uppdateringar till andra klusterresurser.

#### <a name="step-4-dns-configuration"></a>Steg 4: DNS-konfiguration

Implementera en smidig övergång beror på hur DNS som används och uppdateras.
När Always On installeras, skapas en klusterresurs för Windows-grupp om du öppnar Klusterhanteraren kan du se att åtminstone den har tre resurser, de två som dokumentet refererar till är:

* Virtuellt nätverksnamn (VNN) – DNS-namn som klienterna ansluter till om du vill ansluta till SQL-servrar via alltid på.
* IP-adressresurs – IP-adressen som som är associerade med VNN och du kan ha fler än en i multisitekonfigurationen du har en IP-adress per plats/undernät.

När du ansluter till SQL Server, SQL Server-Client-drivrutinen hämtar DNS-poster som är associerade med lyssnaren och försöker ansluta till varje Always On associerade IP-adress. Nu ska vi går igenom några faktorer som kan påverka det här.

Hur många samtidiga DNS-poster som är associerade med namnet på lyssnare beror inte bara på antalet IP-adresser som är associerade, men ' RegisterAllIpProviders'setting i Redundansklustring för Always ON VNN resursen.

När du distribuerar alltid på i Azure finns på olika steg för att skapa lyssnaren och IP-adresser, måste du manuellt konfigurerar RegisterAllIpProviders 1, detta skiljer sig från en lokal Always On distribution där den har angetts till 1.

Om ”RegisterAllIpProviders” är 0, och sedan du bara ser en DNS-post i DNS som är associerade med lyssnaren:

![Appendix4][14]

Om ”RegisterAllIpProviders” är 1:

![Appendix5][15]

Koden nedan minnesdumpar ut VNN-inställningar och anger det åt dig. För att ändringen ska börja gälla måste du koppla från VNN och aktivera den igen. Detta tar lyssnaren offline orsakar klienten anslutning avbrott.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

I ett senare migreringssteg måste du uppdatera Always On-lyssnaren med uppdaterade IP-adressen som refererar till en belastningsutjämnare, detta innebär att en IP-adress resource borttagning och tillägg. När IP-uppdateringen måste du se till att den nya IP-adressen har uppdaterats i DNS-zon och att klienterna uppdaterar sin lokala DNS-cachen.

Om klienterna finns i olika nätverkssegment och referera till en annan DNS-server, måste du tänka på vad som händer om DNS-zonöverföring under migreringen, eftersom programmet återansluta tiden är begränsad av zonen överför tiden för alla nya IP-adresser adresser för lyssnaren. Om du är under tiden begränsningen här du ska diskutera och testa tvinga en inkrementell zonöverföring med dina Windows-team, och även placera DNS-värdpost till en lägre Time To Live (TTL), så uppdateras. Mer information finns i [inkrementella zonöverföringar](https://technet.microsoft.com/library/cc958973.aspx) och [Start DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Som standard-TTL för DNS-post som är associerad med lyssnaren i Always On i Azure är 1200 sekunder. Du kan välja att minska det här om du är under tiden som begränsning under migreringen att se till att klienterna uppdatera sina DNS med den uppdaterade IP-adressen för lyssnaren. Du kan se och ändra konfigurationen av dumpning ut konfigurationen av VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Ju lägre 'HostRecordTTL', en större mängd DNS-trafik inträffar.

##### <a name="client-application-settings"></a>Inställningar för program

Om din SQL-klientprogrammet har stöd för .net 4.5 SQLClient, därefter kan du använda ”MULTISUBNETFAILOVER = TRUE” nyckelord. Det här nyckelordet tillämpas eftersom de ger snabbare anslutning till SQL Always On Availability Group under redundansväxlingen. Den räknar upp via alla IP-adresser som är associerade med Always On-lyssnaren parallellt och utför en mer aggressivt TCP återförsök anslutningshastighet under en redundansväxling.

Mer information om de tidigare inställningarna finns i [MultiSubnetFailover nyckelord och associerade funktioner](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Se även [SqlClient stöd för hög tillgänglighet, Haveriberedskap](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Steg 5: Inställningarna för klusterkvorum

Du bör ändra kvoruminställningen klustret om du använder filen resurs vittne (FSW) med två noder, bör du ange kvorum tillåter Nodmajoritet och utnyttja dynamisk rösta eftersom du ska ta reda på minst en SQL Server nedåt i taget , vilket ger en enda nod att förbli stående.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Läs mer om hantering och konfiguration av klustrets kvorum, [konfigurera och hantera kvorum i ett redundanskluster för Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Steg 6: Extrahera befintliga slutpunkter och ACL: er

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Spara texten i en fil.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Steg 7: Ändra Redundanspartners och Replikeringslägen

Om du har fler än två SQL-servrar kan du bör ändra redundans för en annan sekundär i en annan Domänkontrollant eller en lokal till ”synkron” och göra det en automatisk redundans Partner (AFP), detta är så att du upprätthålla hög tillgänglighet, även om du gör ändringar. Du kan göra detta via TSQL av dock ändra SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Steg 8: Ta bort sekundära virtuella datorn från Molntjänsten

Du bör planera att migrera en sekundär nod i molnet först. Om den här noden är för närvarande primära, bör du initiera en manuell redundansväxling.

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 9: Ändra disk cachelagring i CSV-filen och spara

Om datavolymer, bör detta vara inställt på READONLY.

För TLOG volymer, ska de vara inställd på Ingen.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Steg 10: Kopiera virtuella hårddiskar

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

![Appendix8][18]

Vänta tills alla dessa registreras åtgärden lyckades.

Information för enskilda blobbar:

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Steg 12: Importera sekundär till ny molntjänst

Koden nedan använder även alternativet har lagts till här kan du importera datorn och använda retainable VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Avaiability Set
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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Steg 13: Skapa ILB på nya Cloud Svc, lägga till Load Balanced slutpunkter och ACL: er

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

#http://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Steg 14: Always On Update

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

Nu ska du ta bort gamla Molntjänsten IP-adress.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Steg 15: DNS-uppdateringskontroll

Nu bör du kontrollera DNS-servrar i dina nätverk för SQL Server-klienten och se till att kluster har lagts till extra värdpost för IP-adress har lagts till. Om dessa DNS-servrar som inte har uppdaterat, Överväg att tvinga fram en DNS-zonöverföring och se till att klienterna i det undernätet ska kunna matcha mot både alltid på IP-adresser, det är så du inte behöver vänta på automatiska DNS-replikeringen.

#### <a name="step-16-reconfigure-always-on"></a>Steg 16: Konfigurera om Always On

Nu kan vänta du tills sekundärt noden som har migrerats för fullständigt omsynkronisering med den lokala noden och växla till synkron replikering noden och blir AFP.  

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 18: Ändra disk cachelagring i CSV-filen och spara

För datavolymer sättas cacheinställningarna till READONLY.

Cacheinställningarna ska anges som NONE för TLOG volymer.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Steg 19: Skapa nytt oberoende Lagringskonto för sekundär nod

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

#### <a name="step-20-copy-vhds"></a>Steg 20: Kopiera virtuella hårddiskar

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

#check induvidual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Du kan kontrollera VHD-kopians status för alla virtuella hårddiskar:

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

Vänta tills alla dessa registreras åtgärden lyckades.

Information för enskilda blobbar:

```powershell
#Check induvidual blob status
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

#Join to existing Avaiability Set

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Steg 22: Lägg till Load Balanced slutpunkter och ACL: er

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#http://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Steg 23: Redundanstest

Vänta tills den migrerade noden som synkroniseras med den lokala alltid på noden. Placera den i synkron replikeringsläge och vänta tills den är synkroniserad. Sedan migreras redundans från lokalt till den första noden, vilket är AFP. När som har arbetat kan du ändra den sista migrerade noden till AFP.

Du bör testa redundansväxling mellan alla noder och kör om chaos tester för att se till att redundans fungerar som förväntat och i en rimlig manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Steg 24: Sätt tillbaka inställningarna för klusterkvorum / TTL för DNS / Failover Pntrs / synkroniseringsinställningar

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Att lägga till IP-adressresurs i samma undernät

Om du har bara två SQL-servrar och vill migrera dem till en ny molntjänst, men vill ha dem i samma undernät, kan du undvika tar lyssnaren offline för att ta bort den ursprungliga alltid på IP-adressen och lägga till den nya IP-adressen. Om du migrerar de virtuella datorerna till ett annat undernät, behöver du inte göra detta eftersom det inte finns en ytterligare klusternätverk som refererar till det undernätet.

När du har upp den migrerade sekundärt och lagts till i den nya resursen för IP-adressen för den nya Molntjänsten före redundans den befintliga primärt, bör du utföra dessa steg inom klustret Redundanshanteraren:

Om du vill lägga till IP-adress, finns i bilaga, steg 14.

1. Ändra möjliga ägare för att kunna ”befintliga primära SQL Server-, i det här exemplet” dansqlams4 ”för den aktuella IP-adressresursen:

    ![Appendix13][23]
2. Ändra möjliga ägare till 'Migrerats sekundära SQL Server ”i det här exemplet” dansqlams5 ”för den nya IP-adress-resursen:

    ![Appendix14][24]
3. När detta är inställt kan du redundans och när den sista noden migreras möjliga ägare kan du ändra så att noden har lagts till som en möjlig ägare:

    ![Appendix15][25]

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQLServer i Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
