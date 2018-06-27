---
title: ta med fil
description: ta med fil
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 5cbe6f1f8f15e9da8e1fe6961d3da9b9e2a31e4b
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806391"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Premium-lagring med hög prestanda och hanterade diskar för virtuella datorer
Azure Premium Storage ger stöd för hög prestanda, låg latens diskar för virtuella datorer (VM) med in-/ utdata (I/O)-intensiv arbetsbelastning. Virtuella diskar som använder Premium-lagring kan du lagra data på SSD-enheter (SSD). Om du vill dra nytta av hastighet och prestanda för lagring av premiumdiskar, kan du migrera befintliga Virtuella diskar till Premium-lagring.

Du kan bifoga flera diskar i premium-lagring till en virtuell dator i Azure. Använda flera diskar ger dina program upp till 256 TB lagringsutrymme per virtuell dator. Med Premium-lagring kan du uppnå 80 000 i/o-åtgärder per sekund (IOPS) per VM och en disk genomflöde i upp till 2 000 MB per sekund (MB/s) per VM dina program. Läsåtgärder ger mycket låg latens.

Azure erbjuder möjligheten att verkligen lift-och-SKIFT krävande företagsprogram som Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite och SharePoint-servergrupper till molnet med Premium-lagring. Du kan köra prestanda-intensiva arbetsbelastningar i program som SQL Server, Oracle, MongoDB, MySQL och Redis, vilket kräver att konsekvent hög prestanda och låg latens.

> [!NOTE]
> För bästa prestanda för ditt program rekommenderar vi att du migrerar Virtuella diskar som kräver hög IOPS till Premium-lagring. Om disken inte kräver hög IOPS, kan du hjälpa gränsen kostnader genom att hålla den i Azure standardlagring. Standardlagring lagras VM diskdata på hårddiskar (HDD) i stället för på SSD-enheter.
> 

Azure erbjuder två sätt att skapa premiumdiskar med lagringsutrymme för virtuella datorer:

* **Ohanterade diskar**

    Den ursprungliga metoden är att använda ohanterade diskar. Ohanterad disken, kan du hantera storage-konton som används för att lagra filer för virtuell hårddisk (VHD) som motsvarar dina VM-diskar. VHD-filer lagras som sidblobbar i Azure storage-konton. 

* **Hanterade diskar**

    När du väljer [Azure hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md), hanteras av Azure storage-konton som du använder för din Virtuella diskar. Anger typ av disk (Standard eller Premium) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig. Du behöver inte bry dig om att diskarna i flera lagringskonton så att du håller dig inom skalbarhetsbegränsningar för dina lagringskonton. Azure hanterar som du.

Vi rekommenderar att du väljer hanterade diskar för att dra nytta av de många funktionerna.

Du kommer igång med Premium-lagring [skapa din kostnadsfria Azure-konto](https://azure.microsoft.com/pricing/free-trial/). 

Information om att migrera dina befintliga virtuella datorer till Premium-lagring finns [konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) eller [konvertera en Linux VM från ohanterade diskar till hanterade diskar](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Premium-lagring finns i de flesta regioner. I listan över tillgängliga regioner finns i raden för **disklagring** i [Azure produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/#services).
> 

## <a name="features"></a>Funktioner

Här följer några av funktionerna i Premium-lagring:

* **Premium-lagringsdiskar**

    Premium-lagring stöder Virtuella diskar som kan kopplas till specifika storlek-serien virtuella datorer. Premium-lagring stöder DS-serien, DSv2-serien GS-serien, Ls-serien, Fs-serien och Esv3-serien virtuella datorer. Du kan välja mellan sju diskstorlekar: P4 (32 GB) P6 (64 GB) P10 (128 GB) P20 (512 GB), P30 (1024 GB), P40 (2 048 GB), p 50 (4095 GB). P4 och P6 diskstorlekar stöds ännu bara för hanterade diskar. Varje diskstorleken har sin egen prestandakrav. Beroende på kraven för application kan du koppla en eller flera diskar till den virtuella datorn. Beskrivs i detalj i specifikationerna [Premium-lagring skalbarhets- och prestandamål](#scalability-and-performance-targets).

* **Premium-sidblobbar**

    Premium-lagring stöder sidblobar. Använda sidblobar för att lagra beständiga ohanterade diskar för virtuella datorer i Premium-lagring. Till skillnad från vanliga Azure Storage Premium-lagring inte stöder blockblobbar, Lägg till blobbar, filer, tabeller eller köer. Premium-sidblobbar stöder sex storlekar från P10 p 50 och P60 (8191GiB). P60 Premium sidblob stöds inte för att kopplas till Virtuella diskar. 

    Alla objekt som placerats i ett premiumlagringskonto blir en sidblobb. Sidan blob fäster till någon av de etablerade storlekarna som stöds. Det är därför ett premiumlagringskonto inte är avsedd att användas för att lagra små blobbar.

* **Premium-lagringskonto**

    Du börjar använda Premium-lagring genom att skapa ett premiumlagringskonto för ohanterade diskar. I den [Azure-portalen](https://portal.azure.com), för att skapa ett premiumlagringskonto, Välj den **Premium** prestandanivå. Välj den **lokalt redundant lagring (LRS)** replikeringsalternativet. Du kan också skapa ett premiumlagringskonto genom att ange vilken prestandanivå **Premium_LRS**. Om du vill ändra nivån av prestanda, använder du någon av följande metoder:
     
    - [PowerShell för Azure Storage](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure CLI för Azure Storage](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp) (för Azure Resource Manager distributioner) eller ett Azure Storage resource provider-klientbibliotek

    Läs om premium lagringskontogränser i [Premium-lagring skalbarhets- och prestandamål](#premium-storage-scalability-and-performance-targets).

* **Lokalt redundant Premium-lagring**

    Ett premiumlagringskonto stöder endast lokalt redundant lagring som replikeringsalternativet. Lokalt redundant lagring behåller tre kopior av data inom en enskild region. För regional katastrofåterställning, du måste säkerhetskopiera Virtuella diskar i en annan region med hjälp av [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). Du måste också använda ett konto med geo-redundant lagring (GRS) som säkerhetskopieringsvalvet. 

    Azure använder ditt lagringskonto som en behållare för ohanterade diskarna. När du skapar en virtuell Azure-dator som har stöd för Premium-lagring med ohanterad diskar, och du väljer ett premiumlagringskonto, lagras operativsystemet och datadiskarna i detta lagringskonto.

## <a name="supported-vms"></a>Virtuella datorer som stöds

Premium-lagring stöder B-serien, DS-serien, DSv2-serien, DSv3-serien, Esv3-serien, GS-serien, Ls-serien, M-serien och Fs-serien virtuella datorer. Du kan använda standard och premium-lagringsdiskar med dessa VM-typer. Du kan inte använda premium lagringsdiskar med VM-serien som inte är Premium Storage-kompatibel.


Information om VM-typer och storlekar i Azure för Windows finns i artikeln [VM-storlekar för Windows](../articles/virtual-machines/windows/sizes.md). Information om VM-typer och storlekar i Azure för Linux finns i artikeln [VM-storlekar för Linux](../articles/virtual-machines/linux/sizes.md).

Det här är några av funktionerna i DS-serien, DSv2-serien GS-serien, Ls-serien och Fs-serien virtuella datorer:

* **Molntjänsten**

    Du kan lägga till DS-serien virtuella datorer till en molntjänst som har endast DS-serien virtuella datorer. Lägg inte till DS-serien virtuella datorer i en befintlig molntjänst som har en annan typ än DS-serien virtuella datorer. Du kan migrera dina befintliga virtuella hårddiskar till en ny molntjänst som kan köras endast DS-serien virtuella datorer. Om du vill använda samma virtuella IP-adress för den nya Molntjänsten som är värd för dina DS-serien virtuella datorer, Använd [reserverad IP-adress](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). GS-serien virtuella datorer kan läggas till en befintlig molntjänst som har endast GS-serien virtuella datorer.

* **Operativsystemdisken**

    Du kan ställa in din Premium-lagring VM för att använda en premium eller en standardoperativsystem disk. För bästa resultat bör du använda en disk i Premium-lagring-baserat operativsystem.

* **Datadiskar**

    Du kan använda premium och standarddiskar i samma VM för Premium-lagring. Med Premium-lagring kan du etablera en virtuell dator och koppla flera beständiga datadiskar till den virtuella datorn. Om du vill öka kapaciteten och prestandan för volymen, kan du stripe över diskarna.

    > [!NOTE]
    > Om du stripe-datadiskar för premium-lagring med hjälp av [lagringsutrymmen](http://technet.microsoft.com/library/hh831739.aspx), konfigurera lagringsutrymmen med 1 kolumn för varje disk som du använder. Annars kan prestandan stripe-volymen vara lägre än förväntat på grund av en ojämn fördelning av trafik över diskar. Som standard i Serverhanteraren kan du skapa kolumner för upp till 8 diskar. Om du har mer än 8 diskar kan du använda PowerShell för att skapa volymen. Ange antalet kolumner manuellt. Annars fortsätter Serverhanteraren UI att använda 8 kolumner, även om du har flera diskar. Ange till exempel 32 kolumner om du har 32 diskar i en enda stripe-uppsättning. Ange antalet kolumner i den virtuella disken använder den [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet, Använd den *NumberOfColumns* parameter. Mer information finns i [översikt över lagringsutrymmen](http://technet.microsoft.com/library/hh831739.aspx) och [lagring lagringsutrymmen vanliga frågor och svar](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    Virtuella datorer i serien storlek som har stöd för Premium-lagring har en unik cachelagring funktion för hög genomflöde och svarstid. Cachelagring kapaciteten överskrider underliggande diskprestanda för premium-lagring. Du kan ställa in disken cachelagring principen på premium lagringsdiskar till **ReadOnly**, **ReadWrite**, eller **ingen**. Standard-disken princip för cachelagring är **ReadOnly** för alla diskar för premium-data, och **ReadWrite** för operativsystemet diskar. Använd rätt cache-inställningen för optimala prestanda för ditt program. Till exempel för frekventa Läs-eller skrivskyddad diskar, till exempel SQL Server-datafiler, ställa in att cachelagring principen till **ReadOnly**. För skrivintensiv eller lässkyddad datadiskar som SQL Server-loggfiler, ställa in att cachelagring principen till **ingen**. Mer information om hur du optimerar din design med Premium-lagring finns [Design för prestanda med Premium-lagring](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analys**

    För att analysera VM prestanda med hjälp av diskar i Premium-lagring, aktivera diagnostik för Virtuella datorer i den [Azure-portalen](https://portal.azure.com). Mer information finns i [Azure VM övervakning med Azure Diagnostics tillägget](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Om du vill se diskprestanda, Använd operativsystem baserat verktyg som [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) för virtuella Windows-datorer och [iostat](http://linux.die.net/man/1/iostat) kommandot för virtuella Linux-datorer.

* **VM scale begränsningar och prestanda**

    Varje Premium-lagring stöds VM-storlek har gränser och prestandakrav för IOPS, bandbredd och antalet diskar som kan bifogas per virtuell dator. När du använder premiumdiskar för lagring med virtuella datorer kan du kontrollera att det finns tillräcklig IOPS och bandbredd på den virtuella datorn till enheten disk trafik.

    En virtuell dator STANDARD_DS1 har till exempel en dedikerad bandbredd på 32 MB/s för premium lagringstrafik disk. En disk med P10 premium-lagring kan ge en bandbredd på 100 MB/s. Om en P10 premium storage disk är kopplad till den här virtuella datorn kommer gå den bara upp till 32 MB/s. Den kan inte använda den maximala 100 MB/s som P10 disken kan ge.

    Största VM i DS-serien är för närvarande i Standard_DS15_v2. Standard_DS15_v2 kan ange upp till 960 MB/s över alla diskar. Största VM i GS-serien är Standard_GS5. Standard_GS5 kan ange upp till 2 000 MB/s över alla diskar.

    Dessa begränsningar gäller för disk-trafik. Dessa begränsningar med inte cacheträffar och nätverkstrafik. En separat bandbredd är tillgänglig för VM-nätverkstrafik. Bandbredd för nätverkstrafik som skiljer sig från dedikerade bandbredden som används av premiumdiskar för lagring.

    Den senaste informationen om högsta IOPS och genomströmning (bandbredd) för Premium-lagring stöds virtuella datorer, se [Windows VM-storlekar](../articles/virtual-machines/windows/sizes.md) eller [Linux VM-storlekar](../articles/virtual-machines/linux/sizes.md).

    Se tabellen i nästa avsnitt för mer information om premium storage diskar och deras IOPS och genomströmning gränser.

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda
I det här avsnittet beskriver vi skalbarhets- och prestandamål att tänka på när du använder Premiumlagring.

Premium-lagringskonton har följande skalbarhetsmål:

| Total kapacitet | Totala bandbredden för ett lokalt redundant lagringskonto |
| --- | --- | 
| Disken kapacitet: 35 TB <br>Ögonblicksbild kapacitet: 10 TB | Upp till 50 Gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> alla data (antal begäranden) som skickas till ett lagringskonto

<sup>2</sup> alla data (svar) som tas emot från ett lagringskonto

Mer information finns i [skalbarhets- och prestandamål för Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Om du använder premium storage-konton för ohanterade diskar och programmet överskrider skalbarhetsmål för ett enda lagringskonto, kanske du vill migrera till hanterade diskar. Om du inte vill migrera till hanterade diskar bygga programmet ska använda flera lagringskonton. Sedan partitionera data mellan dessa lagringskonton. Till exempel om du vill bifoga diskar av 51 TB över flera virtuella datorer sprida dem mellan två lagringskonton. 35 TB är gränsen för ett enda premiumlagringskonto. Se till att ett enda premiumlagringskonto aldrig har mer än 35 TB etablerade diskar.

### <a name="premium-storage-disk-limits"></a>Premium Lagringsgränser disk
När du etablerar en disk för premium-lagring anger storleken på disken högsta IOPS och genomströmning (bandbredd). Azure erbjuder sju typer av lagring premiumdiskar: P4 (hanterade diskar endast), P6 (hanterade diskar endast), P10, P20, P30, P40 och p 50. Varje premium storage disk har specifika gränser för IOPS och dataflöde. Begränsningar för disktyper beskrivs i följande tabell:

| Premium diskar typ  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB| 512 GB            | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100   | 2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund | 

> [!NOTE]
> Kontrollera att tillräckligt med bandbredd som är tillgänglig på den virtuella datorn på enheten disk trafik, enligt beskrivningen i [Premium-lagring stöds VMs](#premium-storage-supported-vms). Annars är dina diskgenomflödet och IOPS begränsad till lägre värden. Maximalt dataflöde och IOPS baseras på VM-gränser, inte på de disk begränsningar som beskrivs i tabellen ovan.  
> 
> 

Här följer några viktiga saker du behöver veta om skalbarhets- och prestandamål för Premium-lagring:

* **Etablerad kapacitet och prestanda**

    När du etablerar en premium storage-disk, till skillnad från vanlig lagring är du garanterat kapacitet, IOPS och dataflöde på disken. Om du skapar en p 50 disk, etablerar Azure exempelvis 4,095 GB lagringskapacitet, 7 500 IOPS och 250 MBIT/s genomströmning för disken. Programmet kan använda hela eller delar av kapacitet och prestanda.

* **Diskens storlek**

    Azure mappar diskens storlek (avrunda uppåt) till det närmaste premium disk lagringsalternativet, som anges i tabellen i föregående avsnitt. Till exempel klassificeras storleken för en disk på 100 GB som ett alternativ för P10. Den kan utföra upp till 500 IOPS, med upp till 100 MBIT/s genomströmning. På samma sätt kan en disk som klassificeras som en P20 400 GB. Den kan utföra upp till 2 300 IOPS, med 150 MBIT/s genomströmning.
    
    > [!NOTE]
    > Du kan enkelt öka storleken på befintliga diskar. Du kanske vill öka storleken på en disk på 30 GB 128 GB och till och 1 TB. Eller så kanske du vill konvertera din P20 disk till en P30 eftersom du behöver mer kapacitet eller fler IOPS och genomflöde. 
    > 
 
* **I/o-storlek**

    Storleken på ett i/o är 256 KB. Om data som överförs är mindre än 256 KB, anses 1 i/o-enhet. Större i/o-storlekar räknas som flera I/o storlek 256 KB. Till exempel räknas 1 100 KB i/o som 5 i/o-enheter.

* **Dataflöde**

    Gräns för genomflöde innehåller skrivningar till disken och innehåller disk läsåtgärder som inte hämtas från cachen. Till exempel har en disk P10 100 MBIT/s genomströmning per disk. I följande tabell visas några exempel på giltiga genomströmning för en P10 disk:

    | Max genomströmning per P10 disk | Icke-cache läser från disk | Icke-cache skrivning till disk |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Träffar i cache**

    Cacheträffar begränsas inte av allokerade IOPS eller dataflöde på disken. Till exempel när du använder en datadisk med en **ReadOnly** cache-inställningen på en virtuell dator som stöds av Premium-lagring, läsningar som hämtas från cachen omfattas inte IOPS och genomströmning caps på disken. Om arbetsbelastningen på en disk är huvudsakligen läser du kan hämta mycket hög genomströmning. Cachen lyder separat IOPS och genomströmning gränser på den virtuella datorn nivåer, baserat på VM-storlek. DS-serien virtuella datorer har ungefär 4 000 IOPS och 33 MBIT/s genomströmning per kärna för cache och lokala SSD I/o. GS-serien virtuella datorer har en gräns på 5 000 IOPS och 50 MBIT/s genomströmning per kärna för cache och lokala SSD I/o. 

## <a name="throttling"></a>Begränsning
Begränsning kan inträffa om programmet IOPS eller genomströmning överskrider de allokerade gränserna för en disk för premium-lagring. Begränsning också uppstå om din totala disk-trafik över alla diskar på den virtuella datorn är längre än disk bandbredd tillgänglig för den virtuella datorn. För att undvika begränsning, rekommenderar vi att du begränsar antalet väntande i/o-begäranden för disken. Använd en begränsning baserat på mål för skalbarhet och prestanda för disken du har etablerat och på den diskbandbredden som är tillgänglig för den virtuella datorn.  

Programmet kan få den lägsta fördröjningen när den är utformad för att undvika begränsning. Men om antalet väntande i/o-begäranden för disken är för liten, programmet kan inte utnyttja högsta IOPS och dataflödesnivåer som är tillgängliga på disken.

Följande exempel visar hur du beräknar bandbreddsbegränsning nivåer. Alla beräkningar baseras på ett i/o-storlek på 256 KB.

### <a name="example-1"></a>Exempel 1
Programmet har bearbetat 495 i/o-enheter med 16 KB storlek i en sekund på en P10 disk. I/o-enheter räknas som 495 IOPS. Om du försöker en 2 MB-i/o i samma andra är summan av i/o-enheter lika med 495 + 8 IOPS. Det beror på 2 MB i/o = 2 048 KB / 256 KB = 8 i/o-enheter, när storlek på i/o är 256 KB. Eftersom summan av 495 + 8 är längre än 500 IOPS för disken, inträffar begränsning.

### <a name="example-2"></a>Exempel 2
Programmet har bearbetat 400 i/o-enheter med 256 KB storleken på en P10 disk. Den totala bandbredden som används är (400 &#215; 256) / 1 024 KB = 100 MB/s. En P10 disk är begränsad till 100 MB/s genomströmning. Om programmet försöker utföra flera i/o-åtgärder i den andra begränsas eftersom den överskrider gränsen som är allokerade.

### <a name="example-3"></a>Exempel 3
Du har en virtuell dator DS4 med två P30 diskar som är anslutna. Varje P30 disk kan 200 MBIT/s genomströmning. En virtuell dator DS4 har dock en totala diskkapaciteten bandbredd på 256 MB/s. Du kan inte enheten båda anslutna diskar till maximalt dataflöde på den här virtuella datorn DS4 på samma gång. Du kan klara trafik över 200 MB/s på en disk och 56 MB/s på den andra disken för att lösa problemet. Om summan av disk-trafik färdas över 256 MB/s, begränsas disk trafik.

> [!NOTE]
> Om disken trafiken består främst av små i/o-storlekar, kommer ditt program som sannolikt nått gränsen för IOPS innan gränsen genomflöde. Men om disk-trafiken oftast består av stora i/o-storlek, kommer ditt program som sannolikt nådde genomströmning gränsen först i stället för IOPS-gräns. Du kan utnyttja programmets IOPS och genomströmningskapaciteten med optimala i/o-storlek. Du kan också begränsa antalet väntande i/o-begäranden för en disk.
> 

Läs mer om hur man designar för hög prestanda genom att använda Premium-lagring i [Design för prestanda med Premium-lagring](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Ögonblicksbilder och kopiera Blob

Till tjänsten Storage är VHD-filen en sidblobb. Du kan ta ögonblicksbilder av sidblobar och kopiera dem till en annan plats, som till ett annat lagringskonto.

### <a name="unmanaged-disks"></a>Ohanterade diskar

Skapa [inkrementell ögonblicksbilder](../articles/virtual-machines/linux/incremental-snapshots.md) för ohanterade premium diskar på samma sätt som du använder ögonblicksbilder med standardlagring. Premium-lagring stöder endast lokalt redundant lagring som replikeringsalternativet. Vi rekommenderar att du skapar ögonblicksbilder och sedan kopiera ögonblicksbilder till ett geo-redundant standardlagringskonto. Mer information finns i [Azure redundans lagringsalternativ](../articles/storage/common/storage-redundancy.md).

Om en disk ansluten till en virtuell dator kan är vissa API-åtgärder på disken inte tillåtna. Du kan exempelvis utföra en [kopiera Blob](/rest/api/storageservices/Copy-Blob) åtgärden på den blobben om disken är kopplad till en virtuell dator. I stället först skapa en ögonblicksbild av blobben med hjälp av den [ögonblicksbild Blob](/rest/api/storageservices/Snapshot-Blob) REST API. Därefter utför den [kopiera Blob](/rest/api/storageservices/Copy-Blob) av ögonblicksbilden kopiera ansluten disk. Alternativt kan du koppla bort disken och utföra alla nödvändiga åtgärder.

Följande begränsningar gäller för premium storage blob ögonblicksbilder:

| Lagringsgränsen för Premium | Värde |
| --- | --- |
| Maximalt antal ögonblicksbilder per blob | 100 |
| Kapacitet för lagringskonton för ögonblicksbilder<br>(Innehåller data endast ögonblicksbilder. Inkluderar inte data i grundläggande blob.) | 10 TB |
| Minimitid mellan på varandra följande ögonblicksbilder | 10 minuter |

Om du vill behålla geo-redundant kopior av dina ögonblicksbilder, kan du kopiera ögonblicksbilder från ett premiumlagringskonto till ett geo-redundant standardlagringskonto med hjälp av AzCopy eller kopiera Blob. Mer information finns i [överföra data med kommandoradsverktyget azcopy](../articles/storage/common/storage-use-azcopy.md) och [kopiera Blob](/rest/api/storageservices/Copy-Blob).

Detaljerad information om hur du gör REST-åtgärder mot sidblobbar i ett premiumlagringskonto finns [Blob tjänståtgärder med Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Hanterade diskar

En ögonblicksbild för hanterade diskar är en skrivskyddad kopia av den hantera disken. Ögonblicksbilden lagras som standard hanterade disk. För närvarande [inkrementell ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) stöds inte för hanterade diskar. Information om hur du tar en ögonblicksbild för hanterade diskar finns [skapar en kopia av en virtuell Hårddisk lagras som en Azure-hanterad disk med hjälp av hanterade ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) eller [skapar en kopia av en virtuell Hårddisk lagras som en Azure-hanterad disk med hjälp av hanterade ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Om hanterade disken kopplas till en virtuell dator, tillåts inte vissa API-åtgärder på disken. Du kan till exempel generera en signatur för delad åtkomst (SAS) för att utföra en kopieringsåtgärd medan disken som är kopplad till en virtuell dator. I stället först skapa en ögonblicksbild av disken och kopiera ögonblicksbilden. Du kan också koppla bort disken och sedan generera en SAS för att utföra kopieringen.


## <a name="premium-storage-for-linux-vms"></a>Premium-lagring för virtuella Linux-datorer
Du kan använda följande information som hjälper dig att konfigurera din virtuella Linux-datorer i Premium-lagring:

Att uppnå skalbarhetsmål i Premium-lagring för alla premium storage diskar med cache värdet **ReadOnly** eller **ingen**, måste du inaktivera ”barriärer” när du monterar filsystemet. Du behöver inte barriärer i det här scenariot eftersom skrivningar till premium-lagringsdiskar är beständig för dessa inställningar för cachelagring. När write-förfrågan har slutförts korrekt har data skrivits till det beständiga arkivet. Använd någon av följande metoder om du vill inaktivera ”barriärer”. Välj en för filsystemet:
  
* För **reiserFS**, för att inaktivera barriärer använder den `barrier=none` montera alternativet. (Använd för att aktivera barriärer `barrier=flush`.)
* För **ext3/ext4**, för att inaktivera barriärer använder den `barrier=0` montera alternativet. (Använd för att aktivera barriärer `barrier=1`.)
* För **XFS**, för att inaktivera barriärer använder den `nobarrier` montera alternativet. (Använd för att aktivera barriärer `barrier`.)
* Premium-lagring diskar med cache anges till **ReadWrite**, aktivera hinder för hållbarhet för skrivning.
* Volymen etiketter ska finnas kvar när du startar om den virtuella datorn, måste du uppdatera /etc/fstab med universellt Unik identifierare (UUID) referenser till diskarna. Mer information finns i [lägga till en hanterad disk till en Linux-VM](../articles/virtual-machines/linux/add-disk.md).

Följande Linux-distributioner har validerats för Azure Premium-lagring. För bättre prestanda och stabilitet med Premium-lagring rekommenderar vi att du uppgraderar dina virtuella datorer till någon av dessa versioner minst (eller en senare version). Vissa av versionerna som kräver den senaste Linux Integration Services (LIS), v4.0, för Azure. Om du vill hämta och installera en distributionsplats på länken visas i följande tabell. Vi lägger till bilder i listan när vi slutför verifieringen. Observera att vår verifieringar visar att prestandan varierar för varje bild. Prestanda beror på arbetsbelastningen egenskaper och inställningar för avbildningen. Olika bilder justerade för olika typer av arbetsbelastningar.

| Distribution | Version | Stöds kernel | Information |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| SUSE sles-12-prioritet-v20150213 <br> SUSE-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | Virtuell CoreOS 584.0.0 |
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

Starta om datorn för att aktivera de nya drivrutinerna.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Premiumlagring, gäller fakturering följande:

* **Premium-lagring disk och blob-storlek**

    Fakturering för en disk för premium-lagring eller blob beror på den etablerade storleken på disken eller blob. Azure mappar etablerade storleken (avrunda uppåt) till det närmaste premium disk lagringsalternativet. Mer information finns i tabellen i [Premium-lagring skalbarhets- och prestandamål](#premium-storage-scalability-and-performance-targets). Varje disk mappar till en stöds etablerad diskstorlek och därefter faktureras. Fakturering för etablerade diskar är linjärt varje timme med månatliga priset för erbjudandet Premium-lagring. Till exempel om du etableras en P10 disken och tog bort den efter 20 timmar, debiteras du för P10 erbjudandet linjärt till 20 timmar. Detta är oavsett hur mycket faktiska data som skrivs till disken eller IOPS och genomströmning används.

* **Premium ohanterade diskar ögonblicksbilder**

    Ögonblicksbilder på ohanterade premiumdiskar debiteras för ytterligare kapacitet som används av ögonblicksbilder. Mer information om ögonblicksbilder finns [skapa en ögonblicksbild av en blob](/rest/api/storageservices/Snapshot-Blob).

* **Premium hanterade diskar ögonblicksbilder**

    En ögonblicksbild av en hanterad disk är en skrivskyddad kopia av disken. Disken lagras som standard hanterade disk. En ögonblicksbild kostnader samma som en standard hanterad disk. Till exempel om du skapar en ögonblicksbild av en hanterad premium 128 GB disk, motsvarar kostnaden för ögonblicksbilden en 128 GB standard hanterade diskar.  

* **Utgående dataöverföring**

    [Utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data skickas från Azure-datacenter) debiteras för bandbreddsanvändning.

Detaljerad information om priser för Premium-lagring, Premium-lagring stöds virtuella datorer och hanterade diskar finns i följande artiklar:

* [Prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Virtuella datorer priser](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Azure Backup-support 

För regional katastrofåterställning, du måste säkerhetskopiera Virtuella diskar i en annan region med hjälp av [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) och ett GRS-lagringskonto som ett säkerhetskopieringsvalv.

Om du vill skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopieringar Använd enkelt VM-återställning och principer för lagring av säkerhetskopior Azure Backup. Du kan använda säkerhetskopiering båda med ohanterade och hanterade diskar. Mer information finns i [Azure Backup för virtuella datorer med ohanterad diskar](../articles/backup/backup-azure-vms-first-look-arm.md) och [Azure Backup för virtuella datorer med hanterade diskar](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Nästa steg
Mer information om Premium-lagring finns i följande artiklar.
