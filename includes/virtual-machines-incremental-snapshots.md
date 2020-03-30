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
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014535"
---
## <a name="overview"></a>Översikt
Azure Storage ger möjlighet att ta ögonblicksbilder av blobbar. Ögonblicksbilder fångar blob-tillståndet vid den tidpunkten. I den här artikeln beskriver vi ett scenario där du kan underhålla säkerhetskopior av virtuella datordiskar med hjälp av ögonblicksbilder. Du kan använda den här metoden när du väljer att inte använda Azure Backup and Recovery Service och vill skapa en anpassad säkerhetskopieringsstrategi för dina virtuella datordiskar.

Virtuella Azure-datorer lagras som sidblobar i Azure Storage. Eftersom vi beskriver en strategi för säkerhetskopiering för virtuella datordiskar i den här artikeln hänvisar vi till ögonblicksbilder i samband med sidblobar. Mer information om ögonblicksbilder finns [i Skapa en ögonblicksbild av en Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Vad är en ögonblicksbild?
En blob-ögonblicksbild är en skrivskyddad version av en blob som fångas vid en tidpunkt. När en ögonblicksbild har skapats kan den läsas, kopieras eller tas bort, men inte ändras. Ögonblicksbilder är ett sätt att säkerhetskopiera en blob som den visas vid en tidpunkt. Fram till REST version 2015-04-05 hade du möjlighet att kopiera fullständiga ögonblicksbilder. Med REST-versionen 2015-07-08 och högre kan du även kopiera inkrementella ögonblicksbilder.

## <a name="full-snapshot-copy"></a>Fullständig kopia av ögonblicksbild
Ögonblicksbilder kan kopieras till ett annat lagringskonto som en blob för att behålla säkerhetskopior av basbloben. Du kan också kopiera en ögonblicksbild över basbloben, som är som att återställa blobben till en tidigare version. När en ögonblicksbild kopieras från ett lagringskonto till ett annat upptar den samma utrymme som bassidans blob. Därför är det långsamt att kopiera hela ögonblicksbilder från ett lagringskonto till ett annat och förbrukar mycket utrymme i mållagringskontot.

> [!NOTE]
> Om du kopierar basbloben till ett annat mål kopieras inte ögonblicksbilderna av blobben tillsammans med den. Om du skriver över en basblob med en kopia påverkas inte ögonblicksbilder som är associerade med basbloben och förblir intakta under basblobbnamnet.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Säkerhetskopiera diskar med ögonblicksbilder
Som en strategi för säkerhetskopiering för dina virtuella datordiskar kan du ta periodiska ögonblicksbilder av disken eller sidbloben och kopiera dem till ett annat lagringskonto med verktyg som [Kopiera Blob-åtgärd](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) eller [AzCopy](../articles/storage/common/storage-use-azcopy.md). Du kan kopiera en ögonblicksbild till en målsidablob med ett annat namn. Den resulterande målsidans blob är en skrivbar sidblob och inte en ögonblicksbild. Senare i den här artikeln beskriver vi steg för att göra säkerhetskopior av virtuella datordiskar med hjälp av ögonblicksbilder.

### <a name="restore-disks-using-snapshots"></a>Återställa diskar med ögonblicksbilder
När det är dags att återställa disken till en stabil version som tidigare har tagits i en av ögonblicksbilderna för säkerhetskopiering kan du kopiera en ögonblicksbild över bassidans blob. När ögonblicksbilden har befordrats till bassidans blob, finns ögonblicksbilden kvar, men dess källa skrivs över med en kopia som kan både läsas och skrivas. Senare i den här artikeln beskriver vi steg för att återställa en tidigare version av din disk från dess ögonblicksbild.

### <a name="implementing-full-snapshot-copy"></a>Implementera fullständig ögonblicksbildkopia
Du kan implementera en fullständig ögonblicksbild genom att göra följande,

* Ta först en ögonblicksbild av basbloben med hjälp av åtgärden [Ögonblicksbildblobb.](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)
* Kopiera sedan ögonblicksbilden till ett mållagringskonto med [kopiera blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Upprepa den här processen för att underhålla säkerhetskopior av basbloben.

## <a name="incremental-snapshot-copy"></a>Inkrementell ögonblicksbildkopia
Den nya funktionen i [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API är ett mycket bättre sätt att säkerhetskopiera ögonblicksbilder av dina sidblobar eller diskar. API:et returnerar listan över ändringar mellan basbloben och ögonblicksbilderna, vilket minskar mängden lagringsutrymme som används på säkerhetskopieringskontot. API:et stöder sidblobar på Premium Storage samt standardlagring. Med det här API:et kan du skapa snabbare och effektivare säkerhetskopieringslösningar för virtuella Azure-datorer. Detta API kommer att vara tillgängligt med REST-versionen 2015-07-08 och högre.

Med inkrementell ögonblicksbildkopia kan du kopiera från ett lagringskonto till ett annat skillnaden mellan,

* Basblob och dess ögonblicksbild ELLER
* Två ögonblicksbilder av basblobben

Under förutsättning att följande villkor är uppfyllda

* Blobben skapades den 1 januari-2016 eller senare.
* Blobben skrevs inte över med [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) eller [Kopiera blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) mellan två ögonblicksbilder.

**Den**här funktionen är tillgänglig för Azure- och Standard-Azure-sidblobar.

När du har en anpassad strategi för säkerhetskopiering med ögonblicksbilder kan det vara långsamt att kopiera ögonblicksbilder från ett lagringskonto till ett annat och förbruka mycket lagringsutrymme. I stället för att kopiera hela ögonblicksbilden till ett lagringskonto för säkerhetskopiering kan du skriva skillnaden mellan på varandra följande ögonblicksbilder till en säkerhetskopieringssidablob. På så sätt minskar tiden för att kopiera och utrymmet för att lagra säkerhetskopior avsevärt.

### <a name="implementing-incremental-snapshot-copy"></a>Implementera inkrementell ögonblicksbildkopia
Du kan implementera inkrementell ögonblicksbildkopia genom att göra följande,

* Ta en ögonblicksbild av basbloben med [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Kopiera ögonblicksbilden till kontot för säkerhetskopiering av mål i samma eller någon annan Azure-region med [kopiera blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Det här är bloben för säkerhetskopian. Ta en ögonblicksbild av säkerhetskopieringssidans blob och lagra den i säkerhetskopieringskontot.
* Ta en ny ögonblicksbild av basbloben med ögonblicksbildblobb.
* Få skillnaden mellan den första och den andra ögonblicksbilden av basbloben med [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Använd den nya parametern **prevsnapshot**för att ange DateTime-värdet för den ögonblicksbild som du vill få skillnaden med. När den här parametern finns innehåller REST-svaret endast de sidor som har ändrats mellan målögonblicksbild och tidigare ögonblicksbilder, inklusive tydliga sidor.
* Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) för att tillämpa ändringarna på bloben för säkerhetskopieringssidan.
* Ta slutligen en ögonblicksbild av säkerhetskopieringssidans blob och lagra den i lagringskontot för säkerhetskopiering.

I nästa avsnitt beskriver vi mer i detalj hur du kan underhålla säkerhetskopior av diskar med hjälp av Inkrementell snapshotkopia

## <a name="scenario"></a>Scenario
I det här avsnittet beskriver vi ett scenario som innebär en anpassad strategi för säkerhetskopiering för virtuella datordiskar med ögonblicksbilder.

Överväg en Azure VM-serie i DS-serien med en premiumlagrings-P30-disk ansluten. Den P30 disken kallas *mypremiumdisk* lagras i en premie lagring konto som kallas *mypremiumaccount*. Ett standardlagringskonto som kallas *mybackupstdaccount* används för att lagra säkerhetskopian av *mypremiumdisk*. Vi vill behålla en ögonblicksbild av *minpremiumdisk* var 12:e timme.

Mer information om hur du skapar ett lagringskonto finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Mer information om hur du säkerhetskopierar virtuella Azure-datorer finns i [Planera azure VM-säkerhetskopior](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Åtgärder för att underhålla säkerhetskopior av en disk med inkrementella ögonblicksbilder
Följande steg beskriver hur du tar ögonblicksbilder av *mypremiumdisk* och underhåller säkerhetskopior i *mybackupstdaccount*. Säkerhetskopian är en standardsida blob kallas *mybackupstdpageblob*. Den säkerhetskopieringssida blob alltid återspeglar samma tillstånd som den sista ögonblicksbilden av *mypremiumdisk*.

1. Skapa säkerhetskopieringssidan blob för din premium lagring disk, genom att ta en ögonblicksbild av *mypremiumdisk* kallas *mypremiumdisk_ss1*.
2. Kopiera den här ögonblicksbilden till mybackupstdaccount som en sidblob som kallas *mybackupstdpageblob*.
3. Ta en ögonblicksbild av *mybackupstdpageblob* kallas *mybackupstdpageblob_ss1*, med [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) och lagra den i *mybackupstdaccount*.
4. Under säkerhetskopieringsfönstret, skapa en annan ögonblicksbild av *mypremiumdisk*, säger *mypremiumdisk_ss2*, och lagra den i *mypremiumaccount*.
5. Hämta inkrementella ändringar mellan de två ögonblicksbilderna, *mypremiumdisk_ss2* och *mypremiumdisk_ss1*, med [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) på *mypremiumdisk_ss2* med **parametern prevsnapshot** inställd på tidsstämpeln *för mypremiumdisk_ss1*. Skriv dessa inkrementella ändringar i säkerhetskopian blob *mybackupstdpageblob* i *mybackupstdaccount*. Om det finns borttagna områden i de inkrementella ändringarna måste de rensas från kortlupen på sidan. Använd [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) för att skriva inkrementella ändringar i kortlupen på sidan för säkerhetskopiering.
6. Ta en ögonblicksbild av säkerhetskopieringssidan blob *mybackupstdpageblob*, kallas *mybackupstdpageblob_ss2*. Ta bort den tidigare *ögonblicksbilden mypremiumdisk_ss1* från premiumlagringskontot.
7. Upprepa steg 4-6 varje säkerhetskopieringsfönster. På så sätt kan du underhålla säkerhetskopior av *mypremiumdisk* i ett standardlagringskonto.

![Säkerhetskopiera disk med inkrementella ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Steg för att återställa en disk från ögonblicksbilder
Följande steg, beskriva hur du återställer premium disken, *mypremiumdisk* till en tidigare ögonblicksbild från backup lagringskonto *mybackupstdaccount*.

1. Identifiera den tidpunkt som du vill återställa premiumdisken till. Låt oss säga att det är ögonblicksbild *mybackupstdpageblob_ss2*, som lagras i backup lagringskonto *mybackupstdaccount*.
2. I mybackupstdaccount, främja *ögonblicksbilden mybackupstdpageblob_ss2* som den nya backup bassidan blob *mybackupstdpageblobrestored*.
3. Ta en ögonblicksbild av den här återställda säkerhetskopieringssidan, som kallas *mybackupstdpageblobrestored_ss1*.
4. Kopiera den återställda sidan blob *mybackupstdpageblobrestored* från *mybackupstdaccount* till *mypremiumaccount* som den nya premium *disken mypremiumdiskrestored*.
5. Ta en ögonblicksbild av *mypremiumdiskrestored*, kallas *mypremiumdiskrestored_ss1* för att göra framtida inkrementella säkerhetskopior.
6. Peka DS-serien VM till den återställda disken *mypremiumdiskrestored* och lossa den gamla *mypremiumdisk* från den virtuella datorn.
7. Börja säkerhetskopieringsprocessen som beskrivs i föregående avsnitt för den återställda disken *mypremiumdiskrestored*, med hjälp av *mybackupstdpageblobrestored* som backup sida blob.

![Återställa disk från ögonblicksbilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Efterföljande moment
Använd följande länkar om du vill veta mer om hur du skapar ögonblicksbilder av en blob och planerar infrastrukturen för säkerhetskopiering av virtuella datorer.

* [Skapa en ögonblicksbild av en blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planera infrastrukturen för säkerhetskopiering av virtuella datorer](../articles/backup/backup-azure-vms-introduction.md)

