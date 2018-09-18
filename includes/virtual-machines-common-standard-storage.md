---
title: ta med fil
description: ta med fil
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ab085d6a5cb38c46cf46a51da6d294732e2fd879
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45978978"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Standard Storage med kostnadseffektiv lagring och ohanterade och hanterade Virtuella Azure-diskar

Azure Standard Storage tillhandahåller tillförlitlig, billig disksupport för virtuella datorer inte arbetsbelastningar som körs. Den stöder även blobar, tabeller, köer och filer. Med Standard-lagring lagras data på hårddiskar (HDD). När du arbetar med virtuella datorer, kan du använda standard SSD och HDD-diskar för utveckling och testning och mindre kritiska arbetsbelastningar och premium SSD-diskar för verksamhetskritiska produktionsprogram. Standard-lagring är tillgängligt i alla Azure-regioner. 

Den här artikeln handlar om användning av standard SSD och HDD-diskar. Mer information om hur du använder lagring med blobbar, tabeller, köer och filer finns i [introduktion till Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Disktyper

Det finns två sätt att skapa standarddiskar för virtuella Azure-datorer:

**Ohanterade diskar**: den här typen av disken är den ursprungliga metoden där du hanterar de lagringskonton som används för att lagra VHD-filerna som motsvarar VM-diskarna. VHD-filer som lagras som sidblobar i lagringskonton. Ohanterade diskar kan kopplas till valfri Azure VM-storlek, inklusive de virtuella datorer som främst använder Premium Storage kan till exempel DSv2 och GS-serien. Virtuella Azure-datorer stöd för att koppla flera standarddiskar, vilket gör att upp till 256 TB lagringsutrymme per virtuell dator.

[**Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): den här funktionen hanterar de lagringskonton som används för VM-diskarna åt dig. Du kan ange typ (Premium SSD-, Standard SSD- och Standard HDD) och storlek på disk och Azure skapar och hanterar disken åt dig. Du inte behöver bekymra dig om genom att diskarna placeras på flera lagringskonton för att säkerställa att du håller dig inom skalbarhetsgränserna för storage-konton – Azure hanterar det åt dig.

Även om båda typer av diskar är tillgängliga, bör du använda Managed Disks för att dra nytta av deras många funktioner.

Om du vill komma igång med Azure standardlagring, besök [Kom igång kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/). 

Information om hur du skapar en virtuell dator med Managed Disks finns i någon av följande artiklar.

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standardlagring funktioner 

Låt oss ta en titt på några av funktionerna i Standard-lagring. Mer information finns i [introduktion till Azure Storage](../articles/storage/common/storage-introduction.md).

**Standardlagring**: Azure standardlagring har stöd för Azure Disks, Azure Blobs, Azure Files, Azure-tabeller och Azure-köer. För att använda Standard Storage-tjänster måste börja med [skapa ett Azure Storage-konto](../articles/storage/common/storage-quickstart-create-account.md).

**Standard SSD-diskar:** Standard SSD-diskar ger mer tillförlitlig prestanda än Standard HDD-diskar och är tillgängligt i förhandsversionen. Mer information om regiontillgänglighet för Standard SSD-diskar finns i [regiontillgänglighet för Standard SSD-diskar (förhandsversion)](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**HDD-standarddiskar:** Standard HDD-diskar kan kopplas till alla Azure virtuella datorer, inklusive storlek-seriens virtuella datorer som används med Premium Storage, till exempel DSv2 och GS-serien. En Standard HDD-disk kan bara kopplas till en virtuell dator. Men kan du koppla en eller flera av dessa diskar till en virtuell dator, upp till maximalt Diskantalet som definierats för den VM-storleken. I följande avsnitt på Standard Storage skalbarhets- och prestandamål beskriver vi specifikationerna i detalj.

**Standard sidblob**: Standard-sidblobar används för att lagra beständiga diskar för virtuella datorer och kan även nås direkt via REST precis som andra typer av Azure-Blobar. [Sidblob-objekt](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) är en samling 512 byte-sidor som är optimerade för icke-sekventiella Läs- och skrivåtgärder. 

**Storage-replikering:** i de flesta regionerna data i ett standardlagringskonto kan vara lokalt replikerade eller geo-replikerade över flera datacenter. Fyra typer av replikering som är tillgängliga är lokalt Redundant lagring (LRS), Zonredundant lagring (ZRS), Geo-Redundant lagring (GRS) och Read Access Geo-Redundant lagring (RA-GRS). Hanterade diskar i standardlagring stöd för närvarande för lokalt Redundant lagring (LRS) endast. Mer information finns i [Lagringsreplikering](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda

I det här avsnittet beskrivs de mål för skalbarhet och prestanda som du behöver tänka på när du använder standard-lagring.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Lagringskonton – gäller inte för hanterade diskar

| **Resurs** | **Standardgräns** |
|--------------|-------------------|
| TB per lagringskonto  | 500 TB |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (oss regioner) | 10 Gbit/s, om aktiverad GRS/ZRS, 20 Gbit/s för LRS |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (oss regioner) | 20 Gbit/s, om aktiverad RA-GRS/GRS/ZRS, 30 Gbit/s för LRS |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (Europeiska och asiatiska regioner) | 5 Gbit/s, om aktiverad GRS/ZRS, 10 Gbit/s för LRS |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (Europeiska och asiatiska regioner) | 10 Gbit/s, om aktiverad RA-GRS/GRS/ZRS, 15 Gbit/s för LRS |
| Begär frekvens (förutsatt 1 KB Objektstorlek) per lagringskonto | Upp till 20 000 IOPS, enheter per sekund eller meddelanden per sekund |

<sup>1</sup> ingående syftar på alla data (förfrågningar) som skickas till ett lagringskonto. Utgående data syftar på alla data (svar) som tas emot från ett lagringskonto.

Mer information finns i [skalbarhet för lagring av Azure- och prestandamål](../articles/storage/common/storage-scalability-targets.md).

Om programmets behov överstiger det för skalbarhetsmål för ett enda lagringskonto, skapa dina program kan använda flera lagringskonton och partitionera dina data på dessa lagringskonton. Alternativt kan du kan Azure Managed Disks och Azure hanterar partitionering och placering av dina data åt dig.

### <a name="standard-disks-limits"></a>Standarddiskar gränser

Till skillnad från Premiumdiskar, indata/utdataåtgärder per sekund (IOPS) och dataflöde (bandbredd) på standarddiskar inte har etablerats. Prestanda för standarddiskar varierar beroende på virtuella datorstorlek till att disken är ansluten, inte till storleken på disken. Du kan förvänta dig att uppnå upp till den prestanda gränsen som anges i tabellen nedan.

**Standarddiskar gränser (hanterade och ohanterade)**

| **Nivå för virtuell dator**            | **Basic-nivån VM** | **Virtuell dator på standardnivå** |
|------------------------|-------------------|----------------------|
| Max diskens storlek          | 4 095 GB           | 4 095 GB              |
| Maximalt antal 8 KB IOPS per disk | Upp till 300         | Upp till 500            |
| Max Bandwidth per disk | Upp till 60 MB/s     | Upp till 60 MB/s        |

Om arbetsbelastningen kräver stöd för diskar med höga prestanda och låg latens, bör du överväga att använda Premium Storage. Om du vill veta mer fördelarna med Premium Storage finns [högpresterande Premium Storage och Azure VM-diskar](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Ögonblicksbilder och kopiering av blob

VHD-filen är en sidblobb till Storage-tjänsten. Du kan ta ögonblicksbilder av sidblobar och kopiera dem till en annan plats, till exempel ett annat lagringskonto.

### <a name="unmanaged-disks"></a>Ohanterade diskar

Du kan skapa [inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) för ohanterade standarddiskar på samma sätt som du kan använda ögonblicksbilder med standardlagring. Vi rekommenderar att du skapar ögonblicksbilder och sedan kopiera dessa ögonblicksbilder till ett geo-redundant standardlagringskonto om källdisken är i ett lokalt redundant lagring-konto. Mer information finns i [redundansalternativ för Azure Storage](../articles/storage/common/storage-redundancy.md).

Om en disken är ansluten till en virtuell dator, tillåts inte vissa API-åtgärder på diskarna. Exempel: du kan inte utföra en [kopiering av Blob](/rest/api/storageservices/Copy-Blob) åtgärden på den blobben som är så länge att disken är ansluten till en virtuell dator. I stället först skapa en ögonblicksbild av blobben med den [ta ögonblicksbild av Blob](/rest/api/storageservices/Snapshot-Blob) REST API-metoden och utför sedan den [kopiering av Blob](/rest/api/storageservices/Copy-Blob) ögonblicksbild att kopiera den anslutna disken. Du kan också koppla från disken och sedan utföra alla nödvändiga åtgärder.

Om du vill underhålla geo-redundanta kopior av dina ögonblicksbilder kan kopiera du ögonblicksbilder från ett lokalt redundant lagringskonto till ett geo-redundant standardlagringskonto med hjälp av AzCopy eller kopiering av Blob. Mer information finns i [överföra data med kommandoradsverktyget Azcopy](../articles/storage/common/storage-use-azcopy.md) och [kopiering av Blob](/rest/api/storageservices/Copy-Blob).

Detaljerad information om hur du utför REST-åtgärder mot sidblobar i standard storage-konton finns i [Azure Storage Services REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Hanterade diskar

En ögonblicksbild för en hanterad disk är en skrivskyddad kopia av den hantera disken som lagras som en hanterad disk som standard. Inkrementella ögonblicksbilder stöds inte för närvarande för Managed Disks, men kommer att stödjas i framtiden.

Om en hanterad disk är kopplat till en virtuell dator, tillåts inte vissa API-åtgärder på diskarna. Exempelvis kan du generera en signatur för delad åtkomst (SAS) för att utföra en kopieringsåtgärd när disken är ansluten till en virtuell dator. I stället först skapa en ögonblicksbild av disken och sedan utföra kopieringen av ögonblicksbilden. Du kan också koppla från disken och sedan generera en signatur för delad åtkomst (SAS) för att utföra kopieringen.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Standard-lagring, gäller följande för debitering:

* Standardlagring ohanterade diskar/datastorlek 
* Hanterade standarddiskar
* Standard storage-ögonblicksbilder
* Utgående dataöverföringar
* Transaktioner

**Ohanterade lagringsstorleken för data och disk:** för ohanterade diskar och andra data (blobs, tabeller, köer och filer), debiteras du bara den mängd utrymme som du använder. Till exempel om du har en virtuell dator vars sidblob har etablerats som 127 GB, men den virtuella datorn är egentligen bara använder 10 GB utrymme, debiteras du för 10 GB utrymme. Vi stöder upp till standardlagring 8191 GB och Standard ohanterade diskar upp till 4 095 GB. 

**Hanterade diskar:** faktureringen för hanterade standarddiskar beror på den etablerade storleken på disken. Azure maps-Allokerad storlek (avrundas uppåt) till närmaste Managed Disks-alternativ som anges i tabellerna nedan. Varje hanterad disk mappar till en av de etablerade storlekarna som stöds och debiteras därefter. Till exempel att om du skapar en hanterad disk som standard och ange en etablerad storlek på 200 GiB, kommer du att debiteras enligt priserna för S15 disktypen.

| **Standard HDD hanteras <br>disktyp** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------| 
| Diskstorlek        | 32 giB  | 64 giB  | 128 GiB | 256 GB | 512 GiB | 1 024 giB (1 TiB) | 2048 giB (2 TiB) | 4 095 giB (4 TiB) | 


**Ögonblicksbilder**: ögonblicksbilder av standarddiskar faktureras för ytterligare kapacitet som används av ögonblicksbilder. Information om ögonblicksbilder finns [skapa en ögonblicksbild av en Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Utgående dataöverföringar**: [utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data som går ut från Azures datacenter) debiteras för bandbreddsanvändning.

**Transaktionen**: Azure debiterar $0.0036 per 100 000 transaktioner för standard-lagring. Transaktioner omfattar både läs- och skrivåtgärder till lagringsutrymmet.

Detaljerad information om priser för Standard-lagring, virtuella datorer och hanterade diskar finns:

* [Priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Support för Azure Backup-tjänsten 

Virtuella datorer med ohanterade diskar kan säkerhetskopieras med Azure Backup. [Mer information om](../articles/backup/backup-azure-vms-first-look-arm.md).

Du kan också använda tjänsten Azure Backup med hanterade diskar för att skapa en säkerhetskopiering med tidsbaserade säkerhetskopieringar, enkelt VM-återställning och principer för lagring av säkerhetskopior. Du kan läsa mer om detta på [med hjälp av Azure Backup-tjänsten för virtuella datorer med Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Storage](../articles/storage/common/storage-introduction.md)

* [Skapa ett lagringskonto](../articles/storage/common/storage-quickstart-create-account.md)

* [Översikt över Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)
