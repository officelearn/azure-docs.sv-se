# <a name="azure-managed-disks-overview"></a>Översikt över Azure-hanterade diskar

Azure-hanterade diskar förenklar Diskhantering för virtuella Azure IaaS-datorer genom att hantera den [lagringskonton](../articles/storage/common/storage-introduction.md) som är associerade med VM-diskarna. Du behöver bara ange ([Premium](../articles/virtual-machines/windows/premium-storage.md) eller [Standard](../articles/virtual-machines/windows/standard-storage.md)) och storleken på disken som du behöver och Azure skapar och hanterar disken åt dig.

## <a name="benefits-of-managed-disks"></a>Fördelarna med hanterade diskar

Låt oss ta en titt på några av fördelarna med får du med hjälp av hanterade diskar från och med den här Channel 9 videon [bättre Azure VM återhämtning med hanterade diskar](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Enkel och skalbar distribution av Virtuella datorer

Hanterade diskar handtag lagring för dig i bakgrunden. Tidigare var tvungen du att skapa storage-konton för att lagra diskar (VHD-filer) för din virtuella Azure-datorer. När du ökar, var du tvungen att kontrollera att du har skapat ytterligare lagringskonton, så att du inte överskrider gränsen på IOPS för lagring med alla diskar. För hanterade diskar hantering lagring, är du inte längre begränsad lagringskontogränser (till exempel 20 000 IOPS / -kontot). Du måste också längre kopiera egna, anpassade avbildningar (VHD-filer) till flera lagringskonton. Du kan hantera dem på en central plats – ett lagringskonto per Azure-region – och använda dem för att skapa hundratals virtuella datorer i en prenumeration.

Hanterade diskar gör att du kan skapa upp till 10 000 VM **diskar** för en prenumeration som gör att du kan skapa tusentals **VMs** i en enda prenumeration. Den här funktionen dessutom ytterligare ökar skalbarheten för [virtuella skala uppsättningar (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) genom att du kan skapa upp till tusen virtuella datorer i en VMSS med hjälp av en Marketplace-avbildning.

### <a name="better-reliability-for-availability-sets"></a>Bättre tillförlitlighet för Tillgänglighetsuppsättningar

Hanterade diskar ger bättre tillförlitlighet för Tillgänglighetsuppsättningar genom att säkerställa att diskar [virtuella datorer i en Tillgänglighetsuppsättning](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Detta sker automatiskt placerar diskarna i olika skalningsenheter (stämplar). Om en stämpel misslyckas på grund av maskinvara eller programvara, inte bara VM-instanser med diskar på de stämplarna. Till exempel att anta du har ett program som körs på fem virtuella datorer och de virtuella datorerna finns i en Tillgänglighetsuppsättning. Diskarna fortsätter för dessa virtuella datorer inte alla lagras i samma stämpel, så om en stämpel kraschar andra instanser av programmet att köras.

### <a name="highly-durable-and-available"></a>Extremt tillförlitliga och tillgängliga

Azure-diskar har en tillförlitlighet på 99,999 %. REST-lättare att veta att du har tre kopior av dina data som möjliggör hög hållbarhet. Om en eller två repliker får problem kan återstående replik(er) ta över för att säkerställa beständigheten hos dina data och ge en hög tolerans mot fel. Tack vare den här arkitekturen har Azure oavbrutet kunnat tillhandahålla tillförlitlighet på storföretagsnivå för sina IaaS-diskar. Azure är branschledande inom detta område med 0 % driftstopp per år. 

### <a name="granular-access-control"></a>Detaljerad åtkomstkontroll

Du kan använda [rollbaserad åtkomstkontroll (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) tilldela specifika behörigheter för hanterade diskar till en eller flera användare. Hanterade diskar visar olika åtgärder, inklusive läsa, skriva (skapa/uppdatera), ta bort och hämtar en [signatur för delad åtkomst (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) för disken. Du kan bevilja åtkomst till de åtgärder som en person behöver för att utföra sitt jobb. Till exempel om du inte vill att en person för att kopiera en hanterade diskar till ett lagringskonto kan du inte att bevilja åtkomst till export-åtgärd för den hantera disken. På samma sätt om du inte vill att en person för att använda en SAS-URI för att kopiera en hanterade diskar, kan du inte bevilja behörigheten till den hantera disken.

### <a name="azure-backup-service-support"></a>Stöd för Azure Backup service
Använda Azure Backup service med hanterade diskar för att skapa en säkerhetskopiering med tidsbaserade säkerhetskopieringar, enkelt VM-återställning och principer för lagring av säkerhetskopior. Hanterade diskar stöder endast lokalt Redundant lagring (LRS) som replikeringsalternativet; Det innebär att den bevarar tre kopior av data inom en enskild region. För regional katastrofåterställning, måste du säkerhetskopiera din Virtuella diskar i en annan region med hjälp av [Azure Backup service](../articles/backup/backup-introduction-to-azure-backup.md) och ett GRS-lagringskonto som säkerhetskopieringsvalvet. För närvarande storlek datadisk för Azure Backup stöder upp till 1TB för säkerhetskopiering. Läs mer om detta i [med hjälp av Azure Backup-tjänsten för virtuella datorer med hanterade diskar](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder hanterade diskar på följande för debitering:
* Lagringstyp

* Diskstorlek

* Antal transaktioner

* Utgående dataöverföringar

* Hanterade diskbilder (fullständig kopia)

Låt oss ta en närmare titt på dessa.

**Lagringstyp:** hanterade diskar erbjuder 2 prestandanivåer: [Premium](../articles/virtual-machines/windows/premium-storage.md) (SSD-baserad) och [Standard](../articles/virtual-machines/windows/standard-storage.md) (HDD-baserat). Fakturering för hanterade diskar beror på vilken typ av lagring som du har valt för disken.


**Diskstorlek**: fakturering för hanterade diskar beror på den etablerade storleken på disken. Azure mappar etablerade storleken (avrunda uppåt) till närmaste hanterade diskar alternativet som anges i tabellerna nedan. Varje hanterade disken mappar till en av de etablerade storlekarna som stöds och därefter faktureras. Om du skapar en standard hanterade diskar och ange en etablerade storlek på 200 GB debiteras du till exempel enligt prissättning S20 disktyp.

Här är storlekar för diskar som är tillgängliga för en hanterad premium-disk:

| **Premium hanteras <br>disktyp** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Diskstorlek        | 32 GiB   | 64 GiB   | 128 GiB  | 256 GiB  | 512 GiB  | 1024 giB (1 TiB) | 2048 giB (2 TiB) | 4095 giB (4 TiB) | 


Här är storlekar för diskar som är tillgängliga för en standard hanterade diskar:

| **Standard hanteras <br>disktyp** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Diskstorlek        | 32 GiB   | 64 GiB   | 128 GiB | 512 GiB | 1024 giB (1 TiB) | 2048 giB (2 TiB) | 4095 giB (4 TiB) | 


**Antal transaktioner**: du debiteras för antal transaktioner som du kan utföra på en standard hanterade disk. Det kostar inget för transaktioner för en hanterad premium-disk.

**Utgående dataöverföringar**: [utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data skickas från Azure-datacenter) debiteras för bandbreddsanvändning.

Detaljerad information om priser för hanterade diskar finns [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Hanterade diskbilder

En hanterad ögonblicksbild är en skrivskyddad fullständig kopia av en hanterad disk som lagras som standard hanterade disk som standard. Med ögonblicksbilder, kan du säkerhetskopiera hanterade diskar när som helst i tid. Dessa ögonblicksbilder finns oberoende av källdisken och kan användas för att skapa nya hanterade diskar. De debiteras baserat på används storlek. Till exempel om du skapar en ögonblicksbild av en hanterad disk med 64 GiB etablerad kapacitet och storleken för data som används på 10 GiB kommer ögonblicksbild att debiteras endast för 10 GiB data används.  

[Inkrementell ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) stöds inte för närvarande för hanterade diskar, men kommer att stödjas framöver.

Mer information om hur du skapar ögonblicksbilder med hanterade diskar finns följande resurser:

* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Avbildningar

Hanterade diskar också stöd för att skapa en anpassad hanterad avbildning. Du kan skapa en avbildning från din anpassade virtuella hårddiskar i ett lagringskonto eller direkt från en generaliserad (sys prepped) VM. Detta samlar in i en enda avbildning samtliga hanterade diskar som är kopplad till en virtuell dator, inklusive både i Operativsystemet och datadiskarna. Detta gör att skapa hundratals virtuella datorer med hjälp av den anpassade avbildningen utan att behöva kopiera eller hantera storage-konton.

Information om hur du skapar bilder finns i följande artiklar:
* [Så här skapar du en hanterad avbildning av en generaliserad virtuell dator i Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Hur du generalisera och avbildar en Linux-dator som använder Azure CLI 2.0](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Bilder jämfört med ögonblicksbilder

Du ser ofta ordet ”bild” användes med virtuella datorer, men nu ”ögonblicksbilder” samt. Det är viktigt att förstå skillnaden mellan dessa. För hanterade diskar, kan du ta en bild av en generaliserad virtuell dator som har frigjorts. Den här avbildningen innehåller alla diskar som är kopplade till den virtuella datorn. Du kan använda den här avbildningen för att skapa en ny virtuell dator och den innehåller alla diskar.

En ögonblicksbild är en kopia av en disk på platsen i hämtas. Det gäller bara för en disk. Om du har en virtuell dator som bara har en disk (OS) kan du skapa en virtuell dator från ögonblicksbilden eller avbildningen ta en ögonblicksbild eller en avbildning av den.

Vad händer om en virtuell dator har fem diskar och de stripe? Du kan ta en ögonblicksbild av var och en av diskarna, men det finns inga medvetenhet inifrån den virtuella datorn över diskar – status ögonblicksbilderna veta endast om en disk. I det här fallet ögonblicksbilderna skulle behöva samordnas med varandra och som inte stöds.

## <a name="managed-disks-and-encryption"></a>Hanterade diskar och kryptering

Det finns två typer av kryptering att diskutera förhållande till hanterade diskar. Den första är Storage Service kryptering (SSE), som utförs av storage-tjänst. Den andra är Azure Disk Encryption, där du kan aktivera på Operativsystemet och datadiskarna för dina virtuella datorer.

### <a name="storage-service-encryption-sse"></a>Storage Service-kryptering (SSE)

[Azure Storage Service-kryptering](../articles/storage/common/storage-service-encryption.md) tillhandahåller kryptering i vila och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. SSE är aktiverat som standard för alla hanterade diskar, ögonblicksbilder och bilder i alla regioner där hanterade diskar är tillgänglig. Startar den 10 juni 2017 samtliga nya hanterade diskar-ögonblicksbilder-avbildningar och nya data skrivs till befintliga hanterade diskar är automatiskt krypterat i vila med nycklar som hanteras av Microsoft.  Besök den [hanterade diskar vanliga frågor om sidan](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) för mer information.


### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption kan du kryptera Operativsystemet och datadiskarna som används av en virtuell IaaS-dator. Detta omfattar hanterade diskar. För Windows krypteras enheterna med BitLocker-kryptering branschstandard. För Linux krypteras diskarna med DM-Crypt-teknik. Detta är integrerad med Azure Key Vault så att du kan styra och hantera krypteringsnycklar disk. Mer information finns [Azure Disk Encryption för Windows och Linux IaaS-VM](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade diskar finns i följande artiklar.

### <a name="get-started-with-managed-disks"></a>Kom igång med Managed Disks

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Ansluta en datadisk hanterade till en virtuell Windows-dator med hjälp av PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Lägga till en hanterad disk till en virtuell Linux-dator](../articles/virtual-machines/linux/add-disk.md)

* [Hanterade diskar PowerShell-exempelskript](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Använda hanterade diskar i Azure Resource Manager-mallar](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Jämför lagringsalternativ för hanterade diskar

* [Premium-lagring och diskar](../articles/virtual-machines/windows/premium-storage.md)

* [Standardlagring och diskar](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Driftvägledning

* [Migrera från andra plattformar och AWS till hanterade diskar i Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Konvertera virtuella Azure-datorer till hanterade diskar i Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
