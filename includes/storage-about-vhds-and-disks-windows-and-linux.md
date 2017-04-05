
## <a name="about-vhds"></a>Om virtuella hårddiskar

De virtuella hårddiskarna i Azure är .vhd-filer som lagras som sidblobar i standard- eller premium-lagringskonton i Azure. Mer information om sidblobar finns [Understanding block blobs and page blobs](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/) (Förstå blockblobar och sidblobar). Mer information om premium-lagring finns i [High-performance premium storage and Azure VMs](../articles/storage/storage-premium-storage.md) (Premium-lagring och virtuella Azure-datorer med hög prestanda).

Azure stöder det fasta formatet för virtuella hårddiskar (VHD). Det fasta format lägger ut den logiska disken linjärt i filen så att diskförskjutning X lagras vid blob-förskjutning X. En liten sidfot i slutet av bloben beskriver den virtuella hårddiskens egenskaper. Det fasta formatet slösar ofta med utrymme eftersom de flesta diskar har stora utrymmen som är oanvända. Men Azure lagrar .vhd-filer i ett begränsat format så att du kan dra nytta av fördelarna med både fasta och dynamiska diskar på samma gång. Mer information finns i [Komma igång med virtuella hårddiskar](https://technet.microsoft.com/library/dd979539.aspx).

Alla .vhd-filer i Azure som du vill använda som källa för att skapa diskar eller avbildningar är skrivskyddade. När du skapar en disk eller avbildning gör Azure kopior av .vhd-filerna. Dessa kopior kan vara skrivskyddade eller läs och skriv, beroende på hur du använder den virtuella hårddisken.

När du skapar en virtuell dator från en avbildning skapar Azure en disk för den virtuella datorn som är en kopia av .vhd-filen som används som källa. För att skydda dig mot att du råkar radera dem av misstag placerar Azure en livslängd på alla .vhd-filer som används som källor för att skapa en avbildning, en operativsystemdisk eller en datadisk.

Innan du kan ta bort en .vhd-fil som används som källa måste du ta bort livslängden genom att ta bort disken eller avbildningen. Om du vill ta bort en .vhd-fil som används av en virtuell dator som en operativsystemdisk kan du ta bort den virtuella datorn, operativsystemdisken, och .vhd-filen som används som källa på en gång genom att ta bort den virtuella datorn och alla associerade diskar. Men det krävs att du genomför ett antal steg i en viss ordning för att ta bort en .vhd-fil som är en källa för en datadisk. Först kopplar du bort disken från den virtuella datorn, sedan tar du bort disken och sist tar du bort .vhd-filen.

> [!WARNING]
> Om du tar bort en .vhd-fil som används som källa från lagringen eller tar bort ditt lagringskonto kan Microsoft inte återställa dessa data åt dig.
> 

## <a name="types-of-disks"></a>Typer av diskar 

Det finns två prestandanivåer för lagring som du kan välja när du skapar diskar – Standard Storage och Premium Storage. Dessutom finns två typer av diskar, ohanterade och hanterade, som kan användas på båda prestandanivåerna.  

### <a name="standard-storage"></a>Standard Storage 

Standard Storage stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standard Storage kan replikeras lokalt i ett datacenter eller vara geo-redundant med primära och sekundära datacenter. Mer information om lagringsreplikeringsalternativ finns i [Azure Storage-replikering](../articles/storage/storage-redundancy.md). 

Mer information om hur du använder Standard Storage med VM-diskar finns i [Standard Storage and Disks](../articles/storage/storage-standard-storage.md) (Standard Storage och diskar).

### <a name="premium-storage"></a>Premium Storage 

Premium Storage stöds av solid state-hårddiskar och levererar högpresterande disksupport med låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Du kan använda Premium Storage med DS, DSv2, GS eller FS-seriens virtuella Azure-datorer. Mer information finns i [Premium Storage](../articles/storage/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Ohanterade diskar

Ohanterade diskar är den traditionella typen av diskar som används av virtuella datorer. Med dem kan du skapa ett eget lagringskonto och ange det lagringskontot när du skapar disken. Du måste se till att du inte lägger till för många diskar i samma lagringskonto eftersom du kan överskrida lagringskontots [skalbarhetsmål](../articles/storage/storage-scalability-targets.md) (20 000 IOPS, till exempel), vilket resulterar i att de virtuella datorerna begränsas. Du måste lista ut hur du optimerar användningen av en eller flera lagringskonton för att få ut den bästa prestandan av dina virtuella datorer med ohanterade diskar.

### <a name="managed-disks"></a>Hanterade diskar 

Managed Disks hanterar skapandet/hanterandet av lagringskontot i bakgrunden och säkerställer att du inte behöver bekymra dig om lagringskontots skalbarhetsgränser. Du anger bara diskens storlek och prestandanivå (Standard/Premium) så skapar och hanterar Azure disken åt dig. Även om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används. 

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [Översikt över Azure Managed Disks](../articles/storage/storage-managed-disks-overview.md).

Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar för att dra nytta av de många funktionerna som finns i Managed Disks.

### <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av Premium och Standard för både ohanterade och hanterade diskar, för att hjälpa dig att avgöra vad som passar dig bäst.

|    | Azure Premium-disk | Azure Standard-disk |
|--- | ------------------ | ------------------- |
| Disktyp | Solid State-hårddiskar (SSD) | Hårddiskar (HDD)  |
| Översikt  | SSD-baserad högpresterande disksupport med låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar eller är värd för verksamhetskritisk produktionsmiljö | HDD-baserad kostnadseffektiv disksupport för Dev/Test-VM-scenarier |
| Scenario  | Produktion och prestandakänsliga arbetsbelastningar | Dev/Test, icke-kritiska, <br>Lågfrekvent åtkomst |
| Diskstorlek | P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB | Ohanterade diskar: 1 GB-1 TB <br><br>Hanterade diskar:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB |
| Maxdataflöde per disk | 200 MB/s | 60 MB/s |
| Max-IOPS per disk | 5000 IOPS | 500 IOPS |
