---
title: "Använda Azure Premium-lagring med SQLServer | Microsoft Docs"
description: "Den här artikeln använder resurser som har skapats med den klassiska distributionsmodellen och ger vägledning om hur du använder Azure Premium-lagring med SQL Server som körs på Azure Virtual Machines."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: ad4b5aeed645512774f1a3ecf94de37beff26b22
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Använd Azure Premium Storage med SQL Server på virtuella datorer
## <a name="overview"></a>Översikt
[Azure Premium-lagring](../premium-storage.md) är nästa generation av lagring som innehåller låg latens och hög genomströmning IO. Det fungerar bäst för nyckel i/o-intensiv arbetsbelastning, till exempel SQL Server på IaaS [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln innehåller planering och vägledning för att migrera en virtuell dator som kör SQL Server att använda Premium-lagring. Detta inkluderar Azure-infrastrukturen (nätverk, lagring) och Gäst Windows VM steg. Exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) visar en fullständig omfattande slutpunkt till slutpunkt-migrering av hur du flyttar större virtuella datorer för att dra nytta av bättre lokala SSD-lagring med PowerShell.

Det är viktigt att förstå slutpunkt till slutpunkt-processen genom att använda Azure Premium-lagring med SQL Server på IAAS-VM. Detta omfattar:

* Identifiering av förutsättningar för att kunna använda Premium-lagring.
* Exempel på distribution av SQL Server på IaaS till Premium-lagring för nya distributioner.
* Exempel på migrera befintliga distributioner, både fristående servrar och distributioner med hjälp av SQL Always On-Tillgänglighetsgrupper.
* Möjliga migrering metoder.
* Fullständig slutpunkt till slutpunkt i exemplet visar Azure, Windows och SQL Server stegen för migrering av en befintlig Always On-implementering.

Mer information om SQL Server i Azure Virtual Machines finns [SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Skapad av:** Mikael Sol **Teknisk granskare:** Thomas Carlos Vargas sill, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Pettersson.

## <a name="prerequisites-for-premium-storage"></a>Krav för Premium-lagring
Det finns flera förutsättningar för att använda Premium-lagring.

### <a name="machine-size"></a>Storleken på datorn
För att använda Premium-lagring behöver du använda DS-serien virtuella datorer (VM). Om du inte har använt DS-serien datorer i din molntjänst innan måste du ta bort den befintliga virtuella datorn, hålla anslutna diskar och sedan skapa en ny molntjänst innan du återskapa den virtuella datorn som DS * rollstorleken. Mer information om storlekar för virtuella datorer finns [virtuell dator och Molntjänststorlekar för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Molntjänster
Du kan bara använda DS * virtuella datorer med Premium-lagring när de skapas i en ny molntjänst. Om du använder SQL Server alltid aktiverad i Azure ska alltid på lyssnaren avser Azure interna eller externa belastningen belastningsutjämnaren IP-adressen som är associerad med en tjänst i molnet. Den här artikeln fokuserar på hur du migrerar samtidigt är tillgänglig i det här scenariot.

> [!NOTE]
> En DS * serie måste vara den första virtuella dator som har distribuerats till en ny molntjänst.
>
>

### <a name="regional-vnets"></a>Regional VNET
För DS * virtuella datorer måste du konfigurera virtuella nätverk (VNET) värd för dina virtuella datorer ska regionala. Detta ”utökar” VNET är att ge större virtuella datorer att etableras i andra kluster och tillåta kommunikation mellan dem. I följande skärmbild visar den markerade platsen regional Vnet medan det första resultatet visas en ”smala” virtuella nätverk.

![RegionalVNET][1]

Du kan höja ett supportärende för Microsoft för att migrera till ett regionalt VNET, Microsoft kommer ändrar och sedan ändra egenskapen AffinityGroup i nätverkskonfigurationen för att slutföra migreringen till regionalt Vnet. Exportera nätverkskonfigurationen i PowerShell och Ersätt den **AffinityGroup** egenskap i den **VirtualNetworkSite** element med en **plats** egenskapen. Ange `Location = XXXX` där `XXXX` är en Azure-region. Importera den nya konfigurationen.

Till exempel att ta hänsyn till följande konfiguration för virtuellt nätverk:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Om du vill flytta det till ett regionalt VNET i Västeuropa, ändra konfigurationen av följande:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Lagringskonton
Du behöver skapa ett nytt lagringskonto som är konfigurerad för Premium-lagring. Observera att användningen av Premium-lagring är inställt på lagringskontot, inte på enskilda virtuella hårddiskar, men när du använder en DS * serien virtuell dator kan du bifoga VHD från Premium och standardlagring konton. Du kan överväga att detta om du inte vill att OS-VHD till Premium-lagring-kontot.

Följande **ny AzureStorageAccountPowerShell** med ”Premium_LRS” **typen** skapar ett Premiumlagringskonto:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Inställningar för cachelagring av virtuella hårddiskar
Den största skillnaden mellan att skapa diskar som ingår i ett Premium Storage-konto är diskcache-inställningen. För SQL Server-Data volym diskar det rekommenderas att du använder '**Läs cachelagring**'. För transaktionen loggvolymer diskcache-inställningen ska vara inställd på '**ingen**'. Detta skiljer sig från rekommendationerna för Standard Storage-konton.

Cache-inställningen kan inte ändras när de virtuella hårddiskarna har bifogats. Du måste koppla från och Återanslut den virtuella Hårddisken med en uppdaterad cache-inställningen.

### <a name="windows-storage-spaces"></a>Lagringsutrymmen för Windows
Du kan använda [Windows lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) som du gjorde med föregående standardlagring kommer detta att du kan migrera en virtuell dator som redan använder lagringsutrymmen. Exemplet i [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (steg 9 och framåt) visar Powershell-koden för att extrahera och importera en virtuell dator med flera anslutna virtuella hårddiskar.

Lagringspooler som användes med Standard-Azure storage-konto för att förbättra genomflöde och minska svarstiden. Du kan hitta värdet i testning lagringspooler med Premium-lagring för nya distributioner, men de lägger till ytterligare komplexitet med Lagringsinställningar.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Så här hittar du vilka Azure virtuella diskar kartan lagringspooler
Eftersom det finns olika cache inställningen rekommendationer för anslutna virtuella hårddiskar, kanske du vill kopiera de virtuella hårddiskarna till ett Premium Storage-konto. När du återansluta dem till den nya DS-serien VM, kanske du måste ändra inställningar för cachelagring av. Det är enklare att använda Premium-lagring rekommenderade inställningar när du har separata virtuella hårddiskar för SQL-datafiler och loggfiler (istället för en enskild virtuell Hårddisk som innehåller både).

> [!NOTE]
> Om du har SQL Server-data och loggfilen filer på samma volym beror cachelagring alternativ du väljer på i/o-åtkomstmönster för databasarbetsbelastningar. Testa bara kan visa vilket alternativ för cachelagring är bäst för det här scenariot.
>
>

Men om du använder Windows lagringsutrymmen som består av flera virtuella hårddiskar som du behöver titta på din ursprungliga skript för att identifiera vilket anslutna virtuella hårddiskar finns i vilka specifika poolen, så du kan ange inställningar för cachelagring av därefter för varje disk.

Du kan använda följande steg för att fastställa mappningen disklagring/poolen om du inte har ursprungliga skriptet som är tillgängliga för att visa dig som virtuella hårddiskar som mappas till lagringspoolen.

Använd följande steg för varje disk:

1. Hämta listan över diskar som är kopplad till virtuell dator med den **Get-AzureVM** kommando:

    Get-AzureVM - ServiceName <servicename> -namnet <vmname> | Get-AzureDataDisk
2. Observera Diskname och LUN.

    ![DisknameAndLUN][2]
3. Fjärrskrivbord till den virtuella datorn. Gå till **Datorhantering** | **Enhetshanteraren** | **diskenheter**. Titta på egenskaperna för varje 'Microsoft virtuella diskar:

    ![VirtualDiskProperties][3]
4. LUN-nummer här är en referens till LUN-nummer som du anger när du kopplar den virtuella Hårddisken till den virtuella datorn.
5. För den ”Microsoft Virtual Disk” gå till den **information** fliken i den **egenskapen** lista, gå till **drivrutinen nyckeln**. I den **värdet**, Observera den **Offset**, vilket är 0002 i följande skärmbild. 0002 anger PhysicalDisk2 som refererar till lagringspoolen.

    ![VirtualDiskPropertyDetails][4]
6. För varje lagringspool dump ut de associera diskarna:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Nu kan du använda koppla den här informationen för att associera virtuella hårddiskar till fysiska diskar i lagringspooler.

När du har mappat virtuella hårddiskar till fysiska diskar i lagringspooler som du kan koppla bort och kopiera dem över till en Premium Storage-konto kan sedan koppla dem till rätt cache-inställningen. Se exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), steg 8 till och med 12. Dessa steg visar hur du extrahera diskkonfiguration VM-ansluten virtuell Hårddisk till en CSV-fil, kopiera de virtuella hårddiskarna, ändra inställningar för cachelagring av disk-konfiguration och slutligen distribuera den virtuella datorn som en serie DS VM med anslutna diskar.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM-lagring bandbredd och kapaciteten för lagring av virtuell Hårddisk
Mängden lagringsprestanda beror på den angivna DS * VM-storleken och VHD-storlekar. De virtuella datorerna har olika tillägg för antalet virtuella hårddiskar som kan bifogas och maximal bandbredd som de stöder (MB/s). Viss bandbredd-siffror finns [virtuell dator och Molntjänststorlekar för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ökad IOPS uppnås med större storlekar för diskar. Du bör överväga när du funderar på sökvägen för migreringen. Mer information [finns i tabellen för IOPS och disktyper](../premium-storage.md#scalability-and-performance-targets).

Slutligen bör du att virtuella datorer har olika maximal disk bandbredder de stöder för alla diskar som är anslutna. Du kan fylla maximal disk-bandbredden som är tillgänglig för Virtuella datorns rollstorlek under hög belastning. Till exempel stöder en Standard_DS14 upp till 512 MB/s. Därför kan du fylla diskbandbredden som av den virtuella datorn med tre P30 diskar. Men i det här exemplet genomströmning gränsen kan överskridas beroende på blandning av läsning och skrivning IOs.

## <a name="new-deployments"></a>Nya distributioner
I följande två avsnitt visar hur du kan distribuera virtuella SQL Server-datorer till Premium-lagring. Som nämnts så bör behöver du inte nödvändigtvis placera OS-disken till Premium-lagring. Du kan välja att göra det om du avser att placera alla i/o-arbetsbelastningar på OS-VHD.

Det första exemplet visar genom att använda befintliga avbildningar i Azure-galleriet. Det andra exemplet visar hur du använder en anpassad VM-avbildning som du har i ett befintligt lagringskonto som Standard.

> [!NOTE]
> Dessa exempel förutsätter att du redan har skapat ett regionalt VNET.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Skapa en ny virtuell dator med Premium-lagring med bild galleriet
Exemplet nedan visar hur du placera OS-VHD till premium-lagring och bifogar Premium Storage virtuella hårddiskar. Du kan också placera OS-disken i ett standardlagringskonto och sedan koppla virtuella hårddiskar som finns i ett Premium Storage-konto. Båda scenarierna är visas.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Steg 1: Skapa ett Premiumlagringskonto
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Steg 2: Skapa en ny molntjänst
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Steg 3: Reservera en Cloud Service VIP (valfritt)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Steg 4: Skapa en VM-behållare
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Steg 5: Avyttring OS VHD Standard eller Premium-lagring
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Steg 6: Skapa en virtuell dator
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
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
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


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Skapa en ny virtuell dator för att använda Premium-lagring med en anpassad avbildning
Det här scenariot visar där du har befintliga anpassade avbildningar som finns i ett standardlagringskonto. Som tidigare nämnts om du vill placera OS-VHD på Premium-lagring behöver du kopiera den bild som finns i standardlagringskontot och överför dem till en Premium-lagring innan den kan användas. Om du har en bild på lokalt, kan du också använda den här metoden för att kopiera som direkt till Premium-lagringskontot.

#### <a name="step-1-create-storage-account"></a>Steg 1: Skapa Storage-konto
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Steg 2 Skapa molntjänst
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Steg 3: Använd befintlig avbildning
Du kan använda en befintlig avbildning. Du kan [ta en bild av en befintlig dator](../classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observera den datorn som du avbildningen inte behöver DS * dator. När du har bilden följande steg visar hur du kopierar den till Premium Storage-konto med den **Start AzureStorageBlobCopy** PowerShell-kommandot.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Steg 4: Kopiera Blob mellan Storage-konton
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Steg 5: Kontrollera regelbundet-kopian status:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Steg 6: Lägg till avbildning disk i Azure-disken databasen i prenumerationen
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Du kanske att även om rapporter som lyckades, du kan fortfarande få ett lån diskfel. I så fall väntar du cirka 10 minuter.
>
>

#### <a name="step-7--build-the-vm"></a>Steg 7: Skapa den virtuella datorn
Här skapar du den virtuella datorn från avbildningen och ansluta två Premium Storage virtuella hårddiskar:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Befintliga distributioner som inte använder alltid på Tillgänglighetsgrupper
> [!NOTE]
> Befintliga distributioner först finns i [krav](#prerequisites-for-premium-storage) i det här avsnittet.
>
>

Det finns olika överväganden för SQL Server-distributioner som inte använder alltid på Tillgänglighetsgrupper och det är möjligt. Om du inte använder alltid på och har en befintlig fristående SQL Server kan du uppgradera till Premium-lagring med hjälp av ett nytt cloud service och storage-konto. Överväg följande alternativ:

* **Skapa en ny SQL Server-VM**. Du kan skapa en ny SQL Server-VM som använder ett premiumlagringskonto, enligt beskrivningen i nya distributioner. Sedan säkerhetskopiera och återställa SQL Server-databaserna konfiguration och användare. Programmet måste uppdateras för att referera till den nya SQL-servern om den används internt eller externt. Du skulle behöva kopiera alla out-of-db-objekt som om du höll på en sida vid sida (SxS) SQL Server-migrering. Detta inkluderar objekt som inloggningar, certifikat och länkade servrar.
* **Migrera en befintlig SQL Server-VM**. Detta kräver att SQL Server-VM tas offline och sedan överföra den till en ny molntjänst, som innehåller alla dess anslutna virtuella hårddiskar har kopierats till Premium-lagringskontot. När den virtuella datorn är online kommer Servervärdnamn som innan referera till programmet. Tänk på att storleken på den befintliga disken påverkar prestandaegenskaperna. Till exempel hämtar en 400 GB disk avrundat till en P20. Om du vet att du inte behöver som diskprestanda, kan du skapa den virtuella datorn som en virtuell dator DS-serien och bifoga Premium Storage virtuella hårddiskar i specifikationen storlek och prestanda som du behöver. Sedan kan du koppla från och Återanslut SQL DB-filer.

> [!NOTE]
> När du kopierar VHD-diskar som du bör vara medveten om storlek, beroende på storleken innebär vilka Premium Storage disktyp de hör till, detta avgör disk prestanda specifikation. Kommer Azure att avrunda till närmaste disk storlek, så om du har en 400 GB disk, detta ska avrundas till en P20. Beroende på dina befintliga i/o-krav för den virtuella Hårddisken OS behöver du kan inte migrera det till ett Premium Storage-konto.
>
>

Om din SQL Server används externt ändras cloud service VIP. Du måste även ha uppdateringen slutpunkter, ACL: er och DNS-inställningar.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Befintliga distributioner som använder alltid på Tillgänglighetsgrupper
> [!NOTE]
> Befintliga distributioner först finns i [krav](#prerequisites-for-premium-storage) i det här avsnittet.
>
>

Först ska vi titta på hur alltid på samverkar med Azure-nätverk i det här avsnittet. Vi kommer sedan att dela upp migreringar i två scenarier: där vissa avbrott kan tillåtas migreringar och migreringar där du måste uppnå minimal avbrottstid.

Lokal SQL Server alltid på Tillgänglighetsgrupper använder en lyssnare lokalt som registrerar ett virtuella DNS-namn tillsammans med en IP-adress som delas mellan en eller flera SQL-servrar. När klienter ansluter dirigeras de via IP-Adressen för lyssnaren till den primära SQL-servern. Det här är den server som äger den alltid på IP-resursen samtidigt.

![DeploymentsUseAlways på][6]

I Microsoft Azure som du kan ha endast en IP-adress som tilldelats ett nätverkskort på den virtuella datorn, så använder för att uppnå samma lager Abstraktionslager som lokalt, Azure IP-adressen som har tilldelats intern/extern belastningsutjämnare (ILB/ELB). IP-resurs som delas mellan servrarna har angetts till samma IP-Adressen som ILB/ELB. Detta är publicerad i DNS och klienttrafik överförs via ILB/ELB till den primära SQL Server-repliken. ILB/ELB vet vilken SQL Server är primär eftersom den använder avsökningar till avsökning alltid på IP-resurs. I föregående exempel är den avsökningar varje nod som har en slutpunkt som refereras av ELB/ILB, beroende på vilket som svarar är den primära SQL-servern.

> [!NOTE]
> ILB och ELB tilldelas till en viss Azure-molntjänst, därför alla molnmigrering i Azure troligen innebär att läsa in belastningsutjämning IP ändras.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrera alltid på distributioner som kan tillåta att vissa avbrott
Det finns två olika metoder för att migrera alltid distributioner så att vissa avbrott:

1. **Lägga till flera sekundära repliker i ett befintligt alltid på kluster**
2. **Migrera till ett nytt alltid på kluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Lägga till flera sekundära repliker i ett befintligt alltid på kluster
En strategi är att lägga till flera sekundärservrar alltid på Tillgänglighetsgruppen. Du måste lägga till dem i en ny molntjänst och uppdatera lyssnaren med den nya IP-belastning belastningsutjämnaren.

##### <a name="points-of-downtime"></a>Punkter stillestånd:
* Klusterverifieringen.
* Testa alltid på redundans för nya sekundärservrar.

Om du använder Windows lagringspooler inifrån den virtuella datorn ha högre i/o-genomflöde kommer sedan dessa att kopplas från under en fullständig verifiering av klustret. Verifieringstest krävs när du lägger till noder i klustret. Den tid det tar för att köra testet kan variera, så bör du testa i din testmiljö för att få en ungefärlig tid för hur lång tid detta tar.

Du måste etablera tid där du kan utföra manuell redundans och chaos testning på de nytillagda noderna för att säkerställa alltid på hög tillgänglighet fungerar som förväntat.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Du bör avsluta alla instanser av SQL Server där lagringspoolen används innan verifieringen körs.
>
> ##### <a name="high-level-steps"></a>Anvisningar
>

1. Skapa två nya SQL-servrar i ny molntjänst med anslutna Premium-lagring.
2. Kopiera över fullständig säkerhetskopiering och återställning med **NORECOVERY**.
3. Kopiera över 'utanför user DB' beroende objekt, till exempel inloggningar osv.
4. Skapa en ny intern belastning belastningsutjämnare (ILB) eller använda en extern belastningen belastningsutjämnare (ELB) och sedan ställa in belastningen belastningsutjämnade slutpunkter på både nya noder.

   > [!NOTE]
   > Kontrollera att alla noder har rätt slutpunktskonfigurationen innan du fortsätter
   >
   >
5. Stoppa användare/Application åtkomst till SQL Server (om du använder lagringspooler).
6. Stoppa SQL Server Engine-tjänster på alla noder (om du använder lagringspooler).
7. Lägga till nya noder för att klustret och köra fullständig verifiering.
8. När verifieringen är klar startar du alla SQL Server-tjänster.
9. Säkerhetskopiera transaktionsloggar och återställa databaserna.
10. Lägg till nya noder i alltid på Tillgänglighetsgruppen och placera replikering till **synkron**.
11. Lägg till IP-adressresurs för den nya Cloud Service ILB/ELB via PowerShell för Always On-baserad på flera platser exemplet i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). I Windows-kluster, ange den **möjliga ägare** av den **IP-adress** resurs till nya noder gamla. Se avsnittet ”lägga till IP-adressresurs i samma undernät, i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Redundans till en ny nod.
13. Se nya noder automatiskt Redundanspartners och testa redundans.
14. Ta bort ursprungliga noder från Tillgänglighetsgruppen.

##### <a name="advantages"></a>Fördelar
* Ny SQL-servrar kan vara testas (SQL Server och program) innan de läggs till Always On.
* Du kan ändra storleken på virtuella datorn och anpassa lagringsplatsen till de exakta kraven. Det skulle dock vara bra att bevara alla sökvägar för SQL-filen.
* Du kan styra när överföringen av DB-säkerhetskopiering till de sekundära replikerna har startats. Detta skiljer sig från att använda Azure **Start AzureStorageBlobCopy** cmdleten igen för att kopiera virtuella hårddiskar, eftersom det är en asynkron kopia.

##### <a name="disadvantages"></a>Nackdelar
* När du använder Windows-lagringspooler finns klusterdriftstopp under fullständig Klustervalideringen för nya ytterligare noder.
* Beroende på SQL Server-Version och befintliga antalet sekundära repliker kanske du inte kan lägga till flera sekundära repliker utan att ta bort befintliga sekundärservrar.
* Det kan finnas långa SQL dataöverföringstid när du konfigurerar sekundära.
* Det finns ytterligare kostnad under migreringen när du har nya datorer som körs parallellt.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrera till ett nytt alltid på kluster
En annan strategi är att skapa en helt ny alltid på klustret med helt nya noder i ny molntjänst och sedan dirigera klienterna för att använda den.

##### <a name="points-of-downtime"></a>Punkter stillestånd
Det finns en avbrottstid när du överför program och användare till alltid på lyssnaren. Avbrottstiden beror på:

* Den tid det tar att återställa säkerhetskopior av slutliga transaktionsloggen för databaser på nya servrar.
* Den tid det tar att uppdatera klientprogram att använda alltid på lyssnaren.

##### <a name="advantages"></a>Fördelar
* Du kan testa faktiska produktionsmiljön, SQL Server och Operativsystemets build-ändringar.
* Har du möjlighet att anpassa lagring och minska storleken för den virtuella datorn. Det kan leda till minskad kostnaden.
* Under den här processen kan du uppdatera din version eller SQL Server-version. Du kan också uppgradera operativsystemet.
* Tidigare alltid på klustret kan fungera som ett fast rollback-mål.

##### <a name="disadvantages"></a>Nackdelar
* Du behöver ändra lyssnaren DNS-namn om du vill att båda Always On-kluster körs samtidigt. Detta lägger till administration försämras under migreringen som klienten programmet strängar måste namnet ny lyssnare.
* Du måste implementera en synkroniseringsmekanism för mellan två miljöer att hålla dem så nära som möjligt för att minimera slutlig synkronisering kraven innan migreringen.
* Det läggs till kostnad under migreringen när du har den nya miljön som körs.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrera alltid på distributioner för minimal driftstörning
Det finns två metoder för att migrera Always On-distributioner för minimal driftstörning:

1. **Använda en befintlig sekundär: enskild plats**
2. **Använda befintlig sekundär tillgänglighetsreplik(er): flera platser**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Använda en befintlig sekundär: enskild plats
En strategi för minimal driftstörning är att ta ett befintligt moln sekundära och ta bort den från den aktuella Molntjänsten. Kopiera de virtuella hårddiskarna till det nya kontot i Premium-lagring, och skapa den virtuella datorn i en ny molntjänst. Uppdatera lyssnare i kluster och växling vid fel.

##### <a name="points-of-downtime"></a>Punkter stillestånd
* Det finns avbrottstid när du uppdaterar den sista noden med belastningsutjämnade-slutpunkten.
* Klient-återanslutning kan fördröjas beroende på din klient/DNS-konfiguration.
* Det finns ytterligare driftstopp om du väljer att ta alltid på klustergruppen offline för att byta ut de IP-adresserna. Du kan undvika detta genom att använda ett beroende eller och möjliga ägare för den tillagda IP-adressresursen. Se avsnittet ”lägga till IP-adressresurs i samma undernät, i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Om du vill att den tillagda noden till partake i som alltid på Failover-Partner som du behöver lägga till en Azure-slutpunkt med en referens till den belastningen den belastningsutjämnade uppsättningen. När du kör den **Lägg till AzureEndpoint** kommandot för att göra detta, aktuella anslutningen förbli öppen, men nya anslutningar till lyssnaren kommer inte att kunna upprättas förrän belastningsutjämnaren har uppdaterats. Testning detta påträffades till senaste 90-120seconds detta bör testas.
>
>

##### <a name="advantages"></a>Fördelar
* Inga extra kostnaden under migreringen.
* En-till-en migrering.
* Minskad komplexitet.
* Ger ökad IOPS från Premium Storage SKU: er. När diskarna är frånkopplat från den virtuella datorn och kopieras till den nya Molntjänsten en 3 part kan du använda verktyget för att öka storleken på VHD som ger högre genomflöden. Öka storleken på VHD finns i följande [forumdiskussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nackdelar
* Det finns en temporär förlust av hög tillgänglighet och Katastrofåterställning under migreringen.
* Eftersom det är en 1:1-migrering, måste du använda en minsta VM-storlek som stöder antal virtuella hårddiskar, så du kan inte downsize dina virtuella datorer.
* Det här scenariot använder Azure **Start AzureStorageBlobCopy** cmdlet, som är asynkron. Det finns inga SLA på Kopiera slutförande. Tiden för kopiorna varierar, medan det beror på vänta i kön beror också på hur mycket data att överföra. Kopiera tid ökar om överföringen kommer att en annan Azure-datacenter som har stöd för Premium-lagring i en annan region. Om du har precis 2 noder kan du en möjlig lösning om kopian tar längre tid än vid testning. Detta kan inkludera följande idéer.
  * Lägg till en tillfällig 3 SQL Server-nod för hög tillgänglighet innan migreringen överenskomna avbrott.
  * Kör migrering utanför Azure schemalagt underhåll.
  * Kontrollera att du har konfigurerat klustrets kvorum korrekt.  

##### <a name="high-level-steps"></a>Anvisningar
Det här dokumentet visar inte ett komplett exempel slutpunkt till slutpunkt, men den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) innehåller information som kan utnyttjas för att utföra detta.

![MinimalDowntime][8]

* Samla in diskkonfigurationen och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett premiumlagringskonto-lagring och kopiera virtuella hårddiskar från standardlagringskontot
* Skapa ny molntjänst och distribuera SQL2 VM i Molntjänsten. Skapa den virtuella datorn med den kopierade ursprungliga OS virtuell Hårddisk och bifoga de kopierade virtuella hårddiskarna.
* Konfigurera ILB / ELB och lägga till slutpunkter.
* Uppdatera lyssnare genom att antingen:
  * Koppla alltid på gruppen och uppdaterar alltid på lyssnaren med nya ILB / ELB IP-adress.
  * Eller lägga till IP-adressresurs av nya Cloud Service ILB/ELB via PowerShell i Windows-kluster. Sedan ange IP-adressresurs möjliga ägare till noden migrerade SQL2, och ange detta eller beroende i nätverksnamn. Se avsnittet ”lägga till IP-adressresurs i samma undernät, i den [bilaga](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Kontrollera DNS-konfiguration/spridningsuppgift till klienterna.
* Migrera SQL1 VM och gå igenom steg 2 – 4.
* Om du använder steg 5ii, Lägg sedan till SQL1 som möjlig ägare för den tillagda IP-adressresurs
* Redundanstestningen.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Använda befintlig sekundär tillgänglighetsreplik(er): flera platser
Om du har noder i mer än en Azure-datacenter (DC) eller om du har en hybridmiljö kan du använda en Always On-konfiguration i den här miljön för att minimera driftavbrott.

Tillvägagångssättet är att ändra Always On-synkronisering till synkron för lokal eller sekundära Azure Domänkontrollant och sedan redundans över till den SQL Server. Kopiera de virtuella hårddiskarna till en Premium Storage-konto, och distribuera datorn till en ny molntjänst. Uppdatera lyssnaren och växlar sedan tillbaka.

##### <a name="points-of-downtime"></a>Punkter stillestånd
Avbrottstiden består av tiden till redundans till alternativa DC och tillbaka. Det beror också på klienten eller DNS-konfigurationen och klient-återanslutning kan vara fördröjd.
Överväg följande exempel på en hybrid Always On-konfiguration:

![MultiSite1][9]

##### <a name="advantages"></a>Fördelar
* Du kan använda befintliga infrastruktur.
* Du har möjlighet att före uppgradering Azure storage på domänkontrollanten DR Azure först.
* DR Azure DC-lagring kan konfigureras.
* Det finns minst två växling vid fel under migreringen, exklusive redundanstestning.
* Du behöver inte flytta SQL Server-data med säkerhetskopiering och återställning.

##### <a name="disadvantages"></a>Nackdelar
* Beroende på klientåtkomst till SQL Server, kan det finnas ökad latens när SQL Server körs i en annan Domänkontrollant till programmet.
* Kopiera tidpunkten för virtuella hårddiskar till Premium-lagring kan vara lång. Detta kan påverka ditt beslut om om du vill behålla noden i Tillgänglighetsgruppen. Tänk på detta för när loggen arbetar belastningar som körs under migreringen krävs, eftersom den primära noden måste ha unreplicated transaktioner i dess transaktionsloggen. Därför kan detta växa avsevärt.
* Det här scenariot använder Azure **Start AzureStorageBlobCopy** cmdlet, som är asynkron. Det finns inga SLA på slutförande. Tiden kopior kan variera medan detta beror på vänta i kön, det beror på hur mycket data att överföra. Därför ha bara en nod i datacentret 2, du bör vidta säkerhetsåtgärder om kopian tar längre tid än vid testning. Detta kan inkludera följande idéer.
  * Lägg till en tillfällig SQL-nod 2 för hög tillgänglighet innan migreringen överenskomna avbrott.
  * Kör migrering utanför Azure schemalagt underhåll.
  * Kontrollera att du har konfigurerat klustrets kvorum korrekt.

Det här scenariot förutsätter att du har dokumenterats din installation och vet hur lagring mappas för att göra ändringar för den diskens cacheinställningarna.

##### <a name="high-level-steps"></a>Anvisningar
![Multisite2][10]

* Se lokalt / Azure DC för SQL Server-primära alternativ, och det andra automatisk redundans Partner (AFP).
* Samla in information om diskkonfiguration från SQL2 och ta bort noden (ta inte bort anslutna virtuella hårddiskar).
* Skapa ett premiumlagringskonto-lagring och kopiera virtuella hårddiskar från standardlagringskontot.
* Skapa en ny molntjänst och skapa den virtuella datorn SQL2 med dess lagring Premier-diskar som är anslutna.
* Konfigurera ILB / ELB och lägga till slutpunkter.
* Uppdatera alltid på lyssnaren med nya ILB / ELB IP-adress och testa redundans.
* Kontrollera DNS-konfigurationen.
* Ändra AFP till SQL2, och migrera SQL1 och gå igenom steg 2 – 5.
* Redundanstestningen.
* Växla AFP tillbaka till SQL1 och SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Bilaga: Migrera en Multisite alltid på klustret till Premium-lagring
Resten av det här avsnittet innehåller ett detaljerat exempel konvertera en Always On kluster på flera platser på Premium-lagring. Den konverterar också lyssnaren från att använda en extern belastningsutjämnare (ELB) till en intern belastningsutjämnare (ILB).

### <a name="environment"></a>Miljö
* Windows 2k 12 / SQL 2k 12
* 1 DB-filer på SP
* 2 x lagringspooler per nod

![Appendix1][11]

### <a name="vm"></a>VM:
I det här exemplet ska vi visa flyttas från en ELB till ILB. ELB fanns innan ILB, så att det visar hur du växlar till detta under migreringen.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pre steg: Ansluta till prenumeration
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Steg 1: Skapa nytt Lagringskonto och Molntjänsten
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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Steg 2: Öka tillåtna fel för resurser<Optional>
På vissa resurser som hör till din Always On-Tillgänglighetsgruppen finns begränsningar på hur många fel som kan uppstå under en period där Klustertjänsten försöker starta om resursgruppen. Det rekommenderas att du ökar det här medan du gå igenom den här proceduren sedan om du manuellt inte växling vid fel och utlösare redundans genom att stänga av datorer som du kan hämta nära den här gränsen.

Det skulle vara klokt fördubbla fel ersättning för att göra detta i hanteraren för redundanskluster, gå till egenskaperna för resursgruppen Always On:

![Appendix3][13]

Ändra maximalt antal misslyckanden till 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Steg 3: Lägga IP-adressresurs för klustergruppen<Optional>
Om du bara ha en IP-adress för klustergruppen och detta justeras till undernätet för molnet, Tänk, om du av misstag koppla från alla noder i klustret i molnet på nätverket sedan klustrets IP-resurs och Klusternätverksnamnet kommer inte att kunna anslutas. Vid detta hindrar det uppdateringar till andra resurser i klustret.

#### <a name="step-4-dns-configuration"></a>Steg 4: DNS-konfiguration
För att implementera en mjuk övergång beror på hur DNS används utnyttjade och uppdateras.
När alltid är installerat, skapas en klusterresurs för Windows-grupp om du öppnar Klusterhanteraren för växling visas att åtminstone den har tre resurser, de två som dokumentet refererar till är:

* Virtuellt nätverksnamn (VNN) – detta är DNS-namnet som klienten ansluta till om du vill ansluta till SQL-servrar via alltid på.
* IP-adressresurs – detta är den IP-adressen som är associerade med VNN, du kan ha flera och i multisitekonfigurationen har du en IP-adress per plats-undernät.

När du ansluter till SQL Server, SQL Server Client drivrutinen hämtar DNS-poster som är associerade med lyssnaren och försök att ansluta till varje alltid på associerade IP-adress, diskuterar nedan vi några faktorer som kan påverka detta.

Antalet samtidiga DNS-poster som är associerade med grupplyssnarens namn beror inte bara på antalet IP-adresser som är associerade, men ' RegisterAllIpProviders'setting i redundanskluster för Always ON VNN resursen.

När du distribuerar alltid på i Azure finns på olika steg för att skapa lyssnare och IP-adresser, måste du manuellt konfigurerar RegisterAllIpProviders 1, detta skiljer sig till en lokal alltid på distribution där det redan har angetts till 1.

'RegisterAllIpProviders' är 0, och sedan visas bara en DNS-post i DNS som är associerade med lyssnare:

![Appendix4][14]

Om 'RegisterAllIpProviders' är 1:

![Appendix5][15]

Koden nedan kommer dump ut VNN inställningarna och ange du, Observera för att ändringarna ska börja gälla behöver du kopplar VNN och aktivera det igen, det här tar den lyssnare offline orsakar avbrott för anslutning av klienten.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

I ett senare migreringssteg behöver du uppdatera Always On-lyssnaren med en uppdaterad IP-adress som ska referera till en belastningsutjämnare detta kommer att omfatta en IP-adress resurs borttagning och tillägg. När IP-uppdateringen måste du se till att den nya IP-adressen har uppdaterats i DNS-zonen och att klienterna uppdaterar sin lokala DNS-cacheminnet.

Om klienterna finns i olika nätverkssegment och referera till en annan DNS-server, måste du vad händer om DNS-zonöverföring under migreringen, eftersom programmet återansluta kommer inte begränsas av zonen överför tiden för alla nya IP-adresser för lyssnaren. Om du är under tidsbegränsning här ska du diskutera testa att tvinga en inkrementell zonöverföring med Windows-grupper och också DNS-värdpost till en lägre Time To Live (TTL), så uppdateras. Mer information finns i [inkrementella zonöverföringar](https://technet.microsoft.com/library/cc958973.aspx) och [Start DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Som standard TTL-värde för DNS-post som är kopplad till lyssnare i alltid på i Azure är 1200 sekunder. Kan du minska detta om du är i gång begränsningen under migreringen så klienterna uppdaterar sina DNS med den uppdaterade IP-adressen för lyssnaren. Du kan se och ändra konfigurationen av dumpning ut konfigurationen av VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Observera Ju lägre den 'HostRecordTTL', kommer att ske i en större mängd DNS-trafik.

##### <a name="client-application-settings"></a>Inställningar för program
Om din SQL-klientprogrammet stöder .net 4.5 SQLClient och du kan använda ' MULTISUBNETFAILOVER = TRUE ”nyckelord, det rekommenderas att tillämpas, eftersom den kan användas för snabbare anslutning till SQL Always On-Tillgänglighetsgruppen under växling vid fel. Den räknar igenom alla IP-adresser som är associerade med Always On-lyssnaren parallellt och utför en mer aggressiv TCP försök anslutningshastighet under en växling vid fel.

Mer information om inställningarna ovan finns [MultiSubnetFailover nyckelord och associerade funktioner](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Se även [SqlClient-stöd för hög tillgänglighet och katastrofåterställning](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Steg 5: Inställningarna för klusterkvorum
Du kommer att ta reda på minst en SQL Server anges i taget, du bör ändra inställningen klustrets kvorum, om du använder filen filresurs vittne (FSW) med 2-noder, bör du ange kvorum för att tillåta Nodmajoritet och använda dynamiska röstning , och detta är att en nod ska vara stående.

    Set-ClusterQuorum -NodeMajority  

Mer information om att hantera och konfigurera klustrets kvorum finns [konfigurera och hantera kvorum i ett redundanskluster för Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Steg 6: Extrahera befintliga slutpunkter och ACL: er
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Spara dem till en textfil.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Steg 7: Ändra Redundanspartners och replikering lägen
Om du har mer än 2 SQL-servrar kan du ändra redundans för en annan sekundär i en annan Domänkontrollant eller lokalt till 'Synkron' och göra det en automatisk redundans Partner (AFP), detta är så att du kan hantera HA medan du gör ändringar. Du kan göra detta via TSQL av dock ändra SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Steg 8: Ta bort sekundära virtuella datorn från Molntjänsten
Du bör planera att migrera en sekundär molnnod först om det är för närvarande primära, bör du initiera en manuell växling vid fel.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 9: Ändra disken cacheinställningar i CSV-filen och spara
För datavolymer som ska dessa anges till READONLY.

För volymer som TLOG ska dessa anges till NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Steg 10: Kopiera virtuella hårddiskar
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



Du kan kontrollera status för kopia av de virtuella hårddiskarna till Premium Storage-konto:

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

![Appendix8][18]

Vänta tills alla dessa registreras åtgärden lyckades.

Information för enskilda BLOB:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Steg 11: Registrera OS-disk
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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Steg 12: Importera sekundär till ny molntjänst
Koden nedan använder också alternativet tillagda här kan du importera datorn och använda retainable VIP.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Steg 13: Skapa ILB på nya molntjänster Svc lägga till belastningen belastningsutjämnade slutpunkter och ACL: er
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

#### <a name="step-14-update-always-on"></a>Steg 14: Uppdatera alltid på
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Nu ska du ta bort den gamla Molntjänsten IP-adress.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Steg 15: DNS-uppdateringskontroll
Du bör nu se DNS-servrar på SQL Server-klient-nätverk och kontrollera att kluster har lagt till extra värdpost för IP-adress som lagts till. Om dessa DNS-servrar som inte har uppdaterat, Överväg att tvinga en DNS-zonöverföring och se till att klienterna i det undernätet är kan matcha till båda alltid på IP-adresser, det är så du inte behöver vänta på att automatisk DNS-replikeringen.

#### <a name="step-16-reconfigure-always-on"></a>Steg 16: Konfigurera om alltid på
Då vänta du på sekundärt noden som migrerades att fullständigt omsynkronisering med den lokala noden och växla till synkron replikeringsnod och göra det i AFP.  

#### <a name="step-17-migrate-second-node"></a>Steg 17: Migrera andra noden
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Steg 18: Ändra disken cacheinställningar i CSV-filen och spara
För datavolymer som ska dessa anges till READONLY.

För volymer som TLOG ska dessa anges till NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Steg 19: Skapa nytt oberoende Lagringskonto för sekundär nod
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

#### <a name="step-20-copy-vhds"></a>Steg 20: Kopiera virtuella hårddiskar
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


Du kan kontrollera VHD-kopian status för alla virtuella hårddiskar: ForEach ($disk i $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Disketikett $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Vänta tills alla dessa registreras åtgärden lyckades.

Information för enskilda BLOB:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Steg 21: Registrera OS-disk
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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Steg 22: Lägg till belastningen belastningsutjämnade slutpunkter och ACL: er
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Steg 23: Testa redundans
Nu bör du låta den migrerade noden synkroniseras med lokala alltid på noden och placera den i synkron replikering läge och vänta tills den är synkroniserad. Sedan migreras växling från lokal till den första noden, vilket är AFP. När som har arbetat ändra sista migrerade noden till AFP.

Du bör redundanstestningen mellan alla noder och kör om chaos testerna för att se till att redundans fungerar som förväntat och en rimlig manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Steg 24: Lägga tillbaka inställningarna för klusterkvorum / DNS TTL / Failover Pntrs / synkroniseringsinställningar
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Lägger till IP-adressresurs på samma undernät
Om du har bara 2 SQL-servrar och vill migrera dem till en ny molntjänst, men vill behålla dem i samma undernät, kan du undvika tar lyssnaren offline för att ta bort den ursprungliga alltid på IP-adressen och lägga till den nya IP-adressen. Om du migrerar de virtuella datorerna till ett annat undernät behöver du inte göra detta som en ytterligare klusternätverk som kommer att referera till det undernätet.

När du har växa upp den migrerade sekundärt och läggas till i den nya resursen IP-adressen för den nya Molntjänsten innan redundans den befintliga primärt, bör du vidta dessa åtgärder inom klustret Failover Manager:

Om du vill lägga till i IP-adress, finns det [bilaga](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), steg 14.

1. Ändra möjliga ägare till 'befintliga primära SQL Server-i exemplet nedan, 'dansqlams4' för den aktuella resursen i IP-adress:

    ![Appendix13][23]
2. Ändra möjliga ägare till 'Migrerats sekundära SQL Server ”i exemplet nedan, 'dansqlams5' för den nya resursen i IP-adress:

    ![Appendix14][24]
3. När det här värdet kan du redundans och när den sista noden migreras möjliga ägare måste redigeras så noden läggs till som möjlig ägare:

    ![Appendix15][25]

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure Premium-lagring](../premium-storage.md)
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
