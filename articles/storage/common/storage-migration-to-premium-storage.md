---
title: Migrera virtuella datorer till Azure Premium Storage | Microsoft-dokument
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage. Premium Storage erbjuder diskstöd med låg latens med hög prestanda för I/O-intensiva arbetsbelastningar som körs på virtuella Azure-datorer.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748343"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrera till Azure Premium Storage (Ohantaged diskar)

> [!NOTE]
> I den här artikeln beskrivs hur du migrerar en virtuell dator som använder ohanterat standarddiskar till en virtuell dator som använder ohanterat premiumdiskar. Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar dina tidigare ohanterade diskar till hanterade diskar. Hanterade diskar hanterar underliggande lagringskonton så att du inte behöver. Mer information finns i [översikten över hanterade diskar.](../../virtual-machines/windows/managed-disks-overview.md)
>

Azure Premium Storage ger diskstöd med hög prestanda med låg latens för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Du kan dra nytta av hastigheten och prestandan hos dessa diskar genom att migrera programmets VM-diskar till Azure Premium Storage.

Syftet med den här guiden är att hjälpa nya användare av Azure Premium Storage att bättre förbereda sig för att göra en smidig övergång från deras nuvarande system till Premium Storage. Guiden behandlar tre av de viktigaste komponenterna i den här processen:

* [Planera för migrering till Premium Storage](#plan-the-migration-to-premium-storage)
* [Förbereda och kopiera virtuella hårddiskar (VHD) till Premium-lagring](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Skapa virtuell Azure-dator med Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Du kan antingen migrera virtuella datorer från andra plattformar till Azure Premium Storage eller migrera befintliga Virtuella Azure-datorer från standardlagring till Premium-lagring. Den här guiden beskriver steg för båda två scenarierna. Följ stegen som anges i det relevanta avsnittet beroende på ditt scenario.

> [!NOTE]
> Du hittar en funktionsöversikt och prissättning av premium-SSD:er i: [Välj en disktyp för virtuella IaaS-datorer](../../virtual-machines/windows/disks-types.md#premium-ssd). Vi rekommenderar att du migrerar alla virtuella datordiskar som kräver hög IOPS till Azure Premium Storage för bästa prestanda för ditt program. Om disken inte kräver hög IOPS kan du begränsa kostnaderna genom att underhålla den i standardlagring, som lagrar diskdata för virtuella datorer på hårddiskar i stället för SSD-enheter.
>

Slutföra migreringsprocessen i sin helhet kan kräva ytterligare åtgärder både före och efter stegen i den här guiden. Exempel på detta är att konfigurera virtuella nätverk eller slutpunkter eller göra kodändringar i själva programmet, vilket kan kräva vissa driftstopp i ditt program. Dessa åtgärder är unika för varje program, och du bör slutföra dem tillsammans med stegen i den här guiden för att göra hela övergången till Premium Storage så smidig som möjligt.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Planera för migreringen till Premium Storage
Det här avsnittet säkerställer att du är redo att följa migreringsstegen i den här artikeln och hjälper dig att fatta det bästa beslutet om vm- och disktyper.

### <a name="prerequisites"></a>Krav
* Du behöver en Azure-prenumeration. Om du inte har en kan du skapa en [månads kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/pricing/free-trial/) eller besöka [Azure Pricing](https://azure.microsoft.com/pricing/) för fler alternativ.
* Om du vill köra PowerShell-cmdletar behöver du Microsoft Azure PowerShell-modulen. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* När du planerar att använda virtuella Azure-datorer som körs på Premium Storage måste du använda de virtuella datorerna för Premium-lagring. Du kan använda både standard- och premiumlagringsdiskar med virtuella premiumlagringskompatibla virtuella datorer. Premium-lagringsdiskar kommer att vara tillgängliga med fler VM-typer i framtiden. Mer information om alla tillgängliga disktyper och storlekar på Azure VM finns i [Storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och storlekar för [molntjänster](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Överväganden
En Virtuell Azure-dator stöder att flera Premium Storage-diskar kan anslutas så att dina program kan ha upp till 256 TB lagringsutrymme per virtuell dator. Med Premium Storage kan dina program uppnå 80 000 IOPS (in-/utdataåtgärder per sekund) per virtuell dator och 2 000 MB per sekund diskgenomströmning per virtuell dator med extremt låga svarstider för läsåtgärder. Du har alternativ i en mängd olika virtuella datorer och diskar. Det här avsnittet är för att hjälpa dig att hitta ett alternativ som bäst passar din arbetsbelastning.

#### <a name="vm-sizes"></a>VM-storlekar
Storleksspecifikationerna för Azure VM visas i [Storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Granska prestandaegenskaperna för virtuella datorer som fungerar med Premium Storage och välj den lämpligaste vm-storleken som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att driva disktrafiken.

#### <a name="disk-sizes"></a>Diskstorlekar
Det finns fem typer av diskar som kan användas med din virtuella dator och var och en har specifika IOPs och dataflödesgränser. Ta hänsyn till dessa gränser när du väljer disktyp för den virtuella datorn baserat på behoven i ditt program när det gäller kapacitet, prestanda, skalbarhet och toppbelastningar.

| Typ av premiumdiskar  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Diskstorlek           | 128 GB| 512 GB| 1 024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS per disk       | 500   | 2 300  | 5000           | 7 500           | 7 500           | 
| Dataflöde per disk | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

Beroende på din arbetsbelastning, avgöra om ytterligare datadiskar är nödvändiga för din virtuella dator. Du kan koppla flera beständiga datadiskar till den virtuella datorn. Om det behövs kan du randa över diskarna för att öka volymens kapacitet och prestanda. (Se vad som är Disk Striping [här](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Om du stripe Premium Storage-datadiskar med [lagringsutrymmen][4]bör du konfigurera dem med en kolumn för varje disk som används. Annars kan den totala prestandan för den stripe-volymen vara lägre än förväntat på grund av ojämn trafikfördelning över diskarna. För Virtuella Linux-datorer kan du använda *mdadm-verktyget* för att uppnå samma. Se artikel [Konfigurera Software RAID på Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för mer information.

#### <a name="storage-account-scalability-targets"></a>Hållbarhetsmål för lagringsansvar

Premium Storage-konton har följande skalbarhetsmål. Om dina programkrav överskrider skalbarhetsmålen för ett enskilt lagringskonto skapar du programmet så att det använder flera lagringskonton och partitionerar dina data över dessa lagringskonton.

| Total kontokapacitet | Total bandbredd för ett lokalt redundant lagringskonto |
|:--- |:--- |
| Diskkapacitet: 35 TB<br />Snapshot kapacitet: 10 TB |Upp till 50 gigabit per sekund för Inkommande + Utgående |

Mer information om Specifikationer för Premium-lagring finns i [Skalbarhetsmål för premiumsida blob storage-konton](../blobs/scalability-targets-premium-page-blobs.md).

#### <a name="disk-caching-policy"></a>Princip för cachelagring av disk
Som standard är diskcachelagringsprincipen *skrivskyddad* för alla Premium-datadiskar och *Läs-skriv* för premium-operativsystemdisken som är ansluten till den virtuella datorn. Den här konfigurationsinställningen rekommenderas för att uppnå optimal prestanda för programmets IOs. För skrivtunna eller skriv-bara datadiskar (till exempel SQL Server-loggfiler) inaktiverar du diskcachelagring så att du kan uppnå bättre programprestanda. Cacheinställningarna för befintliga datadiskar kan uppdateras med hjälp av [Azure-portalen](https://portal.azure.com) eller *parametern -HostCaching* i cmdleten *Set-AzureDataDisk.*

#### <a name="location"></a>Location
Välj en plats där Azure Premium Storage är tillgängligt. Se [Azure Services efter region](https://azure.microsoft.com/regions/#services) för uppdaterad information om tillgängliga platser. Virtuella datorer som finns i samma region som lagringskontot som lagrar diskarna för den virtuella datorn ger mycket bättre prestanda än om de är i separata regioner.

#### <a name="other-azure-vm-configuration-settings"></a>Andra konfigurationsinställningar för Azure VM
När du skapar en virtuell Azure-dator blir du ombedd att konfigurera vissa vm-inställningar. Kom ihåg att få inställningar är fasta under den virtuella datorns livstid, medan du kan ändra eller lägga till andra senare. Granska dessa konfigurationsinställningar för Azure VM och se till att dessa är konfigurerade på rätt sätt för att matcha dina arbetsbelastningskrav.

### <a name="optimization"></a>Optimering
[Azure Premium Storage: Design for High Performance](../../virtual-machines/windows/premium-storage-performance.md) innehåller riktlinjer för att skapa högpresterande program med Azure Premium Storage. Du kan följa riktlinjerna i kombination med metodtips för prestanda som gäller för tekniker som används av ditt program.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Förbereda och kopiera virtuella hårddiskar (VHD) till Premium Storage
I följande avsnitt finns riktlinjer för hur du förbereder virtuella hårddiskar från den virtuella datorn och kopierar virtuella hårddiskar till Azure Storage.

* [Scenario 1: "Jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage."](#scenario1)
* [Scenario 2: "Jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Krav
Om du vill förbereda virtuella hårddiskar för migrering måste du:

* En Azure-prenumeration, ett lagringskonto och en behållare i det lagringskonto som du kan kopiera din virtuella hårddisk till. Observera att mållagringskontot kan vara ett standard- eller Premium Storage-konto beroende på dina behov.
* Ett verktyg för att generalisera den virtuella hårddisken om du planerar att skapa flera VM-instanser från den. Till exempel sysprep för Windows eller virt-sysprep för Ubuntu.
* Ett verktyg för att överföra VHD-filen till lagringskontot. Se [Överföra data med AzCopy Command-Line Utility](storage-use-azcopy.md) eller använd en Azure storage [explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). I den här guiden beskrivs kopiering av din virtuella hårddisk med hjälp av azkopiverktyget.

> [!NOTE]
> Om du väljer synkron kopieringsalternativ med AzCopy, för optimal prestanda, kopierar du din virtuella hårddisk genom att köra ett av dessa verktyg från en Azure VM som finns i samma region som mållagringskontot. Om du kopierar en virtuell hårddisk från en Virtuell Azure-dator i en annan region kan prestanda vara långsammare.
>
> När du kopierar en stor mängd data över begränsad bandbredd bör du [överväga att använda Azure Import/Export-tjänsten för att överföra data till Blob Storage](../storage-import-export-service.md). På så sätt kan du överföra dina data genom att skicka hårddiskar till ett Azure-datacenter. Du kan bara använda Azure Import/Export-tjänsten för att kopiera data till ett standardlagringskonto. När data finns i ditt standardlagringskonto kan du använda antingen [Kopiera Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) eller AzCopy för att överföra data till ditt premiumlagringskonto.
>
> Observera att Microsoft Azure endast stöder VHD-filer i fast storlek. VHDX-filer eller dynamiska virtuella hårddiskar stöds inte. Om du har en dynamisk virtuell hårddisk kan du konvertera den till fast storlek med cmdleten [Konvertera-VHD.](https://technet.microsoft.com/library/hh848454.aspx)
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Scenario 1: "Jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage."
Om du migrerar befintliga virtuella Azure-datorer stoppar du den virtuella datorn, förbereder virtuella hårddiskar per den typ av virtuell hårddisk du vill ha och kopierar sedan den virtuella hårddisken med AzCopy eller PowerShell.

Den virtuella datorn måste vara helt nere för att migrera ett rent tillstånd. Det kommer att finnas ett driftstopp tills migreringen är klar.

#### <a name="step-1-prepare-vhds-for-migration"></a>Steg 1. Förbereda virtuella hårddiskar för migrering
Om du migrerar befintliga virtuella Azure-datorer till Premium Storage kan din virtuella hårddisk vara:

* En allmän avbildning av operativsystemet
* En unik operativsystemdisk
* En datadisk

Nedan går vi igenom dessa 3 scenarier för att förbereda din virtuella hårddisk.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Använda en generaliserad virtuell hårddisk i operativsystemet för att skapa flera VM-instanser
Om du överför en virtuell hårddisk som ska användas för att skapa flera generiska Azure VM-instanser måste du först generalisera VHD med hjälp av ett sysprep-verktyg. Detta gäller för en virtuell hårddisk som är lokalt eller i molnet. Sysprep tar bort all maskinspecifik information från den virtuella hårddisken.

> [!IMPORTANT]
> Ta en ögonblicksbild eller säkerhetskopiera den virtuella datorn innan du generaliserar den. Om du kör sysprep stoppas och frigörs den virtuella datorn-instansen. Följ stegen nedan för att sysprep en Windows OS VHD. Observera att om du kör kommandot Sysprep måste du stänga av den virtuella datorn. Mer information om Sysprep finns i [Sysprep Översikt](https://technet.microsoft.com/library/hh825209.aspx) eller [Sysprep Teknisk referens](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Öppna ett kommandotolksfönster som administratör.
2. Ange följande kommando för att öppna Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. I systemförberedelseverktyget väljer du Ange oobe (System out-of-Box Experience), markerar kryssrutan Generalize, väljer **Avstängning**och klickar sedan på **OK**, som visas i bilden nedan. Sysprep generaliserar operativsystemet och stänger av systemet.

    ![][1]

För en Ubuntu-virtuell dator använder du virt-sysprep för att uppnå samma. Se [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) för mer information. Se även några av de open source [Linux Server Etablering programvara](https://www.cyberciti.biz/tips/server-provisioning-software.html) för andra Linux-operativsystem.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Använda en unik virtuell hårddisk i operativsystemet för att skapa en enda VM-instans
Om du har ett program som körs på den virtuella datorn som kräver datorspecifika data ska du inte generalisera den virtuella hårddisken. En icke-generaliserad virtuell hårddisk kan användas för att skapa en unik Azure VM-instans. Om du till exempel har Domänkontrollant på din virtuella hårddisk blir den ineffektiv som domänkontrollant genom att köra sysprep. Granska programmen som körs på den virtuella datorn och effekten av att köra sysprep på dem innan du generaliserar den virtuella hårddisken.

##### <a name="register-data-disk-vhd"></a>Registrera virtuell datadisk
Om du har datadiskar i Azure som ska migreras måste du se till att de virtuella datorerna som använder dessa datadiskar stängs av.

Följ stegen nedan för att kopiera VIRTUELLD till Azure Premium Storage och registrera den som en etablerad datadisk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa målet för din virtuella hårddisk
Skapa ett lagringskonto för att underhålla dina virtuella hårddiskar. Tänk på följande punkter när du planerar var du ska lagra dina virtuella hårddiskar:

* Målpremielagringskontot.
* Lagringskontoplatsen måste vara samma som Azure-virtuella Azure-virtuella azure-datorer som premiumlagringskompatibla som du skapar i det sista steget. Du kan kopiera till ett nytt lagringskonto eller planera att använda samma lagringskonto baserat på dina behov.
* Kopiera och spara lagringskontonyckeln för mållagringskontot för nästa steg.

För datadiskar kan du välja att behålla vissa datadiskar i ett standardlagringskonto (till exempel diskar som har kylare lagring), men vi rekommenderar starkt att du flyttar alla data för produktionsarbetsbelastning för att använda premiumlagring.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Steg 3. Kopiera VIRTUELLD med AzCopy eller PowerShell
Du måste hitta din behållarsökväg och lagringskontonyckel för att bearbeta något av dessa två alternativ. Behållarsökväg och lagringskontonyckel finns i **Azure Portal** > **Storage**. Behållar-URL:en blir\/som "https: /myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Alternativ 1: Kopiera en virtuell hårddisk med AzCopy (asynkron kopia)

Med AzCopy kan du enkelt ladda upp den virtuella hårddisken via Internet. Beroende på storleken på de virtuella hårddiskarna kan det ta tid. Kom ihåg att kontrollera begränsningarna för lagringskontot ingång/utgående när du använder det här alternativet. Mer information finns i [Skalbarhets- och prestandamål för standardlagringskonton.](scalability-targets-standard-account.md)

1. Ladda ner och installera AzCopy härifrån: [Senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från "Källa" till "Destination".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Här är beskrivningar av de parametrar som används i kommandot AzCopy:

   * **/Källa:** _ &lt;&gt;källa :_ Plats för mapp- eller lagringsbehållarens URL som innehåller den virtuella hårddisken.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ Storage account key of the source storage account.
   * **/Dest:** _ &lt;&gt;destination:_ URL för lagringsbehållare för att kopiera den virtuella hårddisken till.
   * **/DestKey:** _ &lt;dest-account-key&gt;: Lagringskontonyckel_ för mållagringskontot.
   * **/Pattern:** _ &lt;filnamn&gt;:_ Ange filnamnet på den virtuella hårddisk som ska kopieras.

Mer information om hur du använder verktyget AzCopy finns i [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Alternativ 2: Kopiera en virtuell hårddisk med PowerShell (synkroniserad kopia)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också kopiera VHD-filen med PowerShell cmdlet Start-AzStorageBlobCopy. Använd följande kommando på Azure PowerShell för att kopiera VIRTUELLD. Ersätt värdena i <> med motsvarande värden från ditt käll- och mållagringskonto. Om du vill använda det här kommandot måste du ha en behållare som kallas virtuella hårddiskar i ditt mållagringskonto. Om behållaren inte finns skapar du en innan du kör kommandot.

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

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Scenario 2: "Jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage."
Om du migrerar virtuell hårddisk från icke-Azure Cloud Storage till Azure måste du först exportera den virtuella hårddisken till en lokal katalog. Har den fullständiga källsökvägen för den lokala katalogen där VHD lagras praktiskt och sedan använda AzCopy för att ladda upp den till Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Steg 1. Exportera virtuell hårddisk till en lokal katalog
##### <a name="copy-a-vhd-from-aws"></a>Kopiera en virtuell hårddisk från AWS
1. Om du använder AWS exporterar du EC2-instansen till en virtuell hårddisk i en Amazon S3-bucket. Följ stegen som beskrivs i Amazon-dokumentationen för export av Amazon EC2-instanser för att installera verktyget Amazon EC2-kommandoradsgränssnitt (CLI) och köra kommandot create-instance-export-task för att exportera EC2-instansen till en VHD-fil. Var noga med att använda **VHD** för disk-&#95;IMAGE&#95;FORMAT-variabeln när kommandot **create-instance-export-task** körs. Den exporterade VHD-filen sparas i Amazon S3-bucketen som du anger under den processen.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Hämta VHD-filen från S3-bucketen. Markera VHD-filen och hämta **åtgärder** > **.**

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiera en virtuell hårddisk från andra moln som inte är Azure
Om du migrerar virtuell hårddisk från icke-Azure Cloud Storage till Azure måste du först exportera den virtuella hårddisken till en lokal katalog. Kopiera den fullständiga källsökvägen för den lokala katalogen där virtuell hårddisk lagras.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiera en virtuell hårddisk från lokala
Om du migrerar virtuell hårddisk från en lokal miljö behöver du den fullständiga källsökvägen där virtuell hårddisk lagras. Källsökvägen kan vara en serverplats eller filresurs.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa målet för din virtuella hårddisk
Skapa ett lagringskonto för att underhålla dina virtuella hårddiskar. Tänk på följande punkter när du planerar var du ska lagra dina virtuella hårddiskar:

* Mållagringskontot kan vara standard- eller premiumlagring beroende på ditt programkrav.
* Lagringskontoregionen måste vara samma som Azure-virtuella Azure-virtuella azure-datorer som premiumlagringskompatibla som du skapar i det sista steget. Du kan kopiera till ett nytt lagringskonto eller planera att använda samma lagringskonto baserat på dina behov.
* Kopiera och spara lagringskontonyckeln för mållagringskontot för nästa steg.

Vi rekommenderar starkt att du flyttar alla data för produktionsarbetsbelastning för att använda premiumlagring.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Steg 3. Ladda upp den virtuella hårddisken till Azure Storage
Nu när du har din virtuella hårddisk i den lokala katalogen kan du använda AzCopy eller AzurePowerShell för att överföra VHD-filen till Azure Storage. Båda alternativen finns här:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Alternativ 1: Använda Azure PowerShell Add-AzureVhd för att ladda upp VHD-filen

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Ett \<exempel Uri> kan vara **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Ett \<exempel på FileInfo> kan vara **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Alternativ 2: Använda AzCopy för att ladda upp VHD-filen

Med AzCopy kan du enkelt ladda upp den virtuella hårddisken via Internet. Beroende på storleken på de virtuella hårddiskarna kan det ta tid. Kom ihåg att kontrollera begränsningarna för lagringskontot ingång/utgående när du använder det här alternativet. Mer information finns i [Skalbarhets- och prestandamål för standardlagringskonton.](scalability-targets-standard-account.md)

1. Ladda ner och installera AzCopy härifrån: [Senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från "Källa" till "Destination".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Exempel:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Här är beskrivningar av de parametrar som används i kommandot AzCopy:

   * **/Källa:** _ &lt;&gt;källa :_ Plats för mapp- eller lagringsbehållarens URL som innehåller den virtuella hårddisken.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ Storage account key of the source storage account.
   * **/Dest:** _ &lt;&gt;destination:_ URL för lagringsbehållare för att kopiera den virtuella hårddisken till.
   * **/DestKey:** _ &lt;dest-account-key&gt;: Lagringskontonyckel_ för mållagringskontot.
   * **/BlobType: sida:** Anger att målet är en sidblob.
   * **/Pattern:** _ &lt;filnamn&gt;:_ Ange filnamnet på den virtuella hårddisk som ska kopieras.

Mer information om hur du använder verktyget AzCopy finns i [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att ladda upp en virtuell hårddisk
Du kan också ladda upp en virtuell hårddisk till ditt lagringskonto på något av följande sätt:

* [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Ladda upp Blobbar för Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [REST-API-referens för lagringsimport/exporttjänst](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Vi rekommenderar att du använder import-/exporttjänsten om den uppskattade uppladdningstiden är längre än 7 dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) för att uppskatta tiden från datastorlek och överföringsenhet.
>
> Import/export kan användas för att kopiera till ett standardlagringskonto. Du måste kopiera från standardlagring till premiumlagringskonto med ett verktyg som AzCopy.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Skapa virtuella Azure-datorer med Premium Storage
När den virtuella hårddisken har överförts eller kopierats till önskat lagringskonto följer du instruktionerna i det här avsnittet för att registrera den virtuella hårddisken som en OS-avbildning eller OS-disk beroende på ditt scenario och sedan skapa en VM-instans från den. Datadisken VHD kan kopplas till den virtuella datorn när den har skapats.
Ett exempelmigreringsskript finns i slutet av det här avsnittet. Det här enkla skriptet matchar inte alla scenarier. Du kan behöva uppdatera skriptet så att det stämmer överens med ditt specifika scenario. Mer om du vill se om det här skriptet gäller för ditt scenario finns nedan [ett exempelmigreringsskript](#a-sample-migration-script).

### <a name="checklist"></a>Checklista
1. Vänta tills alla VHD-diskar kopiering är klar.
2. Kontrollera att Premium Storage är tillgängligt i den region du migrerar till.
3. Bestäm den nya VM-serien som du ska använda. Det bör vara en Premium Storage-kapacitet, och storleken ska vara beroende på tillgängligheten i regionen och baserat på dina behov.
4. Bestäm den exakta vm-storleken du ska använda. VM-storleken måste vara tillräckligt stor för att stödja antalet datadiskar som du har. T.ex. Om du har 4 datadiskar måste den virtuella datorn ha två eller fler kärnor. Överväg också att bearbeta energi- och nätverksbandbreddsbehov.
5. Skapa ett Premium Storage-konto i målregionen. Det här är kontot som du ska använda för den nya virtuella datorn.
6. Ha den aktuella VM-informationen till hands, inklusive listan över diskar och motsvarande VHD-blobbar.

Förbered din ansökan för driftstopp. Om du vill göra en ren migrering måste du stoppa all bearbetning i det aktuella systemet. Först då kan du få det till konsekvent tillstånd som du kan migrera till den nya plattformen. Stilleståndstiden beror på hur mycket data i diskarna som ska migreras.

> [!NOTE]
> Om du skapar en virtuell dator med Azure Resource Manager från en specialiserad VHD-disk läser du [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) för distribution av Resource Manager-vm med befintlig disk.
>
>

### <a name="register-your-vhd"></a>Registrera din virtuella hårddisk
Om du vill skapa en virtuell dator från virtuell dator för os eller koppla en datadisk till en ny virtuell dator måste du först registrera dem. Följ stegen nedan beroende på ditt VHD-scenario.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Allmän virtuell hårddisk för operativsystemet för att skapa flera Azure VM-instanser
När den allmänna virtuella hårddisken för OS-avbildningen har överförts till lagringskontot registrerar du den som en **Azure VM-avbildning** så att du kan skapa en eller flera VM-instanser från det. Använd följande PowerShell-cmdlets för att registrera din virtuella hårddisk som en Azure VM OS-avbildning. Ange hela behållar-URL:en där VHD kopierades till.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopiera och spara namnet på den här nya Azure VM-avbildningen. I exemplet ovan är det *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unik virtuell hårddisk för operativsystem för att skapa en enda Azure VM-instans
När den unika VIRTUELLA OS-hårddisken har överförts till lagringskontot registrerar du den som en **Azure OS-disk** så att du kan skapa en VM-instans från den. Använd dessa PowerShell-cmdlets för att registrera din virtuella hårddisk som en Azure OS-disk. Ange hela behållar-URL:en där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopiera och spara namnet på den här nya Azure OS-disken. I exemplet ovan är det *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Datadiskens virtuella hårddisk som ska kopplas till nya Azure VM-instanser
När datadiskens VIRTUELLA HÅRDDISK har överförts till lagringskontot registrerar du den som en Azure Data Disk så att den kan kopplas till din nya DS-serie, DSv2-serie eller GS Series Azure VM-instans.

Använd dessa PowerShell-cmdlets för att registrera din virtuella hårddisk som en Azure Data Disk. Ange hela behållar-URL:en där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopiera och spara namnet på den nya Azure Data Disk. I exemplet ovan är det *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Skapa en virtuell dator med Premium-lagringskompatibel
När OS-avbildningen eller OS-disken har registrerats skapar du en ny virtuell dator i DSv2-serien eller en virtuell dator i GS-serien. Du kommer att använda operativsystemets avbildning eller operativsystemsdisknamn som du registrerade. Välj den virtuella datorn-typen på premiumlagringsnivån. I exemplet nedan använder vi *den Standard_DS2* VM-storleken.

> [!NOTE]
> Uppdatera diskstorleken för att se till att den matchar dina kapacitets- och prestandakrav och tillgängliga Azure-diskstorlekar.
>
>

Följ steg för steg PowerShell-cmdlets nedan för att skapa den nya virtuella datorn. Ställ först in de gemensamma parametrarna:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Skapa först en molntjänst där du kommer att vara värd för dina nya virtuella datorer.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Därefter, beroende på ditt scenario, skapa Azure VM-instansen från antingen OS-avbildningen eller OS-disken som du registrerade.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Allmän virtuell hårddisk för operativsystemet för att skapa flera Azure VM-instanser
Skapa en eller flera nya Azure VM-instanser i DS-serien med azure **os-avbildningen** som du registrerade. Ange det här OS-avbildningsnamnet i vm-konfigurationen när du skapar ny virtuell dator enligt nedan.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unik virtuell hårddisk för operativsystem för att skapa en enda Azure VM-instans
Skapa en ny Azure VM-instans i DS-serien med azure **os-disken** som du registrerade. Ange det här OS-disknamnet i vm-konfigurationen när du skapar den nya virtuella datorn enligt nedan.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Ange annan Azure VM-information, till exempel en molntjänst, region, lagringskonto, tillgänglighetsuppsättning och cachelagringsprincip. Observera att VM-instansen måste vara samlokalisering med associerade operativsystem eller datadiskar, så det valda molntjänst-, region- och lagringskontot måste alla finnas på samma plats som de underliggande virtuella hårddiskarna för dessa diskar.

### <a name="attach-data-disk"></a>Anslut en datadisk
Slutligen, om du har registrerade datadisk-virtuella hårddiskar, bifoga dem till den nya Azure Storage-kompatibla Azure-datorn för Premium Storage.

Använd följande PowerShell-cmdlet för att koppla datadisk till den nya virtuella datorn och ange cachelagringsprincipen. I exempel nedan anges cachelagringsprincipen till *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Det kan finnas ytterligare åtgärder som krävs för att stödja ditt program som inte omfattas av den här guiden.
>
>

### <a name="checking-and-plan-backup"></a>Kontrollera och planera säkerhetskopiering
När den nya virtuella datorn är igång, komma åt den med samma inloggnings-ID och lösenord är som den ursprungliga virtuella datorn, och kontrollera att allt fungerar som förväntat. Alla inställningar, inklusive stripe-volymer, finns i den nya virtuella datorn.

Det sista steget är att planera säkerhetskopierings- och underhållsschema för den nya virtuella datorn baserat på programmets behov.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Ett exempelmigreringsskript
Om du har flera virtuella datorer att migrera kommer automatisering via PowerShell-skript att vara till hjälp. Följande är ett exempel skript som automatiserar migreringen av en virtuell dator. Observera att nedanstående skript är bara ett exempel, och det finns få antaganden om de aktuella VM-diskarna. Du kan behöva uppdatera skriptet så att det stämmer överens med ditt specifika scenario.

Antagandena är:

* Du skapar klassiska virtuella Azure-datorer.
* Käll-OS-diskar och källdatadiskar finns i samma lagringskonto och samma behållare. Om dina OS-diskar och datadiskar inte är på samma plats kan du använda AzCopy eller Azure PowerShell för att kopiera virtuella hårddiskar via lagringskonton och behållare. Se föregående steg: [Kopiera VIRTUELLD med AzCopy eller PowerShell](#copy-vhd-with-azcopy-or-powershell). Att redigera det här skriptet för att uppfylla ditt scenario är ett annat val, men vi rekommenderar att du använder AzCopy eller PowerShell eftersom det är enklare och snabbare.

Automatiseringsskriptet finns nedan. Ersätt text med din information och uppdatera skriptet så att det stämmer överens med ditt specifika scenario.

> [!NOTE]
> Om du använder det befintliga skriptet bevaras inte nätverkskonfigurationen för källdatorn. Du måste konfigurera om nätverksinställningarna på migrerade virtuella datorer.
>
>

```powershell
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
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

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

    # how frequently to report the copy status in seconds
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
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
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
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
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

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
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

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
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

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a><a name="optimization"></a>Optimering
Den aktuella VM-konfigurationen kan anpassas specifikt för att fungera bra med standarddiskar. Till exempel för att öka prestanda genom att använda många diskar i en randig volym. I stället för att till exempel använda 4 diskar separat på Premium Storage kan du kanske optimera kostnaden genom att ha en enda disk. Optimeringar som detta måste hanteras från fall till fall och kräver anpassade steg efter migreringen. Observera också att den här processen kanske inte fungerar väl för databaser och program som är beroende av disklayouten som definierats i installationen.

##### <a name="preparation"></a>Förberedelse
1. Slutför den enkla migreringen enligt beskrivningen i det tidigare avsnittet. Optimeringar utförs på den nya virtuella datorn efter migreringen.
2. Definiera de nya diskstorlekar som behövs för den optimerade konfigurationen.
3. Bestäm mappning av aktuella diskar/volymer till de nya diskspecifikationerna.

##### <a name="execution-steps"></a>Körningssteg
1. Skapa de nya diskarna med rätt storlekar på den virtuella datorn för Premium-lagring.
2. Logga in på den virtuella datorn och kopiera data från den aktuella volymen till den nya disken som mappar till den volymen. Gör detta för alla aktuella volymer som behöver mappas till en ny disk.
3. Ändra sedan programinställningarna för att växla till de nya diskarna och koppla bort de gamla volymerna.

För att justera programmet för bättre diskprestanda, se avsnittet optimera programprestanda i vår [design för högpresterande](../../virtual-machines/windows/premium-storage-performance.md) artikel.

### <a name="application-migrations"></a>Migreringar av program
Databaser och andra komplexa program kan kräva särskilda steg enligt programleverantörens definition för migreringen. Se respektive ansökningsdokumentation. T.ex. Vanligtvis kan databaser migreras via säkerhetskopiering och återställning.

## <a name="next-steps"></a>Nästa steg
Se följande resurser för specifika scenarier för migrering av virtuella datorer:

* [Migrera virtuella Azure-datorer mellan lagringskonton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Skapa och ladda upp en virtuell windows server-hårddisk till Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa och ladda upp en Virtuell Linux-hårddisk till Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrera virtuella datorer från Amazon AWS till Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Virtuella Azure-datorer:

* [Azure-lagring](https://azure.microsoft.com/documentation/services/storage/)
* [Virtuella Azure-datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Välja en disktyp för virtuella IaaS-datorer](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
