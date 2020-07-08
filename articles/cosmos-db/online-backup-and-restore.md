---
title: Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB
description: Den här artikeln beskriver hur automatisk säkerhets kopiering online och data återställning på begäran fungerar i Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8ed9e23b178b8eeefbd3c3a690491124e6901180
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112930"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB

Azure Cosmos DB automatiskt tar säkerhets kopior av dina data med jämna mellanrum. Automatiska säkerhets kopieringar tas utan att påverka databas åtgärdernas prestanda eller tillgänglighet. Alla säkerhets kopior lagras separat i en lagrings tjänst och de säkerhets kopiorna replikeras globalt för återhämtning mot regionala haverier. Automatiska säkerhets kopieringar är användbara i scenarier när du oavsiktligt tar bort eller uppdaterar ditt Azure Cosmos-konto, din databas eller behållare och senare behöver data återställningen.

## <a name="automatic-and-online-backups"></a>Automatisk säkerhets kopiering och onlinesäkerhetskopiering

Med Azure Cosmos DB, inte bara för dina data, utan även säkerhets kopior av dina data är mycket redundanta och elastiska för regional katastrofer. Följande steg visar hur Azure Cosmos DB utför säkerhets kopiering av data:

* Azure Cosmos DB tar automatiskt en säkerhetskopia av databasen var fjärde timme och vid valfritt tillfälle. Endast de senaste två säkerhetskopiorna lagras. Men om containern eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en specifik container eller databas i 30 dagar.

* Azure Cosmos DB lagrar dessa säkerhets kopior i Azure Blob Storage, medan faktiska data finns lokalt inom Azure Cosmos DB.

*  För att garantera låg latens lagras ögonblicks bilden av säkerhets kopian i Azure Blob Storage i samma region som den aktuella Skriv regionen (eller någon av de skrivskyddade regionerna, om du har en konfiguration med flera huvud) för ditt Azure Cosmos Database-konto. För återhämtning mot regional katastrof replikeras varje ögonblicks bild av säkerhets kopierings data i Azure Blob Storage igen till en annan region via Geo-redundant lagring (GRS). Den region som säkerhets kopian replikeras till baseras på käll regionen och det regionala paret som är associerat med käll regionen. Mer information finns i [listan över geo-redundanta par av Azure-regioner](../best-practices-availability-paired-regions.md) . Du kan inte komma åt den här säkerhets kopian direkt. Azure Cosmos DB använder endast den här säkerhets kopian om en återställning av säkerhets kopia initieras.

* Säkerhets kopiorna tas utan att påverka programmets prestanda eller tillgänglighet. Azure Cosmos DB utför säkerhets kopiering av data i bakgrunden utan att behöva använda ytterligare allokerat data flöde (ru: er) eller påverka databasens prestanda och tillgänglighet.

* Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhets kopiorna.

Följande bild visar hur en Azure Cosmos-behållare med alla de tre primära fysiska partitionerna i västra USA säkerhets kopie ras i ett fjärran slutet Azure Blob Storage-konto i USA och sedan replikeras till USA, östra:

:::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Regelbundna fullständiga säkerhets kopieringar av alla Cosmos DB entiteter i GRS Azure Storage" border="false":::

## <a name="options-to-manage-your-own-backups"></a>Alternativ för att hantera dina egna säkerhets kopior

Med Azure Cosmos DB SQL API-konton kan du även underhålla dina egna säkerhets kopior genom att använda någon av följande metoder:

* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data regelbundet till valfri lagrings plats.

* Använd Azure Cosmos DB [ändra feed](change-feed.md) för att läsa data regelbundet för fullständiga säkerhets kopieringar, samt för stegvisa ändringar och lagra dem i ditt eget lagrings utrymme.

## <a name="backup-retention-period"></a>Kvarhållningsperiod för säkerhets kopior

Azure Cosmos DB tar ögonblicks bilder av dina data var fjärde timme. Vid en specifik tidpunkt behålls bara de sista två ögonblicks bilderna. Men om containern eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en specifik container eller databas i 30 dagar.

## <a name="restoring-data-from-online-backups"></a>Återställa data från säkerhets kopiering online

Oavsiktlig borttagning eller ändring av data kan ske i något av följande scenarier:  

* Hela Azure Cosmos-kontot tas bort

* En eller flera Azure Cosmos-databaser tas bort

* En eller flera Azure Cosmos-behållare har tagits bort

* Azure Cosmos-objekt (till exempel dokument) i en behållare tas bort eller ändras. Detta specialfall kallas vanligt vis "skadade data".

* En delad erbjudande databas eller behållare i en delad erbjudande databas tas bort eller skadas

Azure Cosmos DB kan återställa data i alla scenarier ovan. Återställningsprocessen skapar alltid ett nytt Azure Cosmos-konto som lagrar återställda data. Namnet på det nya kontot, om det inte anges, kommer att ha formatet `<Azure_Cosmos_account_original_name>-restored1` . Den sista siffran ökas, om flera försök görs. Du kan inte återställa data till ett redan skapat Azure Cosmos-konto.

När ett Azure Cosmos-konto tas bort kan vi återställa data till ett konto med samma namn, förutsatt att konto namnet inte används. I sådana fall rekommenderar vi att du inte återskapar kontot efter borttagningen, eftersom det inte bara förhindrar att de återställda data använder samma namn, men det gör också att identifiera rätt konto för återställning från svårare. 

När en Azure Cosmos-databas tas bort är det möjligt att återställa hela databasen eller en delmängd av behållarna i databasen. Du kan också välja behållare mellan databaser och återställa dem och alla återställda data placeras i ett nytt Azure Cosmos-konto.

När ett eller flera objekt i en behållare oavsiktligt har tagits bort eller ändrats (skadade data) måste du ange hur lång tid som ska återställas. Tiden för det här fallet är grunden. Eftersom behållaren är Live körs säkerhets kopieringen fortfarande, så om du väntar bortom kvarhållningsperioden (Standardvärdet är åtta timmar) kommer säkerhets kopiorna att skrivas över. När det gäller borttagningar lagras inte dina data längre eftersom de inte skrivs över av säkerhets kopierings cykeln. Säkerhets kopior för borttagna databaser eller behållare sparas i 30 dagar.

Om du etablerar data flöde på databas nivå (det vill säga där en uppsättning behållare delar det etablerade data flödet), sker säkerhets kopieringen och återställnings processen i det här fallet på hela databas nivån och inte på de enskilda behållar nivåerna. I sådana fall är det inte något alternativ att välja en delmängd behållare som ska återställas.

## <a name="migrating-data-to-the-original-account"></a>Migrera data till det ursprungliga kontot

Det primära målet för data återställningen är att tillhandahålla ett sätt att återställa data som du tar bort eller ändrar av misstag. Vi rekommenderar därför att du först kontrollerar innehållet i de återställda data för att se till att det innehåller det du förväntar dig. Arbeta sedan med att migrera data tillbaka till det primära kontot. Även om det är möjligt att använda det återställda kontot som ett Live-konto, är det inte ett rekommenderat alternativ om du har produktions arbets belastningar.  

Följande är olika sätt att migrera data tillbaka till det ursprungliga Azure Cosmos-kontot:

* Använda [verktyget för migrering av Cosmos db data](import-data.md)
* Använda [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Använda [ändrings flöde](change-feed.md) i Azure Cosmos DB 
* Skriv anpassad kod

Ta bort de återställda kontona så snart du har migrerat, eftersom de kommer att ådra sig kontinuerliga kostnader.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du lära dig hur du återställer data från ett Azure Cosmos-konto eller Lär dig hur du migrerar data till ett Azure Cosmos-konto

* Om du vill göra en återställnings förfrågan kontaktar du Azure-supporten, [File a Ticket från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Så här återställer du data från ett Azure Cosmos-konto](how-to-backup-and-restore.md)
* [Använd Cosmos DB ändra feed](change-feed.md) för att flytta data till Azure Cosmos dB.
* [Använd Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta Data till Azure Cosmos dB.

