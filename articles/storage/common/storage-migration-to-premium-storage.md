---
title: Migrera virtuella datorer till Azure Premium Storage | Microsoft Docs
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage. Premium Storage erbjuder stöd för diskar med höga prestanda och låg latens för I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines.
services: storage
author: yuemlu
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.subservice: common
ms.openlocfilehash: d42183e1db49850afc115fcb5645baf7290cf3c8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477614"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrera till Azure Premium Storage (ohanterade diskar)

> [!NOTE]
> Den här artikeln beskriver hur du migrerar en virtuell dator som använder ohanterade diskar till en virtuell dator som använder ohanterade premium-diskar som standard. Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar. Hanterade diskar referensen underliggande lagringskonton så att du inte behöver. Mer information finns i vår [översikten över Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage tillhandahåller högpresterande och låg latens disksupport för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Du kan dra nytta av hastigheten och prestandan för dessa diskar genom att migrera VM-diskar för ditt program till Azure Premium Storage.

Syftet med den här guiden är att hjälpa nya användare i Azure Premium Storage bättre förbereda för att få en smidig övergång från sina nuvarande system till Premium Storage. Guiden löser tre av de viktigaste komponenterna i den här processen:

* [Planera för migrering till Premium Storage](#plan-the-migration-to-premium-storage)
* [Förbereda och kopiera virtuella hårddiskar (VHD) till Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Skapa Azure-dator med Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Du kan migrera virtuella datorer från andra plattformar till Azure Premium Storage, eller så kan du migrera befintliga virtuella Azure-datorer från standardlagring till Premium Storage. Den här guiden beskriver steg för båda två scenarier. Följ stegen som anges i avsnittet relevanta beroende på ditt scenario.

> [!NOTE]
> Du hittar en översikt över funktioner och priser för Premium Storage i Premium Storage: [Lagring med höga prestanda för Azure-Datorbelastningar](../../virtual-machines/windows/premium-storage.md). Vi rekommenderar att du migrerar alla virtuella diskar som kräver hög IOPS till Azure Premium Storage för bästa prestanda för ditt program. Om disken inte kräver hög IOPS, kan du begränsa kostnaderna genom att i Standard-lagring som lagrar data för virtuell dator-disk på hårddiskar (HDD) i stället för SSD-enheter.
>

Du har slutfört migreringen i sin helhet kan kräva ytterligare åtgärder både före och efter stegen i den här guiden. Exempel är Konfigurera virtuella nätverk eller slutpunkter eller genom att göra ändringar i koden själva programmet som kan kräva att vissa avbrott i ditt program. De här åtgärderna är unika för varje program och bör du genomföra dem tillsammans med stegen i den här guiden för att göra fullständiga övergången till Premium Storage så smidig som möjligt.

## <a name="plan-the-migration-to-premium-storage"></a>Planera för migrering till Premium Storage
Det här avsnittet säkerställer att du är redo att följa stegen i migreringen i den här artikeln och hjälper dig att fatta det bästa beslutet på virtuell dator och Disk-typer.

### <a name="prerequisites"></a>Förutsättningar
* Du behöver en Azure-prenumeration. Om du inte har någon kan du skapa en månads [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) prenumeration eller besök [priser för Azure](https://azure.microsoft.com/pricing/) fler alternativ.
* För att köra PowerShell-cmdlets, måste Microsoft Azure PowerShell-modulen. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* När du planerar att använda virtuella Azure-datorer med Premium Storage som du behöver använda Premium Storage kan virtuella datorer. Du kan använda både Standard och Premium Storage-diskar med Premium Storage kan virtuella datorer. Premium storage-diskar är tillgängliga med flera typer av virtuella datorer i framtiden. Mer information om alla tillgängliga Azure VM-disktyper och storlekar finns i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [storlekar för Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Överväganden
En Azure-dator stöder koppla flera Premium Storage-diskar så att dina program kan ha upp till 256 TB lagringsutrymme per virtuell dator. Med Premium Storage kan kan dina program uppnå 80 000 IOPS (antal input/output-åtgärder per sekund) per virtuell dator och 2 000 MB per sekund diskdataflöde per virtuell dator med extremt låg fördröjning för läsåtgärder. Har du alternativ på en mängd olika virtuella datorer och diskar. Det här avsnittet är att hjälpa dig att hitta ett alternativ som bäst passar din arbetsbelastning.

#### <a name="vm-sizes"></a>VM-storlekar
Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Granska prestandaegenskaperna för virtuella datorer med Premium Storage och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Se till att det finns tillräckligt mycket bandbredd på den virtuella datorn att driva trafiken disk.

#### <a name="disk-sizes"></a>Diskstorlekar
Det finns fem typer av diskar som kan användas med den virtuella datorn och alla har specifika IOPs och dataflöde gränser. Ta hänsyn till dessa gränser när välja typ av disk för den virtuella datorn baserat på dina behov för ditt program när det gäller kapacitet, prestanda, skalbarhet och högsta läses in.

| Typen för Premium-diskar  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Diskstorlek           | 128 GB| 512 GB| 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) | 
| IOPS per disk       | 500   | 2 300  | 5000           | 7500           | 7500           | 
| Dataflöde per disk | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

Beroende på arbetsbelastningen, avgör du om det krävs ytterligare datadiskar för den virtuella datorn. Du kan koppla flera beständiga datadiskar till den virtuella datorn. Om det behövs kan du stripe över diskar att öka kapaciteten och prestandan för volymen. (Se vad som är Disk Striping [här](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Om du stripe-datadiskar i Premium Storage med hjälp av [lagringsutrymmen][4], bör du konfigurera den med en kolumn för varje disk som används. Annars vara prestandan stripe-volym lägre än förväntat pga en ojämn fördelning av trafik på diskarna. För virtuella Linux-datorer kan du använda den *mdadm* verktyg för att göra samma sak. Se artikeln [konfigurera programvaru-RAID på Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information.

#### <a name="storage-account-scalability-targets"></a>Skalbarhetsmål för lagringskontot
Premium Storage-konton har följande skalbarhetsmål utöver den [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md). Om dina programkrav överstiger det för skalbarhetsmål för ett enda lagringskonto, ett program som du använder flera lagringskonton och partitionera dina data på dessa lagringskonton.

| Total kapacitet | Totala bandbredden för ett lokalt Redundant Lagringskonto |
|:--- |:--- |
| Kapacitet för disk: 35TB<br />Kapacitet för ögonblicksbilder: 10 TB |Upp till 50 Gigabit per sekund för inkommande och utgående |

Mer information om specifikationer för Premium Storage, Kolla in [skalbarhets- och prestandamål när du använder Premiumlagring](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Disk-principen för cachelagring
Som standard disken Cachelagringsprincip är *skrivskyddad* för alla Premium datadiskar, och *skrivskyddad* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. Inaktivera diskcachelagring så att du kan få bättre prestanda för hög eller lässkyddad datadiskar (till exempel loggfiler för SQL Server). Inställningar för cachelagring för befintliga datadiskar kan uppdateras med hjälp av [Azure-portalen](https://portal.azure.com) eller *- HostCaching* -parametern för den *Set-AzureDataDisk* cmdlet.

#### <a name="location"></a>Plats
Välj en plats där Azure Premium Storage är tillgängligt. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser. Virtuella datorer finns i samma region som lagringskontot som lagrar diskarna för den virtuella datorn får mycket bättre prestanda än om de finns i olika områden.

#### <a name="other-azure-vm-configuration-settings"></a>Andra Virtuella Azure-konfigurationsinställningar
När du skapar en Azure-dator kan blir du ombedd att konfigurera vissa inställningar för virtuell dator. Kom ihåg att vissa inställningar är fasta för livslängden för den virtuella datorn, samtidigt som du kan ändra eller lägga till andra senare. Granska dessa konfigurationsinställningar för virtuella Azure-datorn och se till att de är konfigurerade på rätt sätt för att matcha dina krav på arbetsbelastningen.

### <a name="optimization"></a>Optimering
[Azure Premium Storage: Design för hög prestanda](../../virtual-machines/windows/premium-storage-performance.md) innehåller riktlinjer för att skapa program med höga prestanda med hjälp av Azure Premium Storage. Du kan följa riktlinjerna i kombination med bästa praxis för prestanda gäller för tekniker som används av ditt program.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Förbereda och kopiera virtuella hårddiskar (VHD) till Premium Storage
Följande avsnitt innehåller riktlinjer för att förbereda virtuella hårddiskar från den virtuella datorn och kopiera virtuella hårddiskar till Azure Storage.

* [Scenario 1: ”Jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage”.](#scenario1)
* [Scenario 2: ”Jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage”.](#scenario2)

### <a name="prerequisites"></a>Förutsättningar
Om du vill förbereda de virtuella hårddiskarna för migrering behöver du:

* En Azure-prenumeration, ett lagringskonto och en behållare i det lagringskontot som du kan kopiera en virtuell Hårddisk. Observera att mållagringskontot kan vara ett Standard- eller Premium Storage-konto utifrån dina behov.
* Ett verktyg för att generalisera den virtuella Hårddisken om du planerar att skapa flera VM-instanser från den. Till exempel sysprep för Windows eller virt-sysprep för Ubuntu.
* Ett verktyg för att ladda upp VHD-filen till lagringskontot. Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) eller Använd en [Azure Lagringsutforskaren](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Den här guiden beskriver kopierar en virtuell Hårddisk med hjälp av AzCopy-verktyget.

> [!NOTE]
> Om du väljer alternativet för synkron kopia med AzCopy, kopierar du en virtuell Hårddisk genom att köra ett av dessa verktyg från en Azure virtuell dator som är i samma region som mållagringskontot för optimala prestanda. Om du kopierar en virtuell Hårddisk från en Azure virtuell dator i en annan region, gå din långsammare.
>
> Kopiera en stor mängd data över begränsad bandbredd, Överväg [med Azure Import/Export-tjänsten för att överföra data till Blob Storage](../storage-import-export-service.md); detta kan du överföra dina data genom att skicka hårddiskenheter till ett Azure-datacenter. Du kan använda Azure Import/Export-tjänsten för att kopiera data till ett standardlagringskonto endast. När data finns i standard storage-konto, du kan använda antingen den [kopiera Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) eller AzCopy för att överföra data till premium storage-konto.
>
> Observera att Microsoft Azure bara stöder VHD-filer med fast storlek. VHDX-filer eller dynamiska virtuella hårddiskar stöds inte. Om du har en dynamisk virtuell Hårddisk kan du konvertera den till fast storlek med hjälp av den [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet.
>
>

### <a name="scenario1"></a>Scenario 1: ”Jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage”.
Om du migrerar befintliga virtuella Azure-datorer, stoppa den virtuella datorn, förbereda virtuella hårddiskar per typ av virtuell Hårddisk som du vill ha och sedan kopiera den virtuella Hårddisken med AzCopy eller PowerShell.

Den virtuella datorn måste vara helt på att migrera ett rent tillstånd. Det är inte ett driftstopp förrän migreringen är klar.

#### <a name="step-1-prepare-vhds-for-migration"></a>Steg 1. Förbereda virtuella hårddiskar för migrering
Om du migrerar befintliga virtuella Azure-datorer till Premium Storage kan vara en virtuell Hårddisk:

* En generaliserad operativsystemsavbildning
* En unik operativsystemdisk
* En datadisk

Nedan går vi igenom dessa 3 scenarier för att förbereda en virtuell Hårddisk.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Använd en generaliserad virtuell Hårddisk med operativsystemet för att skapa flera Virtuella datorinstanser
Om du överför en virtuell Hårddisk som används för att skapa flera allmänna Azure VM-instanser måste du först generalisera virtuell Hårddisk med en sysprep-verktyget. Detta gäller för en virtuell Hårddisk som finns lokalt eller i molnet. Sysprep tar bort datorspecifik information från den virtuella Hårddisken.

> [!IMPORTANT]
> Ta en ögonblicksbild eller säkerhetskopiera den virtuella datorn innan du generalisera den. Köra sysprep ska stoppa och frigöra den Virtuella datorinstansen. Följ stegen nedan för att sysprep en VHD för Windows-operativsystem. Observera att köra kommandot Sysprep måste du stänga av den virtuella datorn. Mer information om Sysprep finns i [Sysprep översikt](https://technet.microsoft.com/library/hh825209.aspx) eller [Teknisk referens för Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Öppna ett kommandotolksfönster som administratör.
2. Ange följande kommando för att öppna Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. I systemförberedelseverktyget, väljer du ange System Out-of-Box Experience (OOBE), markerar du kryssrutan Generalize, markera **avstängning**, och klicka sedan på **OK**, enligt bilden nedan. Sysprep att generalisera operativsystemet och stänga av systemet.

    ![][1]

Använd virt sysprep för att göra samma sak för en Ubuntu-VM. Se [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) för mer information. Se även några av öppen källkod [Linux Serveretableringen programvara](http://www.cyberciti.biz/tips/server-provisioning-software.html) för andra Linux-operativsystem.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Använda en unik operativsystemet virtuell Hårddisk för att skapa en enda VM-instans
Om du har ett program som körs på den virtuella datorn som kräver specifika data för den dator kan du inte generalisera den virtuella Hårddisken. En icke-generaliserad virtuell Hårddisk kan användas för att skapa en unik Azure VM-instans. Till exempel om du har en domänkontrollant på en virtuell Hårddisk kan blir köra sysprep det ineffektiv som en domänkontrollant. Granska de program som körs på den virtuella datorn och effekten av att köra sysprep på dem. innan generaliserar den virtuella Hårddisken.

##### <a name="register-data-disk-vhd"></a>Registrera datadisk-VHD
Om du har datadiskar i Azure som ska migreras måste du kontrollera de virtuella datorer som använder diskarna data måste stängas.

Följ stegen nedan för att kopiera VHD till Azure Premium Storage och registrera den som en etablerade datadisk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa mål för en virtuell Hårddisk
Skapa ett lagringskonto för att underhålla de virtuella hårddiskarna. Tänk på följande när du planerar var du vill lagra dina VHD: er:

* Mål Premium storage-konto.
* Platsen för lagringskontot måste vara samma som Premium Storage kan virtuella Azure-datorer skapas i det sista steget. Du kan kopiera till ett nytt lagringskonto, eller planerar att använda samma lagringskonto utifrån dina behov.
* Kopiera och spara lagringskontots åtkomstnyckel för mållagringskontot för nästa steg.

Du kan välja att behålla vissa datadiskar i ett standardlagringskonto (till exempel diskar med mer lågfrekvent lagring) för datadiskar, men bör du flytta alla data för produktionsarbetsbelastning använda premium storage.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Steg 3. Kopiera virtuella Hårddisken med AzCopy eller PowerShell
Du behöver att hitta din behållare sökväg och lagringskontonyckeln att bearbeta något av de här två alternativen. Behållare-sökväg och lagringskontonyckeln hittar du i **Azure-portalen** > **Storage**. Behållaren för URL: en blir som ”https://myaccount.blob.core.windows.net/mycontainer/”.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Alternativ 1: Kopiera en virtuell Hårddisk med AzCopy (asynkron copy)
Med AzCopy kan överföra du enkelt den virtuella Hårddisken via Internet. Det kan ta tid beroende på storleken på de virtuella hårddiskarna. Kom ihåg att kontrollera lagringskontogränser för ingående/utgående trafik när du använder det här alternativet. Se [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md) mer information.

1. Hämta och installera AzCopy härifrån: [Senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från ”källa” till ”mål”.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Här följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/ Källa:  *&lt;källa&gt;:*** Platsen för mappen eller storage-behållarens Webbadress som innehåller den virtuella Hårddisken.
   * **/ SourceKey:  *&lt;källa kontonyckel&gt;:*** Lagringskontonyckel för källagringskontot.
   * **/ Dest:  *&lt;mål&gt;:*** Storage-behållarens Webbadress att kopiera den virtuella Hårddisken till.
   * **/DestKey: *&lt;dest-account-key&gt;:*** Lagringskontonyckel för mållagringskontot.
   * **/ Mönster:  *&lt;filnamn&gt;:*** Ange namnet på den virtuella Hårddisken för att kopiera.

Mer information om hur du använder AzCopy-verktyget, se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Alternativ 2: Kopiera en virtuell Hårddisk med PowerShell (Synchronized copy)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också kopiera VHD-filen med hjälp av PowerShell-cmdleten Start-AzStorageBlobCopy. Använd följande kommando på Azure PowerShell för att kopiera VHD. Ersätt värdena i <> med motsvarande värden från din käll- och storage-konto. Om du vill använda det här kommandot måste du ha en behållare som kallas virtuella hårddiskar i din mållagringskontot. Om behållaren inte finns, skapa en innan du kör kommandot.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exempel:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenario 2: ”Jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage”.
Om du migrerar virtuella Hårddisken från icke - Azure Cloud Storage till Azure, måste du först exportera den virtuella Hårddisken till en lokal katalog. Ha fullständig källsökvägen för den lokala katalogen där VHD lagras till hands och sedan använda AzCopy för att överföra den till Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Steg 1. Exportera virtuell Hårddisk till en lokal katalog
##### <a name="copy-a-vhd-from-aws"></a>Kopiera en virtuell Hårddisk från AWS
1. Om du använder AWS kan du exportera EC2-instans på en virtuell Hårddisk i en Amazon S3-bucket. Följ stegen som beskrivs i dokumentationen för Amazon för export av Amazon EC2-instanser att installera verktyget för Amazon EC2-kommandoradsgränssnittet (CLI) och kör kommandot create-instans-export-aktivitet för att exportera EC2-instans till en VHD-fil. Se till att använda **VHD** för DISKEN&#95;bild&#95;FORMAT variabeln när du kör den **skapa-instans-export-uppgift** kommando. Den exporterade VHD-filen sparas i en Amazon S3-bucket som du anger under den här processen.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Hämta VHD-filen från S3-bucket. Välja VHD-filen sedan **åtgärder** > **hämta**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiera en virtuell Hårddisk från andra icke-Azure-molnet
Om du migrerar virtuella Hårddisken från icke - Azure Cloud Storage till Azure, måste du först exportera den virtuella Hårddisken till en lokal katalog. Kopiera fullständig källsökvägen för den lokala katalogen där VHD lagras.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiera en VHD från en lokal plats
Om du migrerar virtuella Hårddisken från en lokal miljö, behöver fullständiga källsökvägen där VHD lagras. Sökvägen kan vara en server eller en filresurs.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa mål för en virtuell Hårddisk
Skapa ett lagringskonto för att underhålla de virtuella hårddiskarna. Tänk på följande när du planerar var du vill lagra dina VHD: er:

* Mållagringskontot kan vara standard eller premium storage beroende på dina behov.
* Region för lagringskonto måste vara samma som Premium Storage kan virtuella Azure-datorer skapas i det sista steget. Du kan kopiera till ett nytt lagringskonto, eller planerar att använda samma lagringskonto utifrån dina behov.
* Kopiera och spara lagringskontots åtkomstnyckel för mållagringskontot för nästa steg.

Vi rekommenderar starkt att du flyttar alla data för produktionsarbetsbelastning använda premium storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Steg 3. Överför den virtuella Hårddisken till Azure Storage
Nu när du har en virtuell Hårddisk i den lokala katalogen kan du använda AzCopy eller AzurePowerShell för att överföra VHD-filen till Azure Storage. Båda alternativen finns här:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Alternativ 1: Med hjälp av Azure PowerShell Add-AzureVhd för att överföra VHD-filen

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Ett exempel <Uri> kanske ***”https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd”***. Ett exempel <FileInfo> kanske ***”C:\path\to\upload.vhd”***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Alternativ 2: Använda AzCopy för att ladda upp VHD-filen
Med AzCopy kan överföra du enkelt den virtuella Hårddisken via Internet. Det kan ta tid beroende på storleken på de virtuella hårddiskarna. Kom ihåg att kontrollera lagringskontogränser för ingående/utgående trafik när du använder det här alternativet. Se [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md) mer information.

1. Hämta och installera AzCopy härifrån: [Senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från ”källa” till ”mål”.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Här följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/ Källa:  *&lt;källa&gt;:*** Platsen för mappen eller storage-behållarens Webbadress som innehåller den virtuella Hårddisken.
   * **/ SourceKey:  *&lt;källa kontonyckel&gt;:*** Lagringskontonyckel för källagringskontot.
   * **/ Dest:  *&lt;mål&gt;:*** Storage-behållarens Webbadress att kopiera den virtuella Hårddisken till.
   * **/DestKey: *&lt;dest-account-key&gt;:*** Lagringskontonyckel för mållagringskontot.
   * **/ BlobType: sidan:** Anger att målet är en sidblob.
   * **/ Mönster:  *&lt;filnamn&gt;:*** Ange namnet på den virtuella Hårddisken för att kopiera.

Mer information om hur du använder AzCopy-verktyget, se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att ladda upp en virtuell Hårddisk
Du kan också ladda upp en virtuell Hårddisk till storage-kontot med någon av följande metoder:

* [Azure Storage kopiering av Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer ladda upp BLOB](https://azurestorageexplorer.codeplex.com/)
* [Storage Import/Export Service REST API-referens](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Du rekommenderas att använda tjänsten importera/exportera om Uppskattat laddar upp tiden är längre än 7 dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) att uppskatta storlek och överför dataenheten tiden.
>
> Import/Export kan användas för att kopiera till ett standardlagringskonto. Du behöver att kopiera från standardlagring till premium storage-konto med ett verktyg som AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Skapa Azure virtuella datorer med Premium Storage
När den virtuella Hårddisken laddas upp till eller kopieras till önskat lagringskonto, följer du anvisningarna i det här avsnittet för att registrera den virtuella Hårddisken som en OS-avbildning, OS-disken beroende på ditt scenario och sedan skapa en VM-instans från den. Datadisk-VHD kan kopplas till den virtuella datorn när den har skapats.
Ett exempelskript för migrering finns i slutet av det här avsnittet. Det här enkla skriptet matchar inte alla scenarier. Du kan behöva uppdatera skriptet så att det överensstämmer med ditt specifika scenario. Se nedan för att se om det här skriptet gäller för ditt scenario, [A migrering exempelskriptet](#a-sample-migration-script).

### <a name="checklist"></a>Checklista
1. Vänta tills alla VHD-diskar som kopierar har slutförts.
2. Kontrollera att Premium Storage är tillgängligt i den region som du migrerar till.
3. Bestäm den nya VM-serien som du kommer att använda. Det bör vara en Premium-lagring som är kompatibla och storlek bör vara beroende på tillgänglighet i regionen och utifrån dina behov.
4. Bestämma den exakta VM-storlek som du ska använda. VM-storlek måste vara tillräckligt stor för att stödja antalet datadiskar som du har. T.ex. Om du har 4 datadiskar, måste den virtuella datorn ha minst 2 kärnor. Överväg även bearbetningskraft, minne och bandbredd i nätverket måste.
5. Skapa ett Premium Storage-konto i målregionen. Detta är det konto som du ska använda för den nya virtuella datorn.
6. Har den aktuella VM-informationen till hands, inklusive en lista över diskar och motsvarande VHD-blobbar.

Förbereda programmet för driftstopp. Du måste stoppa all bearbetning i det aktuella systemet om du vill göra en ren migrering. Du kan bara flytta det till konsekvent tillstånd som du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror på mängden data i diskar för att migrera.

> [!NOTE]
> Om du skapar en Azure Resource Manager-VM från en specialiserad virtuell hårddisk kan du läsa [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) för att distribuera Resource Manager-VM med hjälp av den befintliga disken.
>
>

### <a name="register-your-vhd"></a>Registrera en virtuell Hårddisk
Skapa en virtuell dator från OS-VHD eller för att koppla en datadisk till en ny virtuell dator, måste du först registrera dem. Följ stegen nedan beroende på din VHD-scenario.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserad operativsystemet VHD för att skapa flera Virtuella Azure-instanser
När generaliserad virtuell Hårddisk för OS-avbildning har laddats upp till storage-konto, kan du registrera den som en **Azure VM-avbildning** så att du kan skapa en eller flera VM-instanser från den. Använd följande PowerShell-cmdletar för att registrera en virtuell Hårddisk som en Azure VM OS-avbildning. Ange fullständig URL: en där VHD kopierades till.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopiera och spara namnet på den här nya Azure VM-avbildning. I exemplet ovan är det *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikt operativsystemet VHD för att skapa en Azure VM-instans
När den unika OS-VHD har överförts till storage-konto kan du registrera den som en **Azure OS-disken** så att du kan skapa en VM-instans från den. Använd dessa PowerShell-cmdletar för att registrera din VHD som en Azure-OS-Disk. Ange fullständig URL: en där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopiera och spara namnet på den här nya Azure-OS-disken. I exemplet ovan är det *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Datadisk-VHD som ska kopplas till nya Azure VM-instanser
När datadisken VHD har överförts till storage-konto, registrera den som en Azure-datadisk så att den kan kopplas till din nya DS-serien, DSv2-serien eller GS-serien Azure VM-instans.

Använd dessa PowerShell-cmdletar för att registrera en virtuell Hårddisk som en Azure-datadisk. Ange fullständig URL: en där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopiera och spara namnet på den här nya Azure-datadisk. I exemplet ovan är det *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Skapa en Premium Storage kompatibel virtuell dator
När OS-avbildning eller OS-disken är registrerade, skapa en ny DS-serien, DSv2-serien eller GS-serien virtuell dator. Du kommer att använda operativsystemavbildning eller operativsystemets Disknamn som du registrerat. Välj typ av virtuell dator från Premium Storage-nivå. I exemplet nedan använder du den *Standard_DS2* VM-storlek.

> [!NOTE]
> Uppdatera storleken att kontrollera att den matchar din Kapacitets- och prestandakrav och den tillgängliga Azure diskstorleken.
>
>

Följ steg för steg PowerShell-cmdlet nedan för att skapa den nya virtuella datorn. Ange först de gemensamma parametrarna:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Skapa först en molnbaserad tjänst där du ska vara värd för din nya virtuella datorer.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Skapa sedan Azure VM-instans från den OS-avbildning eller en OS-disken som du registrerat beroende på ditt scenario.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserad operativsystemet VHD för att skapa flera Virtuella Azure-instanser
Skapa i en eller flera nya DS-serien Azure VM-instanser med hjälp av den **Azure OS-avbildning** som du registrerat. Ange det här namnet för OS-avbildning i VM-konfigurationen när du skapar ny virtuell dator som visas nedan.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikt operativsystemet VHD för att skapa en Azure VM-instans
Skapa en ny DS serien virtuell Azure-dator instans med den **Azure OS-disken** som du registrerat. Ange det här namnet för OS-disken i VM-konfigurationen när du skapar den nya virtuella datorn enligt nedan.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Ange andra Virtuella Azure-information, till exempel en molntjänst, region, storage-konto, tillgänglighetsuppsättning och principen för cachelagring. Observera att den Virtuella datorinstansen måste vara samordnad med associerade operativsystem eller datadiskar, så att det valda moln service, region och lagring-kontot måste vara på samma plats som de underliggande virtuella hårddiskarna för dessa diskar.

### <a name="attach-data-disk"></a>Anslut en datadisk
Slutligen, om du har registrerat datadisk virtuella hårddiskar, bifoga dem nya Premium-lagring kan Azure-datorn.

Använd följande PowerShell-cmdlet för att ansluta datadisk till den nya virtuella datorn och ange principen för cachelagring. I exemplet nedan anges cachelagringsprincipen till *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Det kan finnas ytterligare steg behövs som stöder ditt program som inte omfattas av den här guiden.
>
>

### <a name="checking-and-plan-backup"></a>Markera och planera säkerhetskopiering
När den nya virtuella datorn är igång, få åtkomst till den med hjälp av samma inloggnings-id och lösenord är som den ursprungliga virtuella datorn och kontrollera att allt fungerar som förväntat. Alla inställningar, inklusive stripe-volymer kan finnas i den nya virtuella datorn.

Det sista steget är att planera säkerhetskopiering och underhållsschema för den nya virtuella datorn utifrån programmets behov.

### <a name="a-sample-migration-script"></a>Ett exempelskript för migrering
Om du har flera virtuella datorer att migrera kan göra det enklare automatisering med PowerShell-skript. Följande är ett exempelskript som automatiserar migreringen av en virtuell dator. Obs som skriptet nedan är bara ett exempel och det finns några antaganden som gjordes om de aktuella VM-diskarna. Du kan behöva uppdatera skriptet så att det överensstämmer med ditt specifika scenario.

Antaganden är:

* Du skapar klassiska virtuella Azure-datorer.
* Din källa OS-diskar och källdiskarna Data finns i samma lagringskonto och samma behållare. Om din OS och Datadiskar inte är på samma plats, kan du använda AzCopy eller Azure PowerShell för att kopiera virtuella hårddiskar över lagringskonton och behållare. Se föregående steg: [Kopiera virtuella Hårddisken med AzCopy eller PowerShell](#copy-vhd-with-azcopy-or-powershell). Redigera det här skriptet för att uppfylla ditt scenario är ett annat alternativ, men vi rekommenderar att du använder AzCopy eller PowerShell eftersom det är enklare och snabbare.

Automationsskript finns nedan. Ersätt texten med din information och uppdatera skriptet så att det överensstämmer med ditt specifika scenario.

> [!NOTE]
> Med hjälp av det befintliga skriptet bevaras inte om nätverkskonfigurationen på den Virtuella källdatorn. Behöver du re-config nätverksinställningarna på dina migrerade virtuella datorer.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimering
Din nuvarande konfiguration av virtuell dator kan anpassas specifikt för att fungera bra med standarddiskar. Till exempel att öka prestanda genom att använda många diskar i en stripe-volym. I stället för att använda separat 4 diskar på Premium-lagring, kan du till exempel att kunna optimerar kostnaderna genom att använda en skiva. Optimeringar som behovet av att hanteras på grundval av fall och kräver anpassade åtgärder efter migreringen. Observera även att den här processen inte fungerar bra för databaser och program som är beroende av disklayouten definierat i inställningarna.

##### <a name="preparation"></a>Förberedelse
1. Slutföra enkla migreringen enligt beskrivningen ovan. Optimeringar utförs på den nya virtuella datorn efter migreringen.
2. Definiera den nya diskstorleken som behövs för optimerad konfiguration.
3. Fastställa mappningen av aktuella diskar/volymer till de nya disken-specifikationerna.

##### <a name="execution-steps"></a>Utförande
1. Skapa nya diskar med rätt storlek på de virtuella datorer i Premium Storage.
2. Logga in på den virtuella datorn och kopiera data från den aktuella volymen till den nya disken som mappar till volymen. Gör detta för alla aktuella volymer som behöver för att mappa till en ny disk.
3. Sedan ändra programinställningarna att växla till nya diskar och koppla från de gamla volymerna.

Justera programmet för bättre prestanda för diskar finns i [optimera programprestanda](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrering
Databaser och andra avancerade program kan kräva särskilda åtgärder som definierats av program-providern för migreringen. Läs programdokumentationen för respektive. T.ex. vanligtvis databaser kan migreras med säkerhetskopiering och återställning.

## <a name="next-steps"></a>Nästa steg
Se följande resurser för specifika scenarier för migrering av virtuella datorer:

* [Migrera virtuella Azure-datorer mellan Lagringskonton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Skapa och ladda upp en Windows Server VHD till Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa och ladda upp en Linux-VHD till Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrera virtuella datorer från Amazon AWS till Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Lagring med höga prestanda för arbetsbelastningar för virtuella Azure-datorer](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
