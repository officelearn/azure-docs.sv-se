---
title: Automatisk, online-säkerhetskopiering och på begäran Återställ i Azure Cosmos DB
description: Den här artikeln beskrivs hur automatisk, online-säkerhetskopiering och på begäran återställer arbete i Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 0d6a370884e6648aaf131892759ee45b29ed3693
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001659"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Onlinesäkerhetskopiering och på begäran data Återställ i Azure Cosmos DB

Azure Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Automatisk säkerhetskopiering är hämtade utan att påverka prestanda eller tillgänglighet för databasåtgärderna. Alla säkerhetskopior lagras separat i en lagringstjänst och säkerhetskopieringarna replikeras globalt för återhämtning mot regionala problem. Automatisk säkerhetskopiering är användbara i situationer när du råkar ta bort eller uppdatera din Azure Cosmos-konto, databas eller behållare och senare behöver återställa data.

## <a name="automatic-and-online-backups"></a>Automatisk och online-säkerhetskopiering

Med Azure Cosmos DB är inte bara dina data, utan även säkerhetskopior av dina data med hög redundant och motståndskraftig mot regionala katastrofer. Automatiska säkerhetskopior tas för närvarande var fjärde timme och när som helst tid de senaste två säkerhetskopiorna lagras. Om du har eller förstörs dina data, bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom åtta timmar så att Azure Cosmos DB-teamet kan du återställa data från säkerhetskopior.

Säkerhetskopieringar kommer utan att påverka prestanda eller tillgänglighet för ditt program. Azure Cosmos DB utför säkerhetskopiering av data i bakgrunden utan att förbruka alla ytterligare etablerat dataflöde (ru) eller påverka prestanda och tillgänglighet för din databas.

Azure Cosmos DB lagrar automatiska säkerhetskopieringar i Azure Blob Storage, medan de faktiska data som finns lokalt i Azure Cosmos DB. För att garantera låg latens, ögonblicksbilden av säkerhetskopian lagras i Azure Blob storage i samma region som den aktuella skrivregionen (eller en av Skriv-regioner, om du har en konfiguration med flera huvudservrar) i din Cosmos DB-databaskonto. Skydd mot regionalt haveri replikeras igen varje ögonblicksbild av data i Azure Blob storage till en annan region genom geo-redundant lagring (GRS). Den region som säkerhetskopieringen replikeras baseras på din region för källa och regionala par som är associerade med källregionen. Mer information finns i den [lista med geo-redundant par med Azure-regioner](../best-practices-availability-paired-regions.md) artikeln. Du kan inte komma åt den här säkerhetskopieringen direkt. Azure Cosmos DB använder den här säkerhetskopian endast om en säkerhetskopiering återställning initieras.
Följande bild visar hur en Azure Cosmos-behållare med alla tre primära fysiska partitioner i västra USA säkerhetskopieras i en fjärransluten Azure Blob Storage-konto i västra USA och sedan replikeras till östra USA:

![Regelbundna fullständiga säkerhetskopieringar för alla Cosmos DB-entiteter i GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Alternativ för att hantera dina egna säkerhetskopior

Med Azure Cosmos DB SQL API-konton kan ha du också egna säkerhetskopior med hjälp av något av följande metoder:

* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) att flytta data med jämna mellanrum till ett lagringsutrymme med ditt val.

* Använd Azure Cosmos DB [ändringsflödet](change-feed.md) att läsa data med jämna mellanrum för fullständiga säkerhetskopieringar, samt för inkrementella ändringar och lagra den i din egen lagring.

## <a name="backup-retention-period"></a>Kvarhållningsperiod för säkerhetskopiering

Azure Cosmos DB tar ögonblicksbilder av dina data var fjärde timme. Endast de två sista ögonblicksbilderna bevaras vid en given tidpunkt. Men om behållare eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en viss behållare eller databasen i 30 dagar.

## <a name="restoring-data-from-online-backups"></a>Återställa data från säkerhetskopior online

Oavsiktlig borttagning eller ändringar av data kan bero på något av följande scenarier:  

* Hela Azure Cosmos-kontot har tagits bort

* En eller flera Azure-Cosmos-databaser har tagits bort

* En eller flera Azure-Cosmos-behållare har tagits bort

* Azure Cosmos-objekt (till exempel dokument) i en behållare som ska tas bort eller ändras. Den här specifika fall kallas vanligtvis ”skadade”.

* En delad erbjudande-databas eller behållare i en delad erbjudandet databas tas bort eller skadad

Azure Cosmos DB kan återställa data i alla scenarier ovan. Återställningsprocessen skapas alltid ett nytt Azure Cosmos-konto för att lagra dessa data. Namnet på det nya kontot, om inte anges har formatet `<Azure_Cosmos_account_original_name>-restored1`. Den sista siffran ökas om flera återställningar kan utföras. Du kan inte återställa data till ett Azure Cosmos-konto som skapats i förväg.

När en Azure Cosmos-kontot har tagits bort, kan vi återställa data på ett konto med samma namn, förutsatt att kontonamnet inte används. I sådana fall kan rekommenderar vi att du inte återskapa kontot efter borttagningen, eftersom den inte bara förhindrar att återställda data om du vill använda samma namn men gör också identifiera rätt typ av konto att återställa från svårare. 

När en Azure Cosmos-databas tas bort, är det möjligt att återställa hela databasen eller en delmängd av behållarna i databasen. Du kan också markera behållare på databaser och återställa dem och alla dessa data placeras i ett nytt Azure Cosmos-konto.

När ett eller flera objekt i en behållare av misstag tas bort eller ändras (data skadas fall), kommer du behöva ange hur lång tid att återställa till. Tiden är i grunden för det här fallet. Eftersom behållaren är aktiv, körs fortfarande säkerhetskopieringen, så om du vill vänta längre än kvarhållningsperioden (standardvärdet är åtta timmar) säkerhetskopieringar kommer att skrivas över. Dina data lagras inte längre när det gäller borttagningar, eftersom de inte åsidosätts av säkerhetskopiering cykeln. Säkerhetskopieringar för borttagna databaser eller behållare sparas i 30 dagar.

Om du etablerar dataflöde på databasnivå (det vill säga där en uppsättning behållare delar det etablerade dataflödet), säkerhetskopiering och återställning i det här fallet inträffa på nivån hela databasen och inte på nivån för enskilda behållare. I sådana fall kan är välja en delmängd av behållare för att återställa inte ett alternativ.

## <a name="migrating-data-to-the-original-account"></a>Migrera data till det ursprungliga kontot

Huvudsyftet med data för återställning är att tillhandahålla ett sätt att återställa alla data som du tar bort eller ändra av misstag. Därför rekommenderar vi att du först granska innehållet i den återställda data att se till att den innehåller vad du förväntade dig. Arbeta sedan om hur du migrerar data tillbaka till det primära kontot. Även om det är möjligt att använda kontot återställda som live-konto, är det inte en rekommenderade alternativet om du har arbetsbelastningar i produktion.  

Här följer några olika sätt att migrera data tillbaka till det ursprungliga Azure Cosmos-kontot:

* Med hjälp av [Cosmos DB-Datamigreringsverktyg](import-data.md)
* Med hjälp av [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Med hjälp av [ändringsflödet](change-feed.md) i Azure Cosmos DB 
* Skriva anpassad kod

Ta bort den återställda användarkonton när du är klar migrera, eftersom de kommer att medföra löpande kostnader.

## <a name="next-steps"></a>Nästa steg

Sedan du kan lära dig om hur du återställer data från ett Azure Cosmos-konto eller Lär dig hur du migrerar data till ett Azure Cosmos-konto

* Att göra en återställning för begäran, kontakta Azure-supporten [lämna in en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Så här återställer du data från ett Azure Cosmos-konto](how-to-backup-and-restore.md)
* [Använd Cosmos DB-ändringsflödet](change-feed.md) att flytta data till Azure Cosmos DB.
* [Använda Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) att flytta data till Azure Cosmos DB.

