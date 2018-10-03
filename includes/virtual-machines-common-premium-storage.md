---
title: ta med fil
description: ta med fil
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 097fc837807d28e02732cf8820afac74c33e16d9
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240020"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Högpresterande Premium Storage och hanterade diskar för virtuella datorer

Azure Premium Storage tillhandahåller högpresterande och låg latens disksupport för virtuella datorer (VM) med indata/utdata (I/O)-intensiva arbetsbelastningar. VM-diskar som använder Premium Storage lagra data på SSD (solid-state drive). Om du vill dra nytta av hastighet och prestanda för premium-lagringsdiskar, kan du migrera befintliga VM-diskar till Premium Storage.

Du kan koppla flera premium storage-diskar till en virtuell dator i Azure. Använda flera diskar ger ditt program upp till 256 TB lagringsutrymme per virtuell dator, om du använder förhandsversionen-storlekar som ditt program kan ha upp till cirka 2 PiB lagringsutrymme per virtuell dator. Med Premium Storage kan kan dina program uppnå 80 000 i/o-åtgärder per sekund (IOPS) per virtuell dator och en disk genomströmning på upp till 2 000 MB per sekund (MBIT/s) per virtuell dator. Läsåtgärder får du mycket låg fördröjning.

Med Premium Storage erbjuder Azure möjligheten att verkligen lift-and-shift krävande företagsprogram som Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite och SharePoint-grupper till molnet. Du kan köra arbetsbelastningar för prestandakrävande i program som SQL Server, Oracle, MongoDB, MySQL och Redis, som kräver konsekvent hög prestanda och låg fördröjning.

> [!NOTE]
> För bästa prestanda för ditt program rekommenderar vi att du migrerar Virtuella diskar som kräver hög IOPS till Premium Storage. Om disken inte kräver hög IOPS, kan du hjälpa gränsen kostnader genom att lagra den i Azure standardlagring. I standard-lagring lagras Virtuella diskdata på hårddiskar (HDD) i stället för på SSD-enheter.

Azure erbjuder två sätt att skapa premium storage-diskar för virtuella datorer:

* **Ohanterade diskar**

    Den ursprungliga metoden är att använda ohanterade diskar. I en ohanterad disk, kan du hantera de lagringskonton som används för att lagra filerna för virtuell hårddisk (VHD) som motsvarar dina VM-diskar. VHD-filer som lagras som sidblobar i Azure storage-konton. 

* **Hanterade diskar**

    När du väljer [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), hanteras av Azure storage-konton som du använder för dina VM-diskar. Anger typ av disk (Premium eller Standard) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig. Du behöver bekymra dig om att diskarna i flera lagringskonton för att se till att du håller dig inom skalbarhetsgränserna för lagringskontot. Azure hanterar det åt dig.

Vi rekommenderar att du väljer hanterade diskar för att dra nytta av deras många funktioner.

Du kommer igång med Premium Storage kan [skapa ditt kostnadsfria Azure-konto](https://azure.microsoft.com/pricing/free-trial/). 

Information om hur du migrerar dina befintliga virtuella datorer till Premium Storage finns i [konvertera en virtuell Windows-dator från ohanterade diskar till managed disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) eller [konvertera en Linux VM från ohanterade diskar till managed disks](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Premium Storage är tillgängligt i de flesta regionerna. Listan över tillgängliga regioner finns på raden för **disklagring** i [Azure produkttillgänglighet per region](https://azure.microsoft.com/regions/#services).

## <a name="features"></a>Funktioner

Här följer några av funktionerna i Premium Storage:

* **Premium storage-diskar**

    Premium Storage stöder VM-diskar som kan kopplas till specifika storlek-serien för virtuella datorer. Premium Storage stöder en mängd olika virtuella Azure-datorer. Du kan välja mellan åtta GA-diskstorlekar: P4 (32 GiB) P6 (64 GiB) P10 (128 GiB) P15 (256 GB), P20 (512 GiB), P30 (1 024 GiB), P40 (2 048 GiB) P50 (4095 GiB). Samt tre Förhandsgranska diskstorlekar: P60 8 192 GiB (8 TiB) P70 16,348 GiB (16 TiB) P80 32 767 GiB (32 TiB). P4, P6, P60, P70 och P80 diskstorlekar finns för närvarande endast stöd för Managed Disks. Varje diskstorleken har sin egen prestandakrav. Du kan koppla en eller flera diskar till din virtuella dator beroende på dina programkrav. Vi beskriver specifikationer i detalj i [skalbarhets- och prestandamål för Premium Storage](#scalability-and-performance-targets).

* **Premium-sidblobar**

    Premium Storage stöder sidblobar. Använda sidblobar för att lagra beständiga, ohanterade diskar för virtuella datorer i Premium Storage. Till skillnad från standardlagring till Azure Premium Storage inte stöd för blockblobbar, Lägg till blobar, filer, tabeller eller köer. Premium-sidblobar stöder sex storlekar från P10 P50 och P60 (8191GiB). P60 Premium-sidblob stöds inte för att anslutas som VM-diskar. 

    Alla objekt som placerats i en premium storage-konto är en sidblob. Sidans blob fäster till en av de etablerade storlekarna som stöds. Det är därför ett premium storage-konto inte är avsedd att användas för att lagra små blobbar.

* **Premium storage-konto**

    Om du vill börja använda Premium Storage genom att skapa ett premium storage-konto för ohanterade diskar. I den [Azure-portalen](https://portal.azure.com), för att skapa ett premium storage-konto, Välj den **Premium** prestandanivån. Välj den **lokalt redundant lagring (LRS)** replikeringsalternativet. Du kan också skapa ett premium storage-konto genom att ställa in prestandanivå **Premium_LRS**. Om du vill ändra prestandanivån, använder du någon av följande metoder:
     
    - [PowerShell för Azure Storage](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure CLI för Azure Storage](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp) (för Azure Resource Manager distributioner) eller en av Azure Storage resource provider-klientbibliotek

    Läs om gränser för premium storage-konto i [skalbarhets- och prestandamål för Premium Storage](#premium-storage-scalability-and-performance-targets).

* **Lokalt redundant Premium-lagring**

    Ett premiumlagringskonto stöder endast lokalt redundant lagring som replikeringsalternativ. Lokalt redundant lagring håller tre kopior av data inom en enda region. För regional haveriberedskap, måste du säkerhetskopiera dina VM-diskar i en annan region med hjälp av [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). Du måste också använda ett geo-redundant lagring (GRS)-konto som säkerhetskopieringsvalvet. 

    Använder Azure storage-konto som en behållare för dina ohanterade diskar. När du skapar en Azure virtuell dator som har stöd för Premium Storage med ohanterade diskar, och du väljer ett premium storage-konto, lagras ditt operativsystem och datadiskar i det lagringskontot.

## <a name="supported-vms"></a>Virtuella datorer som stöds

Premium Storage stöds på en mängd olika virtuella Azure-datorer. Du kan använda standard och premium storage-diskar med dessa typer av virtuella datorer. Du kan inte använda premium storage-diskar med VM-serien som inte är Premium Storage-kompatibel.


Information om VM-typer och storlekar i Azure för Windows finns i artikeln [VM-storlekar för Windows](../articles/virtual-machines/windows/sizes.md). Information om VM-typer och storlekar i Azure för Linux finns i artikeln [VM-storlekar för Linux](../articles/virtual-machines/linux/sizes.md).

Det här är några av de funktioner som stöds i premium storage-aktiverade virtuella datorer:

* **Tillgänglighetsuppsättning**

    Exempel på en virtuella datorer i DS-serien kan, du använda en DS-serien virtuell dator till en molntjänst med endast DS-serien virtuella datorer. Lägg inte till virtuella datorer i DS-serien i en befintlig molntjänst som har en annan typ än virtuella datorer i DS-serien. Du kan migrera dina befintliga VHD: er till en ny molntjänst som körs endast DS-serien virtuella datorer. Om du vill använda samma virtuella IP-adress för den nya Molntjänsten som är värd för dina DS-serien virtuella datorer, Använd [reserverade IP-adresser](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

* **Operativsystemets disk**

    Du kan ställa in dina virtuella datorer i Premium Storage för att använda en premium- eller en standardoperativsystem disk. För bästa resultat bör du använda en disk i Premium Storage-baserat operativsystem.

* **Datadiskar**

    Du kan använda premium och standard-diskar i samma Premium Storage VM. Med Premium Storage kan du etablera en virtuell dator och koppla flera beständiga datadiskar till den virtuella datorn. Om du vill öka kapaciteten och prestandan för volymen, kan du stripe över dina diskar.

    > [!NOTE]
    > Om du stripe-data för premiumlagringsdiskar med hjälp av [lagringsutrymmen](http://technet.microsoft.com/library/hh831739.aspx), konfigurera lagringsutrymmen med 1 kolumn för varje disk som du använder. I annat fall kan prestanda i stripe-volymen vara lägre än förväntat på grund av en ojämn fördelning av trafik på diskarna. Du kan ställa in kolumner för upp till 8 diskar som standard i Serverhanteraren. Om du har mer än 8 diskar kan du använda PowerShell för att skapa volymen. Ange antalet kolumner manuellt. I annat fall fortsätter Serverhanteraren UI att använda 8 kolumner, även om du har fler diskar. Exempel: Om du har 32 diskar i en enda stripe-uppsättning, ange 32 kolumner. Ange antalet kolumner i den virtuella disken använder den [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell-cmdleten, Använd den *NumberOfColumns* parametern. Mer information finns i [översikt över lagringsutrymmen](http://technet.microsoft.com/library/hh831739.aspx) och [Storage Spaces vanliga frågor och svar](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    Virtuella datorer (VM) som har stöd för Premium Storage har en unika funktionen för cachelagring för högre dataflöde och minskad fördröjning. Deras cachelagring förmåga överskrider underliggande premium storage-diskprestanda. Inte alla virtuella datorer support cachelagring dock så kontrollera VM-specifikationer för VM-storlekar som du vill veta mer.  Virtuella datorer som har stöd för cachelagring indikerar detta i deras spec med ett ”maximalt genomflöde för cachelagring och temporär lagring”-mått.  De också anges direkt under rubriken VM.
    
    Med cachelagring – du kan ställa in disken Cachelagringsprincip på premium-lagringsdiskar till **ReadOnly**, **ReadWrite**, eller **ingen**. Standard disken Cachelagringsprincip är **ReadOnly** för alla diskar för premium-data, och **ReadWrite** för operativsystemdiskar. Kom ihåg att använda rätt cache-inställningen för optimala prestanda för ditt program ange. 
    
    Som exempel för Läs omfattande eller skrivskyddad datadiskar, till exempel SQL Server-datafiler, ställa in att Cachelagringsprincip till **ReadOnly**. För hög eller lässkyddad datadiskar, till exempel loggfiler för SQL Server, ställa in att Cachelagringsprincip till **ingen**. 
    
    Mer information om hur du optimerar din design med Premium Storage finns [Design för prestanda med Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analys**

    För att analysera prestanda för virtuella datorer med hjälp av diskar i Premium-lagring, aktivera diagnostik för virtuella datorer i den [Azure-portalen](https://portal.azure.com). Mer information finns i [Azure VM-övervakning med Azure Diagnostics-tillägget](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Om du vill se diskprestanda med operativsystem-baserade verktyg som [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) för virtuella Windows-datorer och [iostat](http://linux.die.net/man/1/iostat) kommandot för virtuella Linux-datorer.

* **VM-gränser för skalning och prestanda**

    Varje Premium-lagring stöds VM-storlek har gränser för skalning och prestanda specifikationer för IOPS, bandbredd och antalet diskar som kan bifogas per virtuell dator. När du använder premium storage-diskar med virtuella datorer kan du kontrollera att det finns tillräckligt med IOPS och bandbredd på den virtuella datorn ska disktrafik för enheten.

    Till exempel har en STANDARD_DS1 virtuell dator en dedikerad bandbredd på 32 MB/s för premium disk lagringstrafik. En P10 premium storage disk kan ge en bandbredd på 100 MB/s. Om en P10 premium storage-disken är ansluten till den här virtuella datorn, kan den bara gå upp till 32 MB/s. Det kan inte använda den maximalt 100 MB/s som P10-disk kan ge.

    Den största virtuella datorn i DS-serien är för närvarande Standard_DS15_v2. Standard_DS15_v2 kan ge upp till 960 MB/s för alla diskar. Den största virtuella datorn i GS-serien är den standard gs5. Den standard gs5 kan ge upp till 2 000 MB/s för alla diskar.

    Dessa gränser är för disktrafik. Dessa gränser inkluderar inte cacheträffar och nätverkstrafik. Det finns en separat bandbredd för VM-nätverkstrafik. Bandbredd för nätverkstrafik skiljer sig från dedikerad bandbredd som används av premium storage-diskar.

    Den senaste informationen om högsta IOPS och dataflöde (bandbredd) för Premium Storage-stöd för virtuella datorer, se [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md) eller [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md).

    Se tabellen i nästa avsnitt för mer information om premium storage-diskar och deras gränser för IOPS och dataflöden.

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda
I det här avsnittet beskriver vi mål för skalbarhet och prestanda att tänka på när du använder Premium Storage.

Premium storage-konton har följande skalbarhetsmål:

| Total kapacitet | Totala bandbredden för ett lokalt redundant lagringskonto |
| --- | --- | 
| Disk-kapacitet: 35 TB <br>Ta en ögonblicksbild kapacitet: 10 TB | Upp till 50 Gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> alla data (förfrågningar) som skickas till ett lagringskonto

<sup>2</sup> alla data (svar) som tas emot från ett lagringskonto

Mer information finns i [skalbarhets- och prestandamål i Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Om du använder premium storage-konton för ohanterade diskar och programmet överskrider det för skalbarhetsmål för ett enda lagringskonto, kanske du vill migrera till managed disks. Om du inte vill migrera till managed disks kan du skapa programmet så att flera lagringskonton. Sedan kan partitionera dina data på dessa lagringskonton. Till exempel om du vill koppla 51 TB diskar mellan flera virtuella datorer fördelade dem på två lagringskonton. 35 TB är gränsen för en enda premium storage-konto. Se till att ett enda lagringskonto aldrig har mer än 35 TB etablerade diskar.

### <a name="premium-storage-disk-limits"></a>Gränser för Premium Storage disk
När du etablerar en premium-lagringsdisk anger storleken på disken högsta IOPS och dataflöde (bandbredd). Azure erbjuder åtta typer av premium-lagringsdiskar: P4 (hanterade diskar endast), P6 (hanterade diskar endast), P10, P15, P20, P30, P40 och P50. Varje typ av premiumlagring har specifika gränser för IOPS och dataflöde. Begränsningar för disktyper beskrivs i följande tabell:

| Premium-diskar typ | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 | |---|---|---|---|---|---|---|---|---|| -------|| -------|| -------| | Diskstorlek | 32 giB | 64 giB | 128 giB | 256 GB | 512 giB | 1 024 giB (1 TiB) | 2048 giB (2 TiB) | 4 095 giB (4 TiB) | 8192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (32 TiB) || IOPS per disk | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7500 | 7500 | 12 500 | 15 000 | 20 000 || Dataflöde per disk | 25 MB per sekund | 50 MB per sekund | 100 MB per sekund | 125 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund | 480 MB per sekund | 750 MB per sekund | 750 MB per sekund |

> [!NOTE]
> Kontrollera att tillräckligt mycket bandbredd är tillgänglig på den virtuella datorn ska enhet disktrafik, enligt beskrivningen i [Premium-lagring stöds virtuella datorer](#premium-storage-supported-vms). Annars kan är ditt dataflöde och IOPS begränsad till lägre värden. Maximalt dataflöde och IOPS baseras på VM-begränsningar, inte i diskgränser som beskrivs i tabellen ovan.  
> Azure har utformats för Premium Storage-plattformen för att vara massivt parallella. Designa programmet är flertrådat hjälper till att uppnå höga prestanda-mål som erbjuds på större diskstorlekar.

Här följer några viktiga saker du behöver veta om skalbarhets- och prestandamål för Premium Storage:

* **Etablerad kapacitet och prestanda**

    När du etablerar en disk för premium storage, till skillnad från standardlagring, garanterat kapacitet, IOPS och dataflöde på disken. Exempelvis kan etablerar du skapa en P50-disk Azure 4 095 GB lagringskapacitet, 7 500 IOPS och 250 MBIT/s genomströmning för disken. Ditt program kan använda hela eller delar av kapacitet och prestanda.

* **Diskstorlek**

    Azure mappar diskens storlek (avrundas uppåt) till närmaste premium disk alternativet, som anges i tabellen i föregående avsnitt. Till exempel klassificeras en diskstorlek på 100 GB som en P10-alternativet. Den kan utföra upp till 500 IOPS, med upp till 100 MBIT/s genomströmning. På samma sätt kan en disk som 400 GB klassificeras som en P20. Den kan utföra upp till 2 300 IOPS, med 150 MBIT/s genomströmning.

    > [!NOTE]
    > Du kan enkelt öka storleken på befintliga diskar. Du kanske exempelvis vill öka storleken på en 30 GB-disk på 128 GB eller 1 TB. Eller så kan du konvertera dina P20-disk till en P30-disk eftersom du behöver mer kapacitet eller fler IOPS och dataflöde. 

* **I/o-storlek**

    Storleken på en i/o är 256 KB. Om de data som överförs är mindre än 256 KB, betraktas den 1 i/o-enhet. Större i/o-storlekar räknas som flera I/o för storlek på 256 KB. Till exempel räknas 1 100 KB i/o som 5 i/o-enheter.

* **Dataflöde**

    Dataflöde gränsen gäller skrivningar till disken och innehåller disk läsåtgärder som inte hämtas från cachen. Till exempel har en P10-disk 100 MBIT/s dataflöde per disk. I följande tabell visas några exempel på giltiga dataflödet för en P10-disk:

    | Högsta dataflöde per P10-disk | Icke-cache läser från disk | Icke-cache skrivningar till disken |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Cacheträffar**

    Cacheträffar begränsas inte av den allokerade IOPS eller dataflöde på disken. Till exempel när du använder en datadisk med en **ReadOnly** cache-inställningen på en virtuell dator som stöds av Premium Storage kan läsningar som hämtas från cachen omfattas inte av IOPS och dataflöden caps på disken. Om arbetsbelastningen på disken är främst läser du kan få mycket hög genomströmning. Cachen lyder separat IOPS och dataflöde gränser i den virtuella datorn på, baserat på virtuella datorstorlek. Virtuella datorer i DS-serien har ungefär 4 000 IOPS och dataflöden för 33 MBIT/s per kärna för cache och lokal SSD-i/o. Virtuella datorer i GS-serien har en gräns på 5 000 IOPS och dataflöden för 50 MBIT/s per kärna för cache och lokal SSD-i/o.

## <a name="throttling"></a>Begränsning

Begränsning kan inträffa om programmet IOPS eller dataflöde överskrider den allokerade gränserna för en premium-lagringsdisk. Begränsning också uppstå om din totala disktrafik över alla diskar på den virtuella datorn är längre än disk bandbredd tillgänglig för den virtuella datorn. Om du vill undvika begränsning, rekommenderar vi att du begränsar antalet väntande i/o-begäranden för disken. Använda en gräns baserat på mål för skalbarhet och prestanda för disken som du har etablerat och på den diskbandbredden som är tillgängliga för den virtuella datorn.  

Ditt program kan uppnå lägsta möjliga svarstid när den är utformad för att undvika begränsning. Men om antalet väntande i/o-begäranden för disken är för liten, kan inte ditt program utnyttja maximal IOPS och dataflödesnivåer som är tillgängliga för disken.

Följande exempel visar hur du beräknar begränsning nivåer. Alla beräkningar baseras på en storlek på i/o på 256 KB.

### <a name="example-1"></a>Exempel 1

Programmet har bearbetat 495 i/o-enheter med 16 KB storlek på en sekund på en P10-disk. I/o-enheter räknas som 495 IOPS. Om du ett 2 MB i/o i samma sekund, är det totala antalet i/o-enheter för lika 495 + 8 IOPS. Detta beror på 2 MB i/o = 2 048 KB / 256 KB = 8 i/o-enheter, om i/o-enhet är 256 KB. Eftersom summan av 495 + 8 överskrider 500 IOPS-gränsen för disken, inträffar begränsning.

### <a name="example-2"></a>Exempel 2

Programmet har bearbetat 400 i/o-enheter med 256 KB storlek på en P10-disk. Den totala bandbredden som förbrukas är (400 &#215; 256) / 1 024 KB = 100 MB/s. En P10-disk har en gräns för genomflöde på 100 MB/s. Om programmet försöker utföra flera i/o-åtgärder i den andra, är den begränsad eftersom den överskrider allokerade gränsen.

### <a name="example-3"></a>Exempel 3

Du har en DS4-VM med två P30-diskar som är anslutna. Varje P30-disk kan 200 MBIT/s genomströmning. Men har en DS4 virtuell dator en total diskkapacitet bandbredd på 256 MB/s. Du kan inte driva båda anslutna diskar till maximalt dataflöde på den här DS4-VM på samma gång. För att lösa problemet, kan du hantering av trafik över 200 MB/s på en disk och 56 MB/s på den andra. Om summan av disk trafiken går via 256 MB/s, är disktrafik begränsad.

> [!NOTE]
> Om din disktrafik mestadels består av små i/o-storlekar, överskrids ditt program som sannolikt IOPS-gränsen innan dataflödesgräns. Men om disken trafiken består främst av stora i/o-storlekar, överskrids ditt program som sannolikt dataflödesgräns först, i stället för IOPS-gränsen. Du kan maximera programmets IOPS och dataflödeskapacitet genom att använda bästa i/o-storlekar. Du kan också begränsa antalet väntande i/o-begäranden för en disk.

Läs mer om design för hög prestanda med Premium Storage i [Design för prestanda med Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Ögonblicksbilder och kopiering av Blob

VHD-filen är en sidblobb till Storage-tjänsten. Du kan ta ögonblicksbilder av sidblobar och kopiera dem till en annan plats, till exempel till ett annat lagringskonto.

### <a name="unmanaged-disks"></a>Ohanterade diskar

Skapa [inkrementella ögonblicksbilder](../articles/virtual-machines/linux/incremental-snapshots.md) för ohanterade premium-diskar på samma sätt som du använder ögonblicksbilder med standardlagring. Premium Storage stöder endast lokalt redundant lagring som replikeringsalternativ. Vi rekommenderar att du skapar ögonblicksbilder och sedan kopiera ögonblicksbilderna till ett geo-redundant standardlagringskonto. Mer information finns i [redundansalternativ för Azure Storage](../articles/storage/common/storage-redundancy.md).

Om en disken är ansluten till en virtuell dator, tillåts inte vissa API-åtgärder på disken. Exempel: du kan inte utföra en [kopiering av Blob](/rest/api/storageservices/Copy-Blob) åtgärden på den blobben som om disken är ansluten till en virtuell dator. I stället först skapa en ögonblicksbild av blobben med den [ta ögonblicksbild av Blob](/rest/api/storageservices/Snapshot-Blob) REST API. Utför sedan den [kopiering av Blob](/rest/api/storageservices/Copy-Blob) ögonblicksbild att kopiera den anslutna disken. Du kan också koppla från disken och sedan utföra alla nödvändiga åtgärder.

Följande begränsningar gäller för premium storage blob-ögonblicksbilder:

| Gränsen för Premium-lagring | Värde |
| --- | --- |
| Högsta antalet ögonblicksbilder per blob | 100 |
| Kapacitet för lagringskonton för ögonblicksbilder<br>(Innehåller data i bara ögonblicksbilder. Inkluderar inte data i grundläggande blob.) | 10 TB |
| Den minsta tiden mellan på varandra följande ögonblicksbilder | 10 minuter |

Om du vill underhålla geo-redundanta kopior av dina ögonblicksbilder kan kopiera du ögonblicksbilder från ett premium storage-konto till ett geo-redundant standardlagringskonto med hjälp av AzCopy eller kopiering av Blob. Mer information finns i [överföra data med kommandoradsverktyget azcopy](../articles/storage/common/storage-use-azcopy.md) och [kopiering av Blob](/rest/api/storageservices/Copy-Blob).

Detaljerad information om hur du utför REST-åtgärder mot sidblobar i ett premium storage-konto finns i [Bloboperationer i tjänsten med Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Hanterade diskar

En ögonblicksbild för en hanterad disk är en skrivskyddad kopia av den hantera disken. Ögonblicksbilden lagras som en hanterad disk som standard. För närvarande [inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) stöds inte för hanterade diskar. Läs hur du tar en ögonblicksbild för en hanterad disk i [skapar en kopia av en virtuell Hårddisk som lagras som en Azure-hanterad disk med hjälp av hanterade ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) eller [skapar en kopia av en virtuell Hårddisk som lagras som en Azure-hanterad disk med hjälp av hanterad ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Om en hanterad disk är kopplat till en virtuell dator, tillåts inte vissa API-åtgärder på disken. Exempelvis kan du generera en signatur för delad åtkomst (SAS) för att utföra en kopieringsåtgärd när disken är ansluten till en virtuell dator. I stället först skapa en ögonblicksbild av disken och sedan utföra kopieringen av ögonblicksbilden. Du kan också koppla från disken och sedan generera en SAS för att utföra kopieringen.


## <a name="premium-storage-for-linux-vms"></a>Premiumlagring för virtuella Linux-datorer
Du kan använda följande information för att konfigurera din virtuella Linux-datorer i Premium Storage:

Få skalbarhetsmål i Premium-lagring för alla premium-lagringsdiskar med cache inställd på **ReadOnly** eller **ingen**, måste du inaktivera ”hinder” när du monterar filsystemet. Du behöver inte hinder i det här scenariot eftersom skrivningar till premium storage-diskar finns under cacheinställningarna. När skrivbegäran slutförs har data skrivits till det beständiga arkivet. Använd någon av följande metoder om du vill inaktivera ”hinder”. Välja för ditt filsystem:
  
* För **reiserFS**, för att inaktivera hinder, Använd den `barrier=none` montera alternativet. (Om du vill aktivera hinder, Använd `barrier=flush`.)
* För **ext3/ext4**, för att inaktivera hinder, Använd den `barrier=0` montera alternativet. (Om du vill aktivera hinder, Använd `barrier=1`.)
* För **XFS**, för att inaktivera hinder, Använd den `nobarrier` montera alternativet. (Om du vill aktivera hinder, Använd `barrier`.)
* För premium storage-diskar med cache inställd **ReadWrite**, aktivera hinder för skrivning tillförlitlighet.
* Volymen etiketter ska finnas kvar när du startar om den virtuella datorn, måste du uppdatera/etc/fstab med universell unik identifierare (UUID) referenser till diskarna. Mer information finns i [lägga till en hanterad disk i en Linux VM](../articles/virtual-machines/linux/add-disk.md).

Följande Linux-distributioner har verifierats för Azure Premium Storage. För bättre prestanda och stabilitet med Premium Storage rekommenderar vi att du uppgraderar dina virtuella datorer till en av dessa versioner, lägst (eller en senare version). Vissa av versionerna som kräver den senaste Linux Integration Services (LIS), v4.0, för Azure. Om du vill hämta och installera en distributionsplats, följer du länken som visas i följande tabell. Vi lägga till avbildningar i listan eftersom vi att slutföra verifieringen. Observera att våra verifieringar visar att prestanda varierar för varje bild. Prestanda beror på arbetsbelastningen och dina inställningar för avbildningen. Olika bilder är anpassade för olika typer av arbetsbelastningar.

| Distribution | Version | Stöds kernel | Information |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| SUSE-sles-12-prioritet-v20150213 <br> SUSE-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 krävs](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Se anmärkning i nästa avsnitt* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 rekommenderas](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Se anmärkning i nästa avsnitt* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 eller RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 eller RHCK med[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 eller RHCK med[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>LIS drivrutiner för OpenLogic CentOS

Om du kör OpenLogic CentOS virtuella datorer, kör du följande kommando för att installera de senaste drivrutinerna:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Starta om den virtuella datorn för att aktivera de nya drivrutinerna.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Premiumlagring, gäller följande för debitering:

* **Premium storage disk och blob-storlek**

    Faktureringen för en premium storage disk eller blob är beroende av etablerade storleken på disk eller blob. Azure mappar den storlek (avrundas uppåt) till närmaste premium disk lagringsalternativet. Mer information finns i tabellen i [skalbarhets- och prestandamål för Premium Storage](#premium-storage-scalability-and-performance-targets). Varje disk mappas till en etablerad diskstorleken som stöds, och faktureras därefter. Debiteringen för etablerade diskar beräknas per timme med hjälp av det månatliga priset för Premium Storage-erbjudandet. Till exempel om du har etablerat en P10-disk och tar bort den efter 20 timmar, debiteras du för erbjudandet P10 beräknat på 20 timmar. Det här är oavsett mängden faktiska data som skrivs till disken eller IOPS och dataflöde som används.

* **Premium unmanaged disks-ögonblicksbilder**

    Ögonblicksbilder på ohanterade premiumdiskar faktureras för ytterligare kapacitet som används av ögonblicksbilder. Läs mer om ögonblicksbilder [skapa en ögonblicksbild av en blob](/rest/api/storageservices/Snapshot-Blob).

* **Premium-hanterade diskar ögonblicksbilder**

    En ögonblicksbild av en hanterad disk är en skrivskyddad kopia av disken. Disken lagras som en hanterad disk som standard. En ögonblicksbild kostar samma som en hanterad disk. Om du skapar en ögonblicksbild av en hanterad disk 128 GB premium är till exempel kostnaden för ögonblicksbilden motsvarar en 128 GB hanterade standarddiskar.  

* **Utgående dataöverföringar**

    [Utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data från Azure-datacenter) debiteras för bandbreddsanvändning.

Detaljerad information om priser för Premium Storage finns stöds av Premium Storage-datorer och hanterade diskar i dessa artiklar:

* [Prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Azure Backup-support

För regional haveriberedskap, måste du säkerhetskopiera dina VM-diskar i en annan region med hjälp av [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) och ett GRS-lagringskonto som ett säkerhetskopieringsvalv.

Om du vill skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior, använder enkel återställning av virtuell dator och lagringsprinciper för säkerhetskopiering, Azure Backup. Du kan använda säkerhetskopiering båda med ohanterade och hanterade diskar. Mer information finns i [Azure Backup för virtuella datorer med ohanterade diskar](../articles/backup/backup-azure-vms-first-look-arm.md) och [Azure Backup för virtuella datorer med hanterade diskar](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Nästa steg

Mer information om Premium Storage finns i följande artiklar.
