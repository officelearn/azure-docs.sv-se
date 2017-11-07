# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Kostnadseffektiv standardlagring och ohanterade och hanterade Virtuella Azure-diskar

Azure standardlagring ger stöd för tillförlitlig, billig diskar för virtuella datorer som kör latens-okänslig arbetsbelastningar. Det stöder också blobbar, tabeller, köer och filer. Med standardlagring lagras data på hårddiskar (HDD). När du arbetar med virtuella datorer kan använda du standardlagring diskar för utveckling och testning scenarier och mindre viktiga arbetsbelastningar och premiumdiskar lagring för kritiska produktionsprogram. Standardlagring är tillgänglig i alla Azure-regioner. 

Den här artikeln fokuserar på att använda standardlagring för Virtuella diskar. Mer information om hur du använder lagring med blobbar, tabeller, köer och filer finns i den [introduktion till Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Disktyper

Det finns två sätt att skapa standarddiskar för virtuella Azure-datorer:

**Ohanterad diskar**: Detta är den ursprungliga metoden där du hanterar storage-konton som används för att lagra VHD-filer som motsvarar VM-diskarna. VHD-filer lagras som sidblobbar i storage-konton. Ohanterad diskar kan kopplas till en Azure VM-storlek, inklusive de virtuella datorer som i första hand använder Premiumlagring, till exempel DSv2 och GS-serien. Virtuella Azure-datorer stöder bifoga flera standarddiskar tillåter upp till 256 TB lagringsutrymme per virtuell dator.

[**Azure-hanterade diskar**](../articles/virtual-machines/windows/managed-disks-overview.md): funktionen hanterar storage-konton som används för de Virtuella diskarna för dig. Du anger typ (Premium eller Standard) och storlek på disk och Azure skapar och hanterar disken du. Du behöver inte bry dig om att placera diskarna över flera lagringskonton för att säkerställa att du håller dig inom skalbarhetsgränser lagringskontots--Azure hanterar som du.

Även om båda typer av diskar är tillgängliga, bör du använda hanterade diskar dra nytta av de många funktionerna.

Kom igång med Azure standardlagring, besök [Kom igång gratis](https://azure.microsoft.com/pricing/free-trial/). 

För information om hur du skapar en virtuell dator med hanterade diskar, se något av följande artiklar.

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standardlagring funktioner 

Låt oss ta en titt på några av funktionerna i standardlagring. Mer information, se [introduktion till Azure Storage](../articles/storage/common/storage-introduction.md).

**Standardlagring**: Azure standardlagring stöder Azure-diskar, Azure BLOB, Azure-filer, Azure-tabeller och köer i Azure. Om du vill använda standardlagring services måste börja med [skapa ett Azure Storage-konto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).

**Standard lagringsdiskar:** standardlagring diskar kan kopplas till alla virtuella datorer i Azure inklusive storlek-serien virtuella datorer som används med Premium-lagring, till exempel DSv2 och GS-serien. En standardlagring disk kan endast kopplas till en virtuell dator. Du kan dock koppla en eller flera av dessa diskar till en virtuell dator, upp till antalet maximal disk som definierats för denna VM-storlek. I följande avsnitt på Standard skalbarhets- och Storage prestandamål beskrivs specifikationer i detalj. 

**Standard sidblob**: Standard sidblobar används för att lagra beständiga diskar för virtuella datorer och kan också komma åt direkt via REST precis som andra typer av Azure-BLOB. [Sidblobbar](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) är en samling 512 byte-sidor som är optimerade för slumpmässiga Läs- och skrivåtgärder. 

**Storage-replikering:** i de flesta regioner data i ett standardlagringskonto kan vara lokalt replikerade eller georeplikerad i flera datacenter. Fyra typer av replikering är tillgängligt är lokalt Redundant lagring (LRS), Zonredundant lagring (ZRS), Geo-Redundant lagring (GRS) och Geo-Redundant lagring med läsbehörighet (RA-GRS). Hanterade diskar i standardlagring stöder för närvarande lokalt Redundant lagring (LRS) bara. Mer information finns [Lagringsreplikering](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda

I det här avsnittet beskrivs de mål för skalbarhet och prestanda som du behöver tänka på när du använder standardlagring.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Lagringskontogränser – gäller inte för hanterade diskar

| **Resurs** | **Standardgräns** |
|--------------|-------------------|
| TB per lagringskonto  | 500 TB |
| Max ingång<sup>1</sup> per lagringskonto (oss regioner) | 10 Gbit/s om GRS/ZRS aktiverad 20 Gbit/s för LRS |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (oss regioner) | 20 Gbit/s om RA-GRS/GRS/ZRS aktiverad 30 Gbit/s för LRS |
| Max ingång<sup>1</sup> per lagringskonto (Europeiska och östasiatiska regioner) | 5 Gbit/s om GRS/ZRS aktiverad, 10 Gbit/s för LRS |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (Europeiska och östasiatiska regioner) | 10 Gbit/s om RA-GRS/GRS/ZRS aktiverad 15 Gbit/s för LRS |
| Begära frekvens (förutsatt att 1 KB Objektstorlek) per lagringskonto | Upp till 20 000 IOPS, enheter per sekund eller meddelanden per sekund |

<sup>1</sup> ingång refererar till alla data (antal begäranden) som skickas till ett lagringskonto. Utgående avser alla data (svar) tas emot från ett lagringskonto.

Mer information finns i [Azure Storage skalbarhets- och prestandamål](../articles/storage/common/storage-scalability-targets.md).

Om behov av programmet överskrider skalbarhetsmål för ett enda lagringskonto, skapa ditt program att använda flera lagringskonton och partitionera data mellan dessa lagringskonton. Alternativt kan du kan Azure hanterade diskar och Azure hanterar partitionering och placering av dina data för dig.

### <a name="standard-disks-limits"></a>Standarddiskar gränser

Till skillnad från Premiumdiskar etableras i/o-åtgärder per sekund (IOPS) och dataflöde (bandbredd) standarddiskar inte. Prestanda för standarddiskar varierar beroende på VM-storlek på disken är ansluten, inte storleken på disken. Du kan förvänta dig att få upp till prestanda gränsen som anges i tabellen nedan.

**Standarddiskar gränser (hanterade och ohanterade)**

| **VM-nivå**            | **Grundnivån VM** | **Standardnivån VM** |
|------------------------|-------------------|----------------------|
| Max diskens storlek          | 4095 GB           | 4095 GB              |
| Maximalt antal 8 KB IOPS per disk | Upp till 300         | Upp till 500            |
| Max Bandwidth per disk | Upp till 60 MB/s     | Upp till 60 MB/s        |

Om din arbetsbelastning kräver stöd för diskar med hög prestanda, låg latens, bör du använda Premium-lagring. Om du vill veta mer fördelarna med Premium-lagring finns [lagring med höga prestanda Premium och Azure VM diskar](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Ögonblicksbilder och kopiera blob

Till tjänsten Storage är VHD-filen en sidblobb. Du kan ta ögonblicksbilder av sidblobar och kopiera dem till en annan plats, till exempel ett annat lagringskonto.

### <a name="unmanaged-disks"></a>Ohanterade diskar

Du kan skapa [inkrementell ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) för ohanterade standarddiskar på samma sätt som du kan använda ögonblicksbilder med standardlagring. Vi rekommenderar att du skapar ögonblicksbilderna och kopierar sedan dessa ögonblicksbilder till ett geo-redundant standardlagringskonto om källdisken är i ett lokalt redundant lagringskonto. Mer information finns i [Azure lagringsalternativ för redundans](../articles/storage/common/storage-redundancy.md).

Om en disk ansluten till en virtuell dator ska tillåts inte vissa API-åtgärder på diskarna. Du kan exempelvis utföra en [kopiera Blob](/rest/api/storageservices/Copy-Blob) åtgärden på blobben så länge disken som är kopplad till en virtuell dator. I stället först skapa en ögonblicksbild av blobben med hjälp av den [ögonblicksbild Blob](/rest/api/storageservices/Snapshot-Blob) REST API-metoden och utför sedan den [kopiera Blob](/rest/api/storageservices/Copy-Blob) av ögonblicksbilden kopiera ansluten disk. Alternativt kan du koppla bort disken och utföra alla nödvändiga åtgärder.

Om du vill behålla geo-redundant kopior av dina ögonblicksbilder, kan du kopiera ögonblicksbilder från ett lokalt redundant lagringskonto till ett geo-redundant standardlagringskonto med hjälp av AzCopy eller kopiera Blob. Mer information finns i [överföra data med kommandoradsverktyget Azcopy](../articles/storage/common/storage-use-azcopy.md) och [kopiera Blob](/rest/api/storageservices/Copy-Blob).

Detaljerad information om hur du utför REST-åtgärder mot sidblobbar i standard storage-konton finns [Azure Storage Services REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Hanterade diskar

En ögonblicksbild för hanterade diskar är en skrivskyddad kopia av den hantera disken som lagras som en standard hanterade diskar. Inkrementell ögonblicksbilder stöds inte för närvarande för hanterade diskar, men kommer att stödjas framöver.

Om hanterade disken kopplas till en virtuell dator, tillåts inte vissa API-åtgärder på diskarna. Du kan till exempel generera en signatur för delad åtkomst (SAS) för att utföra en kopieringsåtgärd medan disken som är kopplad till en virtuell dator. I stället först skapa en ögonblicksbild av disken och kopiera ögonblicksbilden. Du kan också koppla bort disken och generera en signatur för delad åtkomst (SAS) för att utföra kopieringen.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder standardlagring gäller fakturering följande:

* Standardlagring ohanterad diskar/datastorlek 
* Hanterade standarddiskar
* Standardlagring ögonblicksbilder
* Utgående dataöverföringar
* Transaktioner

**Ohanterad lagringsstorleken för data och disk:** för ohanterade diskar och andra data (blobbar, tabeller, köer och -filer), debiteras du bara den mängd utrymme som du använder. Till exempel om du har en virtuell dator vars sidblob har etablerats som 127 GB, men den virtuella datorn verkligen är du bara använder 10 GB utrymme du debiteras för 10 GB ledigt utrymme. Vi stöder standardlagring upp till 8191 GB och Standard ohanterad diskar upp till 4095 GB. 

**Hanterade diskar:** hanterade diskar debiteras för den allokerade storleken. Om disken har etablerats som en disk på 10 GB och du bara använder 5 GB, debiteras du etablera storleken på 10 GB.

**Ögonblicksbilder**: ögonblicksbilder av standarddiskar debiteras för ytterligare kapacitet som används av ögonblicksbilder. Mer information om ögonblicksbilder finns [skapa en ögonblicksbild av en Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Utgående dataöverföringar**: [utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data skickas från Azure-datacenter) debiteras för bandbreddsanvändning.

**Transaktionen**: Azure debiterar $0.0036 per 100 000 transaktioner för standardlagring. Transaktioner omfattar både läs- och skrivåtgärder till lagringsutrymmet.

Detaljerad information om priser för standardlagring för finns virtuella datorer och hanterade diskar:

* [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/)
* [Virtuella datorer priser](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Stöd för Azure Backup service 

Virtuella datorer med ohanterad diskar kan säkerhetskopieras med Azure Backup. [Mer information](../articles/backup/backup-azure-vms-first-look-arm.md).

Du kan också använda tjänsten Azure Backup med hanterade diskar för att skapa en säkerhetskopiering med tidsbaserade säkerhetskopieringar, enkelt VM-återställning och principer för lagring av säkerhetskopior. Du kan läsa mer om detta i [med hjälp av Azure Backup-tjänsten för virtuella datorer med hanterade diskar](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Storage](../articles/storage/common/storage-introduction.md)

* [Skapa ett lagringskonto](../articles/storage/common/storage-create-storage-account.md)

* [Översikt över Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)
