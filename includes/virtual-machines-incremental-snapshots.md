---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960224"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Säkerhetskopiera Azure ohanterade Virtuella datordiskar med inkrementella ögonblicksbilder
## <a name="overview"></a>Översikt
Azure Storage tillhandahåller möjligheten att ta ögonblicksbilder av blobar. Ögonblicksbilder avbilda blob-tillstånd vid den tidpunkten i tid. I den här artikeln beskriver vi ett scenario där du kan underhålla säkerhetskopior av virtuella diskar med hjälp av ögonblicksbilder. Du kan använda den här metoden när du väljer att inte använda Azure Backup och Recovery-tjänsten och vill skapa en anpassad strategi för säkerhetskopiering för virtuella datordiskar.

Azure-datordiskar lagras som sidblobar i Azure Storage. Eftersom vi beskriver en strategi för säkerhetskopiering för virtuella diskar i den här artikeln, som vi refererar till ögonblicksbilder i kontexten för sidblobar. Läs mer om ögonblicksbilder [skapa en ögonblicksbild av en Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Vad är en ögonblicksbild?
En blob-ögonblicksbild är en skrivskyddad version av en blob som hämtas vid en tidpunkt i tid. När en ögonblicksbild har skapats, kan det läsas, kopieras, eller ta bort, men inte har ändrats. Ögonblicksbilder är ett sätt att säkerhetskopiera en blob som det visas vid en tidpunkt. Fram REST version 2015-04-05, var du tvungen att kunna kopiera fullständiga ögonblicksbilder. Med REST-version 2015-07-08 och senare, du kan också kopiera inkrementella ögonblicksbilder.

## <a name="full-snapshot-copy"></a>Fullständig ögonblicksbild kopia
Ögonblicksbilder kan kopieras till ett annat lagringskonto som en blob till behåller säkerhetskopior av grundläggande blob. Du kan också kopiera en ögonblicksbild över dess grundläggande blob som liknar återställa blob till en tidigare version. När en ögonblicksbild kopieras från ett lagringskonto till ett annat, upptar samma adressutrymme som bassida-blob. Därför kopierar hela ögonblicksbilder från ett lagringskonto till ett annat långsamt och förbrukar mycket utrymme krävs på mål-lagringskontot.

> [!NOTE]
> Om du kopierar den grundläggande blobben till en annan plats, kopieras inte ögonblicksbilder för bloben tillsammans med den. Om du skriva över en grundläggande blob med en kopia ögonblicksbilder som är associerade med den grundläggande blobben påverkas inte och förblir intakta under grundläggande blobnamnet.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Säkerhetskopiera diskar med hjälp av ögonblicksbilder
Som en strategi för säkerhetskopiering för virtuella datordiskar, kan du ta regelbundna ögonblicksbilder av disk- eller blob och kopiera dem till en annan storage-konto med hjälp av verktygen som [kopiering av Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) åtgärden eller [AzCopy](../articles/storage/common/storage-use-azcopy.md). Du kan kopiera en ögonblicksbild till en mål-sidblob med ett annat namn. Den resulterande sidan målblobben är en skrivbar sidblob och inte en ögonblicksbild. Senare i den här artikeln beskriver vi steg för att göra säkerhetskopior av virtuella diskar med hjälp av ögonblicksbilder.

### <a name="restore-disks-using-snapshots"></a>Återställ diskar med hjälp av ögonblicksbilder
När det är dags att återställa disken till en stabil version som hämtades tidigare i en av ögonblicksbilderna av säkerhetskopior kan du kopiera en ögonblicksbild över bassida-blob. När ögonblicksbilden befordras till bassidan blob snapshot finns kvar, men dess källa skrivs över med en kopia som kan både läsas och skrivas. Senare i den här artikeln beskriver vi steg för att återställa en tidigare version av disken från dess ögonblicksbild.

### <a name="implementing-full-snapshot-copy"></a>Implementera kopia för fullständig ögonblicksbild
Du kan implementera en fullständig ögonblicksbild kopia genom att göra följande

* Först ta en ögonblicksbild av en grundläggande blob med hjälp av den [ta ögonblicksbild av Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) igen.
* Kopiera sedan ögonblicksbilden till en mål-lagringskontot med [kopiering av Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Upprepa processen för att underhålla säkerhetskopior av din grundläggande blob.

## <a name="incremental-snapshot-copy"></a>Kopiering av inkrementell ögonblicksbild
Den nya funktionen i den [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API gör det mycket lättare för säkerhetskopiering av ögonblicksbilder av din sidblobar eller diskar. API: et returnerar en lista över ändringar mellan grundläggande blob och ögonblicksbilder, vilket minskar mängden lagringsutrymme som används för säkerhetskopiering kontot. API: et stöder sidblobar på Premium-lagring, samt standardlagring. Med detta API kan skapa du snabbare och effektivare lösningar för säkerhetskopiering för virtuella Azure-datorer. Detta API är tillgängliga med REST-version 2015-07-08 och högre.

Inkrementell kopiering av ögonblicksbild kan du kopiera från ett lagringskonto till ett annat skillnaden mellan

* Grundläggande blob- och dess ögonblicksbild eller
* Alla två grundläggande blob-ögonblicksbilder

Om följande villkor är uppfyllda

* Blobben har skapats på Jan-1-2016 eller senare.
* Blobben som inte skrivas över med [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) eller [kopiering av Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) mellan två ögonblicksbilder.

**Obs**: den här funktionen är tillgänglig för Premium och Standard Azure-Sidblobar.

När du har en anpassad strategi för säkerhetskopiering med ögonblicksbilder, kopierar ögonblicksbilder från ett lagringskonto till ett annat kan vara långsam och konsumerar mycket lagringsutrymme. Du kan skriva skillnaden mellan på varandra följande ögonblicksbilder av en säkerhetskopiering sidblob istället för att kopiera hela ögonblicksbilden till ett konto för lagring av säkerhetskopior. På så sätt kan betydligt tiden för att kopiera och utrymme för lagring av säkerhetskopior.

### <a name="implementing-incremental-snapshot-copy"></a>Implementera inkrementell ögonblicksbild kopia
Du kan implementera inkrementell ögonblicksbild kopiera genom att göra följande

* Ta en ögonblicksbild av grundläggande bloben med [ta ögonblicksbild av Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Kopiera ögonblicksbilden till målkontot för lagring av säkerhetskopior i samma eller alla andra Azure-region med [kopiering av Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Det här är den säkerhetskopierade sidblob. Ta en ögonblicksbild av säkerhetskopiering sidans blob och lagra den i backup-kontot.
* Ta en annan ögonblicksbild av den grundläggande blob med ta ögonblicksbild av Blob.
* Hämta skillnaden mellan de första och andra ögonblicksbilderna av grundläggande bloben med [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Använd den nya parametern **prevsnapshot**, för att ange den ögonblicksbild som du vill hämta skillnaden jämfört med DateTime-värdet. Om den här parametern finns innehåller REST-svaret endast de sidor som har ändrats mellan mål ögonblicksbild och tidigare ögonblicksbild, inklusive Rensa sidor.
* Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) att tillämpa ändringarna på säkerhetskopiering sidans blob.
* Slutligen kan ta en ögonblicksbild av säkerhetskopiering sidans blob och lagra den på lagringskontot för säkerhetskopiering.

I nästa avsnitt beskriver vi i detalj hur du kan underhålla säkerhetskopior av diskar med hjälp av inkrementell kopiering av ögonblicksbild

## <a name="scenario"></a>Scenario
I det här avsnittet beskriver vi ett scenario med en anpassad strategi för säkerhetskopiering för virtuella diskar med hjälp av ögonblicksbilder.

Beakta en Azure virtuell dator för DS-serien med ett premium storage P30-disk som är ansluten. P30-disk kallas *mypremiumdisk* lagras i ett premiumlagringskonto som kallas *mypremiumaccount*. Ett standardlagringskonto kallas *mybackupstdaccount* används för att lagra säkerhetskopian av *mypremiumdisk*. Vi vill hålla en ögonblicksbild av *mypremiumdisk* var 12: e timme.

Läs om hur du skapar ett lagringskonto i [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Läs om hur du säkerhetskopierar virtuella Azure-datorer, [planera Virtuella Azure-säkerhetskopieringar](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Steg för att underhålla säkerhetskopior för en disk med inkrementella ögonblicksbilder
Följande steg beskriver hur du ta ögonblicksbilder av *mypremiumdisk* och underhålla säkerhetskopior på *mybackupstdaccount*. Säkerhetskopieringen är en standard sidblob kallas *mybackupstdpageblob*. Säkerhetskopiering av sidans blob alltid visar samma tillstånd som den senaste ögonblicksbilden av *mypremiumdisk*.

1. Skapa backup sidblob för din premium-lagringsdisk genom att ta en ögonblicksbild av *mypremiumdisk* kallas *mypremiumdisk_ss1*.
2. Kopiera den här ögonblicksbilden till mybackupstdaccount som en sidblobb kallas *mybackupstdpageblob*.
3. Ta en ögonblicksbild av *mybackupstdpageblob* kallas *mybackupstdpageblob_ss1*med hjälp av [ta ögonblicksbild av Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) och lagra den i *mybackupstdaccount*.
4. Skapa en annan ögonblicksbild av under säkerhetskopieringen, *mypremiumdisk*, exempelvis *mypremiumdisk_ss2*, och lagra den i *mypremiumaccount*.
5. Få stegvisa ändringar mellan de två ögonblicksbilderna *mypremiumdisk_ss2* och *mypremiumdisk_ss1*med hjälp av [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) på *mypremiumdisk_ ss2* med den **prevsnapshot** parameteruppsättning tidsstämpeln för *mypremiumdisk_ss1*. Skriva dessa stegvisa ändringar till den säkerhetskopierade sidblob *mybackupstdpageblob* i *mybackupstdaccount*. Om det finns borttagna intervall i stegvisa ändringar, måste de tas bort från säkerhetskopiering sidans blob. Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) att skriva inkrementella ändringar till säkerhetskopiering sidans blob.
6. Ta en ögonblicksbild av säkerhetskopiering sidblob *mybackupstdpageblob*, kallas *mybackupstdpageblob_ss2*. Ta bort den tidigare ögonblicksbilden *mypremiumdisk_ss1* från premium storage-konto.
7. Upprepa steg 4 – 6 varje säkerhetskopieringsintervallet. På så sätt kan du behålla säkerhetskopior av *mypremiumdisk* i ett standardlagringskonto.

![Säkerhetskopiera diskar med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Steg för att återställa en disk från ögonblicksbilder
Följande steg beskriver hur du återställer premiumdisk *mypremiumdisk* till en tidigare ögonblicksbild från backup storage-kontot *mybackupstdaccount*.

1. Identifiera den punkt som du vill återställa disken till premium. Anta att den ögonblicksbild *mybackupstdpageblob_ss2*, som lagras i lagringskontot för säkerhetskopiering *mybackupstdaccount*.
2. Flytta upp ögonblicksbilden i mybackupstdaccount, *mybackupstdpageblob_ss2* som ny säkerhetskopiering bassida blob *mybackupstdpageblobrestored*.
3. Ta en ögonblicksbild av den här återställda säkerhetskopiering sidblob, kallas *mybackupstdpageblobrestored_ss1*.
4. Kopiera den återställda sidblob *mybackupstdpageblobrestored* från *mybackupstdaccount* till *mypremiumaccount* som den nya premium-disken  *mypremiumdiskrestored*.
5. Ta en ögonblicksbild av *mypremiumdiskrestored*, kallas *mypremiumdiskrestored_ss1* för att göra framtida inkrementella säkerhetskopieringar.
6. Peka DS-serien virtuella datorn på den återställda disken *mypremiumdiskrestored* och koppla från gammalt *mypremiumdisk* från den virtuella datorn.
7. Starta processen för säkerhetskopiering som beskrivs i föregående avsnitt för den återställda disken *mypremiumdiskrestored*med hjälp av den *mybackupstdpageblobrestored* som säkerhetskopiering sidblobb.

![Återställ disken från ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Nästa steg
Använd följande länkar om du vill veta mer om att skapa ögonblicksbilder av en blob och planera din infrastruktur för säkerhetskopiering av virtuell dator.

* [Skapa en ögonblicksbild av en Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planera din infrastruktur för säkerhetskopiering av virtuell dator](../articles/backup/backup-azure-vms-introduction.md)

