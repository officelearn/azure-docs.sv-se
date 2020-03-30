---
title: Online säkerhetskopiering och dataåterställning på begäran i Azure Cosmos DB
description: I den här artikeln beskrivs hur automatisk säkerhetskopiering online och dataåterställning på begäran fungerar i Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240437"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online säkerhetskopiering och dataåterställning på begäran i Azure Cosmos DB

Azure Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. De automatiska säkerhetskopieringarna tas utan att påverka databasåtgärdernas prestanda eller tillgänglighet. Alla säkerhetskopior lagras separat i en lagringstjänst och dessa säkerhetskopior replikeras globalt för återhämtning mot regionala katastrofer. De automatiska säkerhetskopieringarna är användbara i scenarier när du av misstag tar bort eller uppdaterar ditt Azure Cosmos-konto, databas eller behållare och senare kräver dataåterställning.

## <a name="automatic-and-online-backups"></a>Automatiska säkerhetskopior och onlinesäkerhetskopior

Med Azure Cosmos DB är inte bara dina data utan även säkerhetskopior av dina data mycket överflödiga och motståndskraftiga mot regionala katastrofer. Följande steg visar hur Azure Cosmos DB utför säkerhetskopiering av data:

* Azure Cosmos DB tar automatiskt en säkerhetskopia av databasen var fjärde timme och vid valfritt tillfälle. Endast de senaste två säkerhetskopiorna lagras. Men om containern eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en specifik container eller databas i 30 dagar.

* Azure Cosmos DB lagrar dessa säkerhetskopior i Azure Blob-lagring medan de faktiska data finns lokalt i Azure Cosmos DB.

*  För att garantera låg latens lagras ögonblicksbilden av säkerhetskopian i Azure Blob-lagring i samma region som den aktuella skrivregionen (eller en av skrivregionerna, om du har en multi-master-konfiguration) för ditt Azure Cosmos-databaskonto. För återhämtning mot regional katastrof replikeras varje ögonblicksbild av säkerhetskopierade data i Azure Blob-lagring igen till en annan region via geo-redundant lagring (GRS). Den region som säkerhetskopian replikeras till baseras på källregionen och det regionala par som är associerat med källregionen. Mer information finns i [listan över geo-redundanta par i Azure-regioner](../best-practices-availability-paired-regions.md) artikeln. Du kan inte komma åt säkerhetskopian direkt. Azure Cosmos DB använder den här säkerhetskopian endast om en återställning av säkerhetskopiering initieras.

* Säkerhetskopieringarna tas utan att påverka programmets prestanda eller tillgänglighet. Azure Cosmos DB utför säkerhetskopiering av data i bakgrunden utan att förbruka ytterligare etablerat dataflöde (RU: er) eller påverkar databasens prestanda och tillgänglighet.

* Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhetskopiorna.

Följande avbildning visar hur en Azure Cosmos-behållare med alla tre primära fysiska partitioner i västra USA säkerhetskopieras i ett fjärrkonto för Azure Blob Storage i västra USA och sedan replikeras till östra USA:

![Periodiska fullständiga säkerhetskopieringar av alla Cosmos DB-enheter i GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Alternativ för att hantera dina egna säkerhetskopior

Med Azure Cosmos DB SQL API-konton kan du också underhålla dina egna säkerhetskopior med någon av följande metoder:

* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data med jämna mellanrum till en lagring som du väljer.

* Använd Azure Cosmos DB [ändra foder](change-feed.md) för att läsa data regelbundet för fullständiga säkerhetskopior, samt för inkrementella ändringar, och lagra den i din egen lagring.

## <a name="backup-retention-period"></a>Lagringsperiod för säkerhetskopiering

Azure Cosmos DB tar ögonblicksbilder av dina data var fjärde timme. Vid varje given tidpunkt behålls endast de två sista ögonblicksbilderna. Men om containern eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en specifik container eller databas i 30 dagar.

## <a name="restoring-data-from-online-backups"></a>Återställa data från onlinesäkerhetskopior

Oavsiktlig radering eller ändring av data kan inträffa i något av följande scenarier:  

* Hela Azure Cosmos-kontot tas bort

* En eller flera Azure Cosmos-databaser tas bort

* En eller flera Azure Cosmos-behållare tas bort

* Azure Cosmos-objekt (till exempel dokument) i en behållare tas bort eller ändras. Det här specifika fallet kallas vanligtvis för "datakorruption".

* En databas eller behållare för delat erbjudande i en databas med delat erbjudande tas bort eller skadas

Azure Cosmos DB kan återställa data i alla ovanstående scenarier. Återställningsprocessen skapar alltid ett nytt Azure Cosmos-konto som lagrar återställda data. Namnet på det nya kontot, om det inte `<Azure_Cosmos_account_original_name>-restored1`anges, kommer att ha formatet . Den sista siffran ökas om flera återställningar görs. Du kan inte återställa data till ett förskapad Azure Cosmos-konto.

När ett Azure Cosmos-konto tas bort kan vi återställa data till ett konto med samma namn, förutsatt att kontonamnet inte används. I sådana fall rekommenderas att kontot inte återskapas efter borttagningen, eftersom det inte bara hindrar återställda data att använda samma namn, utan också gör det svårare att upptäcka rätt konto för att återställa från svårare. 

När en Azure Cosmos-databas tas bort är det möjligt att återställa hela databasen eller en delmängd av behållarna i databasen. Det är också möjligt att välja behållare över databaser och återställa dem och alla återställda data placeras i ett nytt Azure Cosmos-konto.

När ett eller flera objekt i en behållare tas bort eller ändras av misstag (ärendet om skadade data) måste du ange vilken tid du ska återställa till. Tiden är av avgörande betydelse för det här fallet. Eftersom behållaren är live körs säkerhetskopieringen fortfarande, så om du väntar längre än kvarhållningsperioden (standardvärdet är åtta timmar) skrivs säkerhetskopieringarna över. När det gäller borttagningar lagras inte längre dina data eftersom de inte skrivs över av säkerhetskopieringscykeln. Säkerhetskopior för borttagna databaser eller behållare sparas i 30 dagar.

Om du etablerar dataflöde på databasnivå (det vill säga där en uppsättning behållare delar det etablerade dataflödet) sker säkerhetskopierings- och återställningsprocessen i det här fallet på hela databasnivå och inte på enskilda behållares nivå. I sådana fall är det inte ett alternativ att välja en delmängd av behållare som ska återställas.

## <a name="migrating-data-to-the-original-account"></a>Migrera data till det ursprungliga kontot

Det primära målet med dataåterställning är att tillhandahålla ett sätt att återställa data som du tar bort eller ändrar av misstag. Därför rekommenderar vi att du först inspekterar innehållet i de återställda data för att säkerställa att den innehåller vad du förväntar dig. Arbeta sedan med att migrera tillbaka data till det primära kontot. Även om det är möjligt att använda det återställda kontot som live-konto, är det inte ett rekommenderat alternativ om du har produktionsarbetsbelastningar.  

Följande är olika sätt att migrera data tillbaka till det ursprungliga Azure Cosmos-kontot:

* Använda [Cosmos DB Data Migration Tool](import-data.md)
* Använda [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Använda [ändringsflödet](change-feed.md) i Azure Cosmos DB 
* Skriv anpassad kod

Ta bort de återställda kontona så fort du är klar med migrering, eftersom de medför årliga avgifter.

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig mer om hur du återställer data från ett Azure Cosmos-konto eller lär dig hur du migrerar data till ett Azure Cosmos-konto

* Om du vill göra en återställningsbegäran kontaktar du Azure Support, [lämnar in en biljett från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Återställa data från ett Azure Cosmos-konto](how-to-backup-and-restore.md)
* [Använd Cosmos DB ändra foder](change-feed.md) för att flytta data till Azure Cosmos DB.
* [Använd Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data till Azure Cosmos DB.

