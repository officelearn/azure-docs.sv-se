# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Säkerhetskopiera Azure ohanterade Virtuella diskar med inkrementell ögonblicksbilder
## <a name="overview"></a>Översikt
Azure Storage ger möjlighet att ta ögonblicksbilder av blobbar. Ögonblicksbilder avbilda tillståndet blob då i tid. I den här artikeln beskriver vi ett scenario där du kan underhålla säkerhetskopior av virtuella diskar med hjälp av ögonblicksbilder. Du kan använda den här metoden när du inte vill använda Azure Backup och Recovery-tjänsten och vill skapa en anpassad strategi för säkerhetskopiering för virtuella diskar.

Azure virtuella diskar lagras som sidblobbar i Azure Storage. Eftersom vi som en strategi för säkerhetskopiering för virtuella diskar i den här artikeln beskriver refererar vi till ögonblicksbilder i samband med sidblobar. Mer information om ögonblicksbilder avser [skapa en ögonblicksbild av en Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Vad är en ögonblicksbild?
En blob-ögonblicksbild är en skrivskyddad version av en blob som hämtas vid en viss tidpunkt. När en ögonblicksbild har skapats, kan den läsa, kopieras, eller ta bort, men inte har ändrats. Ögonblicksbilder är ett sätt att säkerhetskopiera en blob som det visas vid en tidpunkt. Fram vila version 2015-04-05, var du tvungen att kunna kopiera fullständig ögonblicksbilder. Med övriga version 2015-07-08 och senare, du kan också kopiera inkrementell ögonblicksbilder.

## <a name="full-snapshot-copy"></a>Fullständig ögonblicksbild kopia
Ögonblicksbilder kan kopieras till ett annat lagringskonto som en blob att säkerhetskopior av grundläggande blob. Du kan också kopiera en ögonblicksbild över grundläggande blob, vilket är så återställer blob till en tidigare version. När en ögonblicksbild kopieras från ett lagringskonto till en annan, upptar samma utrymme som bassida-blob. Därför kopiera hela ögonblicksbilder från ett lagringskonto till en annan går långsamt och tar upp mycket utrymme i mål-lagringskontot.

> [!NOTE]
> Om du kopierar grundläggande blob till ett annat mål, kopieras inte ögonblicksbilder av blob tillsammans med den. Om du skriver över en grundläggande blob med en kopia ögonblicksbilder kopplade till grundläggande blob påverkas inte och intakta under grundläggande blobbnamnet.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Säkerhetskopiera diskar med hjälp av ögonblicksbilder
Som en strategi för säkerhetskopiering för virtuella diskar, kan du ta regelbundna ögonblicksbilder av disk- eller blob och kopiera dem till en annan storage-konto med hjälp av verktyg som [kopiera Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) åtgärden eller [AzCopy](../articles/storage/common/storage-use-azcopy.md). Du kan kopiera en ögonblicksbild till en mål-sidblob med ett annat namn. Den resulterande sidblob mål är en skrivbar sidblob och inte en ögonblicksbild. Senare i den här artikeln beskrivs stegen för att ta säkerhetskopior av virtuella diskar med hjälp av ögonblicksbilder.

### <a name="restore-disks-using-snapshots"></a>Återställa diskar med hjälp av ögonblicksbilder
När det är dags att återställa disken till en stabil version som tidigare har hämtats i ett av ögonblicksbilderna av säkerhetskopior kan du kopiera en ögonblicksbild över bassida-blob. När ögonblicksbilden befordras till bassidan blob ögonblicksbild förblir, men dess källa skrivs över med en kopia som kan vara både läses och skrivs. Nedan beskrivs stegen för att återställa en tidigare version av disken från dess ögonblicksbild.

### <a name="implementing-full-snapshot-copy"></a>Implementera kopia för fullständig ögonblicksbild
Du kan implementera en fullständig ögonblicksbild kopia genom att göra följande:

* Ta först en ögonblicksbild av den grundläggande blob med hjälp av den [ögonblicksbild Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) igen.
* Kopiera sedan ögonblicksbilden till ett mål lagring kontot med [kopiera Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Upprepa processen för att underhålla säkerhetskopior av din grundläggande blob.

## <a name="incremental-snapshot-copy"></a>Inkrementell ögonblicksbild kopia
Den nya funktionen i den [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API ger en mycket bättre sätt att säkerhetskopiera ögonblicksbilder av din sidblobbar eller diskar. API: et returnerar en lista över ändringar mellan grundläggande blob och ögonblicksbilder, vilket minskar mängden lagringsutrymme som används för kontot säkerhetskopiering. API: et stöder sidblobar på Premium-lagring samt standardlagring. Med detta API kan skapa du snabbare och effektivare säkerhetskopieringslösningar för virtuella Azure-datorer. Detta API är tillgängliga med REST-version 2015-07-08 och högre.

Inkrementell ögonblicksbild kopia kan du kopiera från ett lagringskonto till en annan skillnaden mellan

* Grundläggande blob och dess ögonblicksbild eller
* Två ögonblicksbilder av grundläggande blobben

Om följande villkor är uppfyllda,

* Blobben har skapats på Jan 1 2016 eller senare.
* Blob som inte skrivas över med [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) eller [kopiera Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) mellan två ögonblicksbilder.

**Obs**: den här funktionen är tillgänglig för Premium- och Standard Sidblobbar för Azure.

När du har en anpassad strategi för säkerhetskopiering med hjälp av ögonblicksbilder, kopiera ögonblicksbilderna från ett lagringskonto till en annan kan ta lång tid och kan förbruka mycket lagringsutrymme. Du kan skriva skillnaden mellan på varandra följande ögonblicksbilder av en säkerhetskopiering sidblob istället för att kopiera hela ögonblicksbilden till en backup storage-konto. Det här sättet betydligt tid att kopiera och utrymme för lagring av säkerhetskopior.

### <a name="implementing-incremental-snapshot-copy"></a>Implementera inkrementell ögonblicksbild kopia
Du kan implementera inkrementell ögonblicksbild kopia genom att göra följande:

* Ta en ögonblicksbild av den grundläggande blob med hjälp av [ögonblicksbild Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Kopiera ögonblicksbilden till målet säkerhetskopieringslagring kontot med [kopiera Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Detta är säkerhetskopiering sidblob. Ta en ögonblicksbild av säkerhetskopian sidblob och lagra den i kontot för säkerhetskopiering.
* Ta en annan ögonblicksbild av grundläggande blob med hjälp av ögonblicksbilder Blob.
* Hämta skillnaden mellan de första och andra ögonblicksbilderna av grundläggande blob med [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Använd den nya parametern **prevsnapshot**, för att ange den ögonblicksbild som du vill hämta skillnaden med DateTime-värdet. Om den här parametern finns innehåller REST-svaret bara de sidor som har ändrats mellan target ögonblicksbild och tidigare ögonblicksbild, inklusive Rensa sidor.
* Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) att tillämpa ändringarna på säkerhetskopian av sidan-blob.
* Slutligen kan ta en ögonblicksbild av säkerhetskopian sidblob och lagra den i backup storage-konto.

I nästa avsnitt, kommer beskrivs i detalj hur du kan upprätthålla säkerhetskopior av diskar med hjälp av inkrementell ögonblicksbild kopia

## <a name="scenario"></a>Scenario
I det här avsnittet beskriver vi ett scenario som omfattar en anpassad strategi för säkerhetskopiering för virtuella diskar med hjälp av ögonblicksbilder.

Beakta en Azure VM DS-serien med premium-lagring P30 disk ansluten. P30 disken kallas *mypremiumdisk* lagras i ett premiumlagringskonto som kallas *mypremiumaccount*. Ett standardlagringskonto kallas *mybackupstdaccount* används för att lagra säkerhetskopian av *mypremiumdisk*. Vi vill behålla en ögonblicksbild av *mypremiumdisk* var 12: e timme.

Mer information om att skapa storage-konto och diskar, referera till [om Azure storage-konton](../articles/storage/storage-create-storage-account.md).

Mer information om säkerhetskopiering av virtuella Azure-datorer, referera till [planera Azure VM-säkerhetskopieringar](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Steg för att underhålla säkerhetskopieringar av en disk med hjälp av inkrementell ögonblicksbilder
Följande steg beskriver hur du ta ögonblicksbilder av *mypremiumdisk* och underhålla säkerhetskopieringar i *mybackupstdaccount*. Säkerhetskopian är en standard sidblob kallas *mybackupstdpageblob*. Säkerhetskopiering sidblob visar alltid samma tillstånd som den senaste ögonblicksbilden av *mypremiumdisk*.

1. Skapa säkerhetskopiering sidblob för hårddisken premium-lagring genom att ta en ögonblicksbild av *mypremiumdisk* kallas *mypremiumdisk_ss1*.
2. Kopiera den här ögonblicksbilden till mybackupstdaccount som en sidblobb kallas *mybackupstdpageblob*.
3. Ta en ögonblicksbild av *mybackupstdpageblob* kallas *mybackupstdpageblob_ss1*med hjälp av [ögonblicksbild Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) och lagra den i *mybackupstdaccount*.
4. Skapa en annan ögonblicksbild av under säkerhetskopieringen, *mypremiumdisk*, säg *mypremiumdisk_ss2*, och lagra den i *mypremiumaccount*.
5. Hämta inkrementella ändringar mellan två ögonblicksbilder *mypremiumdisk_ss2* och *mypremiumdisk_ss1*med hjälp av [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) på *mypremiumdisk_ ss2* med den **prevsnapshot** parameterinställning tidsstämpeln för *mypremiumdisk_ss1*. Skriva dessa inkrementella ändringar till säkerhetskopiering sidblob *mybackupstdpageblob* i *mybackupstdaccount*. Om det finns borttagna intervall i inkrementella ändras, måste tas bort från säkerhetskopian av sidan-blob. Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) skriva inkrementella ändringar till säkerhetskopiering sidblob.
6. Ta en ögonblicksbild av säkerhetskopian sidblob *mybackupstdpageblob*, kallat *mybackupstdpageblob_ss2*. Ta bort den tidigare ögonblicksbilden av *mypremiumdisk_ss1* från premium storage-konto.
7. Upprepa steg 4 – 6 var säkerhetskopieringsintervallet. På så sätt kan du hantera säkerhetskopior av *mypremiumdisk* i ett standardlagringskonto.

![Säkerhetskopiera disk med hjälp av inkrementell ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Steg för att återställa en disk från ögonblicksbilder
Följande steg beskriver hur du återställer premium-disk *mypremiumdisk* till en tidigare ögonblicksbild från kontot för säkerhetskopieringslagring *mybackupstdaccount*.

1. Identifiera punkten i tid som du vill återställa premium disken. Anta att den har ögonblicksbilder *mybackupstdpageblob_ss2*, som lagras på kontot för säkerhetskopieringslagring *mybackupstdaccount*.
2. Befordra ögonblicksbilden i mybackupstdaccount, *mybackupstdpageblob_ss2* som ny säkerhetskopiering bassida blob *mybackupstdpageblobrestored*.
3. Ta en ögonblicksbild av den här återställda säkerhetskopiering sidblob, kallas *mybackupstdpageblobrestored_ss1*.
4. Kopiera den återställda sidblob *mybackupstdpageblobrestored* från *mybackupstdaccount* till *mypremiumaccount* som den nya disken premium  *mypremiumdiskrestored*.
5. Ta en ögonblicksbild av *mypremiumdiskrestored*, kallat *mypremiumdiskrestored_ss1* för att göra framtida inkrementella säkerhetskopieringar.
6. DS-serien VM peka återställda disken *mypremiumdiskrestored* och koppla från gammalt *mypremiumdisk* från den virtuella datorn.
7. Starta Säkerhetskopiering processen som beskrivs i föregående avsnitt för den återställda disken *mypremiumdiskrestored*med hjälp av den *mybackupstdpageblobrestored* som säkerhetskopiering sidblobb.

![Återställa disk från ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Nästa steg
Använd följande länkar om du vill lära dig mer om att skapa ögonblicksbilder av en blob och planera din infrastruktur för säkerhetskopiering av VM.

* [Skapa en ögonblicksbild av en Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planera infrastrukturen för säkerhetskopiering VM](../articles/backup/backup-azure-vms-introduction.md)

