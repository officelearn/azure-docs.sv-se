---
title: Migrera virtuella datorer till Azure Premium-lagring | Microsoft Docs
description: "Migrera dina befintliga virtuella datorer till Azure Premium-lagring. Premium-lagring ger stöd för I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines diskar med hög prestanda, låg latens."
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: ca893f87b155a92c457e3bf6d9d39aaf86bf5fb3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrera till Azure Premium-lagring (ohanterade diskar)

> [!NOTE]
> Den här artikeln beskriver hur du migrerar en virtuell dator som använder ohanterade standarddiskar till en virtuell dator som använder ohanterade premiumdiskar. Vi rekommenderar att du använder Azure hanterade diskar för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar. Hanterade diskar referensen underliggande storage-konton, så du behöver. Mer information, se vår [översikt för hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Du kan dra nytta av hastighet och prestanda för dessa diskar genom att migrera programmets Virtuella diskar till Azure Premium-lagring.

Syftet med den här guiden är att nya användare på Azure Premium Storage bättre förbereda för att få en smidig övergång från systemet till Premium-lagring. Guiden löser tre huvudkomponenter i den här processen:

* [Planera för migrering till Premium-lagring](#plan-the-migration-to-premium-storage)
* [Förbereda och kopiera virtuella hårddiskar (VHD) till Premium-lagring](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Skapa Azure virtuell dator med Premium-lagring](#create-azure-virtual-machine-using-premium-storage)

Du kan migrera virtuella datorer från andra plattformar till Azure Premium-lagring, eller så kan du migrera befintliga virtuella Azure-datorer från standardlagring till Premium-lagring. Den här guiden beskriver steg för båda två scenarier. Följ stegen som anges i avsnittet relevanta beroende på ditt scenario.

> [!NOTE]
> Du hittar en översikt över funktioner och priser för Premium-lagring i Premium Storage: [högpresterande lagring för arbetsbelastningar på virtuella Azure](storage-premium-storage.md). Vi rekommenderar att du migrerar alla virtuella diskar som kräver hög IOPS till Azure Premium-lagring för bästa prestanda för ditt program. Om disken inte kräver hög IOPS, kan du begränsa kostnader genom att upprätthålla i standardlagring som lagrar data för virtuell disk på hårddiskar (HDD) i stället för SSD-enheter.
>

Slutföra migreringsprocessen i sin helhet kan kräva ytterligare åtgärder både före och efter stegen i den här guiden. Exempel inkluderar konfigurering av virtuella nätverk eller slutpunkter eller ändrar koden själva programmet som kan kräva vissa avbrott i ditt program. Dessa åtgärder är unika för varje program och du bör utföra dem tillsammans med stegen i den här guiden för att göra fullständiga övergången till Premium-lagring som sömlös som möjligt.

## <a name="plan-the-migration-to-premium-storage"></a>Planera för migrering till Premium-lagring
Det här avsnittet säkerställer att du är redo att följa stegen i migreringen i den här artikeln och hjälper dig att göra det bästa på VM- och diskresurser typer.

### <a name="prerequisites"></a>Krav
* Du behöver en Azure-prenumeration. Om du inte har någon, kan du skapa en månad [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) prenumeration eller besök [priser för Azure](https://azure.microsoft.com/pricing/) fler alternativ.
* Om du vill köra PowerShell-cmdlets, måste Microsoft Azure PowerShell-modulen. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* När du planerar att använda Azure virtuella datorer som körs på Premium-lagring måste du använda Premium-lagring kan virtuella datorer. Du kan använda Standard- och Premium-lagring diskar med Premium-lagring kan virtuella datorer. Premium-lagringsdiskar kommer att vara tillgänglig med flera VM-typer i framtiden. Läs mer på alla tillgängliga Virtuella Azure-disktyper och storlekar, [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [storlekar för molntjänster](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Överväganden
En Azure VM stöder bifoga flera Premium-lagring diskar så att dina program kan ha upp till 256 TB lagringsutrymme per virtuell dator. Med Premium-lagring kan dina program uppnå (i/o-åtgärder per sekund) för 80 000 IOPS per virtuell dator och 2000 MB per andra diskgenomflödet per virtuell dator med mycket låg latens för läsåtgärder. Har du alternativ på en mängd olika virtuella datorer och diskar. Det här avsnittet är att hjälpa dig att hitta ett alternativ som bäst passar din arbetsbelastning.

#### <a name="vm-sizes"></a>VM-storlekar
Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Granska prestandaegenskaper för virtuella datorer som fungerar med Premium-lagring och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att ge disk-trafik.

#### <a name="disk-sizes"></a>Diskstorlekar
Det finns fem typer av diskar som kan användas med den virtuella datorn och var och en har särskilda IOPs och genomströmning gränser. Ta hänsyn till dessa begränsningar när du väljer typ av disk för den virtuella datorn baserat på dina behov av ditt program vad gäller kapacitet, prestanda, skalbarhet och belastning läser in.

| Premium diskar typ  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Diskstorlek           | 128 GB| 512 GB| 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS per disk       | 500   | 2 300  | 5000           | 7500           | 7500           | 
| Dataflöde per disk | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

Beroende på din arbetsbelastning avgör du om ytterligare hårddiskar krävs för den virtuella datorn. Du kan bifoga flera beständiga datadiskar till den virtuella datorn. Om det behövs kan stripe du över diskarna att öka kapaciteten och prestandan för volymen. (Se vad som är Disk Striping [här](storage-premium-storage-performance.md#disk-striping).) Om du stripe-Premium-lagring datadiskar med hjälp av [lagringsutrymmen][4], bör du konfigurera den med en kolumn för varje disk som används. Annars vara prestandan stripe-volym lägre än väntat på grund av en ojämn fördelning av trafik över diskar. För virtuella Linux-datorer kan du använda den *mdadm* verktyg för att uppnå samma. Se artikeln [konfigurera programvara RAID på Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information.

#### <a name="storage-account-scalability-targets"></a>Skalbarhetsmål för lagringskontot
Premium-lagringskonton har följande skalbarhetsmål förutom det [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md). Om din programkrav överskrider skalbarhetsmål för ett enda lagringskonto, skapa ditt program att använda flera lagringskonton och partitionera data mellan dessa lagringskonton.

| Total kapacitet | Totala bandbredden för ett lokalt Redundant Lagringskonto |
|:--- |:--- |
| Disken kapacitet: 35TB<br />Ögonblicksbild kapacitet: 10 TB |Upp till 50 Gigabit per sekund för inkommande och utgående |

Mer information om specifikationer för Premium-lagring, kolla [skalbarhets- och prestandamål när du använder Premiumlagring](storage-premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Princip för cachelagring av disk
Princip för cachelagring av disk är som standard *skrivskyddad* för alla Premium datadiskar, och *Read-Write* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimal prestanda för ditt program IOs. Inaktivera cachelagring på disk så att du kan få bättre prestanda för programmet för skrivintensiv eller lässkyddad datadiskar (till exempel loggfiler för SQL Server). Inställningar för cachelagring för befintliga datadiskar kan uppdateras med hjälp av [Azure Portal](https://portal.azure.com) eller *- HostCaching* parameter för den *Set AzureDataDisk* cmdlet.

#### <a name="location"></a>Plats
Välj en plats där Azure Premium-lagring är tillgängliga. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser. Virtuella datorer finns i samma region som det lagringskonto som lagrar diskarna för den virtuella datorn får mycket bättre prestanda än om de finns i olika områden.

#### <a name="other-azure-vm-configuration-settings"></a>Andra Virtuella Azure-konfigurationsinställningar
När du skapar en Azure VM, uppmanas du att konfigurera vissa inställningar för virtuell dator. Kom ihåg att några inställningar korrigeras livslängden för den virtuella datorn medan du kan ändra eller lägga till andra senare. Granska dessa Virtuella Azure-konfigurationsinställningar och se till att dessa är korrekt konfigurerade som motsvarar dina behov av arbetsbelastning.

### <a name="optimization"></a>Optimering
[Azure Premium Storage: Utforma för högprestanda](storage-premium-storage-performance.md) innehåller riktlinjer för att skapa program med höga prestanda med Azure Premium-lagring. Du kan följa riktlinjerna i kombination med prestandarelaterade metodtips gäller för tekniker som används av ditt program.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Förbereda och kopiera virtuella hårddiskar (VHD) till Premium-lagring
Följande avsnitt innehåller riktlinjer för att förbereda virtuella hårddiskar från den virtuella datorn och kopiera virtuella hårddiskar till Azure Storage.

* [Scenario 1: ”jag migrera befintliga virtuella Azure-datorer till Azure Premium-lagring”.](#scenario1)
* [Scenario 2: ”jag migrera virtuella datorer från andra plattformar till Azure Premium-lagring”.](#scenario2)

### <a name="prerequisites"></a>Krav
För att förbereda de virtuella hårddiskarna för migrering, behöver du:

* En Azure-prenumeration, ett lagringskonto och en behållare i det lagringskonto som du kan kopiera den virtuella Hårddisken. Observera att mål-lagringskontot kan vara ett Standard eller Premium-lagring konto utifrån dina behov.
* Ett verktyg för att generalisera den virtuella Hårddisken om du tänker skapa flera VM-instanser. Till exempel sysprep för Windows eller Radnr i virtuell sysprep för Ubuntu.
* Ett verktyg för att överföra VHD-filen till lagringskontot. Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) eller Använd en [Azure Lagringsutforskaren](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Den här guiden beskriver kopiera den virtuella Hårddisken med hjälp av verktyget AzCopy.

> [!NOTE]
> Om du väljer alternativet för synkron kopia med AzCopy, kopierar du den virtuella Hårddisken genom att köra något av dessa verktyg från en virtuell Azure-dator som är i samma region som lagringskontot mål för optimala prestanda. Om du kopierar en VHD från en Azure-dator i en annan region, gå din långsammare.
>
> Överväg att kopiera stora mängder data över begränsad bandbredd, [använder tjänsten Azure Import/Export för att överföra data till Blob Storage](../storage-import-export-service.md); du kan överföra dina data genom att leverera hårddiskar till ett Azure-datacenter. Du kan använda tjänsten Azure Import/Export för att kopiera data till ett standardlagringskonto endast. När data är i ditt lagringskonto som standard, kan du använda antingen den [kopiera Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) eller AzCopy för att överföra data till premium-lagringskontot.
>
> Observera att Microsoft Azure bara stöder fast storlek VHD-filer. VHDX-filer eller dynamiska virtuella hårddiskar stöds inte. Om du har en dynamisk virtuell Hårddisk kan du konvertera den till fast storlek med den [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet.
>
>

### <a name="scenario1"></a>Scenario 1: ”jag migrera befintliga virtuella Azure-datorer till Azure Premium-lagring”.
Om du migrerar befintliga virtuella Azure-datorer, stoppa den virtuella datorn och förbereda virtuella hårddiskar per typ av virtuell Hårddisk som du vill kopiera den virtuella Hårddisken med AzCopy eller PowerShell.

Den virtuella datorn måste vara helt på att migrera ett rent tillstånd. Det blir ett driftstopp tills migreringen är klar.

#### <a name="step-1-prepare-vhds-for-migration"></a>Steg 1. Förbereda virtuella hårddiskar för migrering
Om du migrerar befintliga virtuella Azure-datorer till Premium-lagring kan vara den virtuella Hårddisken:

* En generaliserad operativsystemsavbildning
* En unik operativsystemdisk
* En datadisk

Nedan går vi igenom dessa 3 scenarier för att förbereda den virtuella Hårddisken.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Använd en generaliserad virtuell Hårddisk med operativsystemet för att skapa flera VM-instanser
Om du överför en virtuell Hårddisk som ska användas för att skapa flera generiska Azure VM-instanser måste du först generalisera VHD med sysprep-verktyget. Detta gäller en virtuell Hårddisk som är lokalt eller i molnet. Sysprep tar bort alla datorspecifik information från den virtuella Hårddisken.

> [!IMPORTANT]
> Ta en ögonblicksbild eller säkerhetskopiera den virtuella datorn innan generalisera den. Kör sysprep avbryts och frigöra VM-instans. Följ stegen nedan för att sysprep en Windows OS-VHD. Observera att köra kommandot Sysprep måste du stänga av den virtuella datorn. Mer information om Sysprep finns [Sysprep översikt](http://technet.microsoft.com/library/hh825209.aspx) eller [Teknisk referens för Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Öppna ett kommandotolksfönster som administratör.
2. Ange följande kommando för att öppna Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Välj ange System Out of Box Experience (OOBE) i systemförberedelseverktyget, väljer kryssrutan Generalize, väljer **avstängning**, och klicka sedan på **OK**som visas i bilden nedan. Sysprep att generalisera operativsystemet och stänga av datorn.

    ![][1]

För en Ubuntu VM, använda Radnr i virtuell sysprep för att uppnå samma. Se [Radnr i virtuell sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) för mer information. Se även vissa med öppen källkod [etablering av Linux-servrar programvara](http://www.cyberciti.biz/tips/server-provisioning-software.html) för andra Linux-operativsystem.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Använda en unik virtuell Hårddisk med operativsystemet för att skapa en enda VM-instans
Om du har ett program som körs på den virtuella datorn som kräver att datorn specifika data kan du inte generalisera den virtuella Hårddisken. En ej generaliserad virtuell Hårddisk kan användas för att skapa en unik Virtuella Azure-instans. Till exempel om du har en domänkontrollant på den virtuella Hårddisken kan gör köra sysprep det ineffektiv som en domänkontrollant. Granska de program som körs på den virtuella datorn och effekten av att köra sysprep på dem. innan generalisera den virtuella Hårddisken.

##### <a name="register-data-disk-vhd"></a>Registrera datadisk VHD
Om du har datadiskar i Azure som ska migreras, måste du kontrollera de virtuella datorer som använder diskarna data är avstängd.

Följ stegen nedan för att kopiera VHD till Azure Premium-lagring och registrera den som en etablerade datadisk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa mål för den virtuella Hårddisken
Skapa ett lagringskonto för att underhålla de virtuella hårddiskarna. Tänk på följande när du planerar var du vill lagra de virtuella hårddiskarna:

* Mål Premium storage-konto.
* Lagringsplats för kontot måste vara samma som Premium-lagring kan virtuella Azure-datorer skapas i det sista steget. Du kan kopiera till ett nytt lagringskonto eller planerar att använda samma lagringskonto baserat på dina behov.
* Kopiera och spara lagringskontonyckel på mål-lagringskontot för nästa steg.

Du kan välja att behålla vissa datadiskar i ett standardlagringskonto (till exempel diskar med kyligare lagring) för datadiskar, men rekommenderas du att flytta alla data för produktion arbetsbelastningen att använda premium-lagring.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Steg 3. Kopiera VHD med AzCopy eller PowerShell
Du behöver att hitta din behållaren sökvägen och lagringsutrymmet kontonyckel bearbeta någon av de här två alternativen. Behållaren sökvägen och lagringsutrymmet kontonyckel finns i **Azure Portal** > **lagring**. Behållarens Webbadress kommer att som ”https://myaccount.blob.core.windows.net/mycontainer/”.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Alternativ 1: Kopiera en virtuell Hårddisk med AzCopy (asynkron kopia)
Med AzCopy kan överföra du enkelt den virtuella Hårddisken via Internet. Detta kan ta tid beroende på storleken på de virtuella hårddiskarna. Kom ihåg att kontrollera ingång-/ utgång lagringskontogränser när du använder det här alternativet. Se [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md) mer information.

1. Hämta och installera AzCopy härifrån: [senaste versionen av AzCopy](http://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från ”källa” till ”mål”.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Nedan följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/ Källa:  *&lt;källa&gt;:***  platsen för mappen eller lagring behållarens Webbadress som innehåller den virtuella Hårddisken.
   * **/ SourceKey:  *&lt;källa kontonyckel&gt;:***  lagringskontonyckel på lagringskontot för källa.
   * **/ Dest:  *&lt;mål&gt;:***  lagring behållar-URL för att kopiera den virtuella Hårddisken till.
   * **/ DestKey:  *&lt;dest kontonyckel&gt;:***  lagringskontonyckel på mål-lagringskontot.
   * **/ Mönster:  *&lt;filnamn&gt;:***  ange filnamnet för den virtuella Hårddisken ska kopieras.

Mer information om hur du använder AzCopy verktyget, se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Alternativ 2: Kopiera en VHD med PowerShell (Synchronized kopia)
Du kan också kopiera VHD-filen med hjälp av PowerShell-cmdleten Start-AzureStorageBlobCopy. Använd följande kommando på Azure PowerShell för att kopiera VHD. Ersätt värdena i <> med motsvarande värden från din käll- och storage-konto. Om du vill använda det här kommandot måste du ha en behållare som kallas virtuella hårddiskar på ditt mål-lagringskonto. Om behållaren inte finns skapa en innan kommandot körs.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exempel:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenario 2: ”jag migrera virtuella datorer från andra plattformar till Azure Premium-lagring”.
Om du migrerar VHD från icke - Azure lagringsutrymmet i molnet till Azure, måste du först exportera den virtuella Hårddisken till en lokal katalog. Ha fullständig källsökvägen för den lokala katalogen där VHD lagras praktiska och sedan använda AzCopy för att överföra den till Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Steg 1. Exportera virtuell Hårddisk till en lokal katalog
##### <a name="copy-a-vhd-from-aws"></a>Kopiera en VHD från AWS
1. Om du använder AWS exportera EC2-instansen till en virtuell Hårddisk i ett Amazon S3-bucket. Följ stegen som beskrivs i dokumentationen för Amazon för export av Amazon EC2 instanser som du vill installera verktyget Amazon EC2 kommandoradsgränssnittet (CLI) och kör kommandot create-instans-export-aktivitet för att exportera EC2-instansen till en VHD-fil. Se till att använda **VHD** för DISKEN &#95; BILDEN & #95. FORMATET variabeln när du kör den **skapa-instans-export-aktivitet** kommando. Exporterade VHD-filen sparas i en Amazon S3-bucket som du anger under den här processen.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Hämta VHD-filen från S3-bucket. Välj sedan VHD-filen **åtgärder** > **hämta**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiera en VHD från andra Azure-moln
Om du migrerar VHD från icke - Azure lagringsutrymmet i molnet till Azure, måste du först exportera den virtuella Hårddisken till en lokal katalog. Kopiera fullständiga sökvägen till den lokala katalogen där VHD lagras.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiera en VHD från lokala
Om du migrerar VHD från en lokal miljö, behöver fullständiga källsökvägen där VHD lagras. Sökvägen kan vara en server eller en filresurs.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa mål för den virtuella Hårddisken
Skapa ett lagringskonto för att underhålla de virtuella hårddiskarna. Tänk på följande när du planerar var du vill lagra de virtuella hårddiskarna:

* Mål-lagringskontot kan vara standard eller premium storage beroende på din programkrav.
* Lagringskontots region måste vara samma som Premium-lagring kan virtuella Azure-datorer skapas i det sista steget. Du kan kopiera till ett nytt lagringskonto eller planerar att använda samma lagringskonto baserat på dina behov.
* Kopiera och spara lagringskontonyckel på mål-lagringskontot för nästa steg.

Vi rekommenderar starkt att du flyttar alla data för produktion arbetsbelastningen att använda premium-lagring.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Steg 3. Överför den virtuella Hårddisken till Azure-lagring
Nu när du har den virtuella Hårddisken i den lokala katalogen kan använda du AzCopy eller AzurePowerShell för att överföra VHD-filen till Azure Storage. Båda alternativen finns här:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Alternativ 1: Använda Azure PowerShell Add-AzureVhd för att överföra VHD-filen

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Ett exempel <Uri> kanske ***”https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd”***. Ett exempel <FileInfo> kanske ***”C:\path\to\upload.vhd”***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Alternativ 2: Använder AzCopy för att överföra VHD-filen
Med AzCopy kan överföra du enkelt den virtuella Hårddisken via Internet. Detta kan ta tid beroende på storleken på de virtuella hårddiskarna. Kom ihåg att kontrollera ingång-/ utgång lagringskontogränser när du använder det här alternativet. Se [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md) mer information.

1. Hämta och installera AzCopy härifrån: [senaste versionen av AzCopy](http://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerat.
3. Använd följande kommando för att kopiera VHD-filen från ”källa” till ”mål”.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Nedan följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/ Källa:  *&lt;källa&gt;:***  platsen för mappen eller lagring behållarens Webbadress som innehåller den virtuella Hårddisken.
   * **/ SourceKey:  *&lt;källa kontonyckel&gt;:***  lagringskontonyckel på lagringskontot för källa.
   * **/ Dest:  *&lt;mål&gt;:***  lagring behållar-URL för att kopiera den virtuella Hårddisken till.
   * **/ DestKey:  *&lt;dest kontonyckel&gt;:***  lagringskontonyckel på mål-lagringskontot.
   * **/ BlobType: sidan:** anger att målet är en sidblobb.
   * **/ Mönster:  *&lt;filnamn&gt;:***  ange filnamnet för den virtuella Hårddisken ska kopieras.

Mer information om hur du använder AzCopy verktyget, se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att överföra en virtuell Hårddisk
Du kan också ladda upp en virtuell Hårddisk till ditt lagringskonto med någon av följande metoder:

* [Azure Storage kopiera Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer överför Blobbar](https://azurestorageexplorer.codeplex.com/)
* [Storage Import/Export Service REST API-referens](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Du rekommenderas att använda Import/Export Service om överföring tid är längre än 7 dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) att uppskatta storlek och överför dataenheten tidpunkt.
>
> Import/Export kan användas för att kopiera till ett standardlagringskonto. Du måste kopiera från standardlagring till premium storage-konto med ett verktyg som AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Skapa virtuella Azure-datorer med Premium-lagring
När den virtuella Hårddisken överförs eller kopieras till önskat lagringskonto, följer du instruktionerna i det här avsnittet för att registrera den virtuella Hårddisken som en operativsystemsavbildning eller OS-disken beroende på ditt scenario och sedan skapa en VM-instans från den. Datadisken VHD kan kopplas till den virtuella datorn när den har skapats.
Ett exempelskript för migrering finns i slutet av det här avsnittet. Det här enkla skriptet matchar inte alla scenarier. Du kan behöva uppdatera skriptet att överensstämmer med din situation. Mer information om det här skriptet gäller för ditt scenario finns under [ett exempelskript för migrering](#a-sample-migration-script).

### <a name="checklist"></a>Checklista
1. Vänta tills alla VHD-diskar kopiera har slutförts.
2. Kontrollera att Premium-lagring är tillgänglig i den region du migrerar till.
3. Bestäm den nya VM-serien som du kommer att använda. Det bör vara en Premium-lagring som är kompatibla och storlek bör vara beroende av tillgängligheten i region och baserat på dina behov.
4. Bestäm den exakta VM-storlek som du vill använda. VM-storlek måste vara tillräckligt stor för att stödja antalet datadiskar som du har. T.ex. Om du har 4 datadiskar, måste den virtuella datorn har 2 eller flera kärnor. Du kan också processorkraft, minne och nätverksbandbredd måste.
5. Skapa ett Premium Storage-konto i mål-region. Detta är det konto som ska användas för den nya virtuella datorn.
6. Har den aktuella informationen för VM praktisk, inklusive en lista över diskar och motsvarande VHD-blobbar.

Förbered ditt program för driftstopp. För att göra en ren migrering, måste du stoppa all bearbetning i det aktuella systemet. Först då kan du få till konsekvent tillstånd som du kan migrera till den nya plattformen. Varaktighet för stilleståndstid beror på mängden data på diskarna att migrera.

> [!NOTE]
> Om du skapar en Azure Resource Manager-VM från en särskild VHD-Disk, se [mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) för distribution av Resource Manager VM med hjälp av den befintliga disken.
>
>

### <a name="register-your-vhd"></a>Registrera den virtuella Hårddisken
Skapa en virtuell dator från OS VHD eller ansluta en datadisk till en ny virtuell dator, måste du först registrera dem. Följ instruktionerna nedan scenario för den virtuella Hårddisken.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserad operativsystemet VHD att skapa flera Virtuella Azure-instanser
När generaliserad virtuell Hårddisk för OS-avbildningen har överförts till lagringskontot, registrera den som en **Azure VM-avbildning** så att du kan skapa en eller flera VM-instanser av den. Använd följande PowerShell-cmdlets för att registrera den virtuella Hårddisken i en Azure VM OS-bild. Ange fullständig behållarens Webbadress där VHD kopierades till.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopiera och spara namnet på den här nya Azure VM-avbildning. I exemplet ovan är det *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unik operativsystemet VHD att skapa en instans av Azure VM
När den unika OS virtuella Hårddisken har överförts till lagringskontot, registrera den som en **Azure OS-disken** så att du kan skapa en VM-instans av den. Använd dessa PowerShell-cmdlets för att registrera den virtuella Hårddisken som en Azure OS-Disk. Ange fullständig behållarens Webbadress där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopiera och spara namnet på den här nya Azure OS-disken. I exemplet ovan är det *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Data disk VHD som ska kopplas till nya Virtuella Azure-instanser
När datadisk VHD har överförts till storage-konto du registrera den som en Azure-datadisk så att den kan kopplas till din nya DS-serien, DSv2-serien eller GS-serien Azure VM-instans.

Använd följande PowerShell-cmdlets för att registrera den virtuella Hårddisken som en Azure-datadisk. Ange fullständig behållarens Webbadress där VHD kopierades till.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopiera och spara namnet på den här nya Azure-datadisk. I exemplet ovan är det *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Skapa en Premium-lagring kan virtuell dator
När OS-avbildningen eller OS-disken har registrerats, skapar en ny DS-serien, DSv2-serien eller GS-serien VM. Du kommer att använda operativsystemavbildningen eller operativsystemet diskens namn som du har registrerat. Välj typ av VM Premium lagringsnivå. I exemplet nedan använder den *Standard_DS2* VM-storlek.

> [!NOTE]
> Uppdatera diskstorleken på att se till att den matchar din kapacitet och prestandakrav och tillgängliga Azure diskstorlekar.
>
>

Följ steg för steg PowerShell-cmdlet nedan för att skapa den nya virtuella datorn. Innan du kan definiera de gemensamma parametrarna:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Först skapa en molnbaserad tjänst där du ska vara värd för din nya virtuella datorer.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Skapa sedan den Virtuella Azure-instansen från OS-avbildningen eller OS-Disk som du har registrerat beroende på ditt scenario.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserad operativsystemet VHD att skapa flera Virtuella Azure-instanser
Skapa en eller flera nya DS-serien Azure VM-instanser med hjälp av den **Azure OS-avbildningen** som har registrerats. Ange namnet OS-avbildningen i VM-konfiguration när du skapar ny virtuell dator som visas nedan.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unik operativsystemet VHD att skapa en instans av Azure VM
Skapa en ny DS serien Azure VM instans med hjälp av **Azure OS-disken** som har registrerats. Ange den här OS-disknamnet VM-konfiguration när du skapar den nya virtuella datorn som visas nedan.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Ange andra Virtuella Azure-information, till exempel en molntjänst, region, storage-konto, tillgänglighetsuppsättning och cachelagringsprincipen. Observera att VM-instans måste vara samordnad med associerade operativsystem eller hårddiskar, så det valda moln service, region och lagring kontot måste vara på samma plats som de underliggande virtuella hårddiskarna för dessa diskar.

### <a name="attach-data-disk"></a>Anslut en datadisk
Slutligen, om du har registrerat datadisk virtuella hårddiskar, bifoga dem i den nya Premium-lagring kan Azure VM.

Använda följande PowerShell-cmdlet för att ansluta en datadisk till den nya virtuella datorn och ange principen för cachelagring. I exemplet nedan anges cachelagringsprincipen till *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Det kan finnas ytterligare steg behövs för ditt program som inte omfattas av den här guiden.
>
>

### <a name="checking-and-plan-backup"></a>Kontrollera och planera säkerhetskopiering
När den nya virtuella datorn är igång kan komma åt den med hjälp av samma inloggnings-id och lösenord är som den ursprungliga virtuella datorn och kontrollera att allt fungerar som förväntat. Alla inställningar, inklusive stripe-volymer kan finnas i den nya virtuella datorn.

Det sista steget är att planera säkerhetskopiering och underhållsschemat för den nya virtuella datorn baserat på programmets behov.

### <a name="a-sample-migration-script"></a>Ett exempelskript för migrering
Om du har flera virtuella datorer för att migrera vara automation via PowerShell-skript användbara. Följande är ett exempelskript som automatiserar migreringen av en virtuell dator. Observera som skriptet nedan är bara ett exempel och det finns några antaganden om de aktuella Virtuella diskarna. Du kan behöva uppdatera skriptet att överensstämmer med din situation.

Antaganden är:

* Du skapar klassiska virtuella Azure-datorer.
* Källan OS diskar och källa Datadiskar finns i samma lagringskonto och samma behållare. Om OS-diskar och Datadiskar som inte är på samma plats kan använda du AzCopy eller Azure PowerShell för att kopiera virtuella hårddiskar över storage-konton och behållare. Referera till det föregående steget: [kopiera VHD med AzCopy eller PowerShell](#copy-vhd-with-azcopy-or-powershell). Redigera det här skriptet för att möta ditt scenario är ett annat alternativ, men vi rekommenderar att du använder AzCopy eller PowerShell eftersom det är enklare och snabbare.

Automation-skript finns nedan. Ersätt texten med din information och uppdatera skriptet att överensstämmer med din situation.

> [!NOTE]
> Med det befintliga skriptet bevaras inte nätverkskonfigurationen för ditt Virtuella källdatorn. Du behöver re-config nätverksinställningarna på de migrerade virtuella datorerna.
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
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
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
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
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
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
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
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
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
Din aktuella VM-konfiguration kan anpassas specifikt fungerar bra med standarddiskar. Till exempel att öka prestandan genom att använda många diskar i en stripe-volym. Till exempel i stället för att använda 4 diskar separat på Premium-lagring, du kan optimera kostnaden genom att ha en enda disk. Optimeringar som den här behöver hanteras på ett fall till fall och kräver anpassade åtgärder efter migreringen. Observera också att den här processen och inte fungerar för databaser och program som beror på disklayouten definierat i inställningarna.

##### <a name="preparation"></a>Förberedelse
1. Slutföra migreringen enkla enligt beskrivningen ovan. Optimeringar kommer att utföras på den nya virtuella datorn efter migreringen.
2. Ange nya storlekar för diskar som behövs för den optimerade konfigurationen.
3. Fastställa mappningen av aktuella diskar/volymer till de nya disken specifikationerna.

##### <a name="execution-steps"></a>Utförande
1. Skapa nya diskar med rätt storlek på den virtuella datorn Premium-lagring.
2. Logga in på den virtuella datorn och kopiera data från den aktuella volymen till den nya disken som mappar till volymen. Gör detta för alla aktuella volymer som ska mappas till en ny disk.
3. Sedan ändra programinställningarna växla till nya diskar och koppla från de gamla volymerna.

För att finjustera programmet för bättre diskprestanda, se [optimera programprestanda](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrering av programmet
Databaser och andra komplexa program kan kräva särskilda åtgärder som definierats av programmet providern för migreringen. Se programdokumentationen för respektive. T.ex. vanligtvis databaser kan migreras med säkerhetskopiering och återställning.

## <a name="next-steps"></a>Nästa steg
Se följande resurser för specifika scenarier för att migrera virtuella datorer:

* [Migrera Azure virtuella datorer mellan Storage-konton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Skapa och ladda upp en Windows Server VHD till Azure.](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Skapa och ladda upp en virtuell hårddisk som innehåller Linux-operativsystem](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrering av virtuella datorer från Amazon AWS till Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att du lär dig mer om Azure Storage- och virtuella datorer i Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Virtuella Azure-datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Lagring med höga prestanda för Azure Virtual Machines-arbetsbelastningar](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
