---
title: Online-säkerhetskopiering och återställning med Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om att utföra automatisk säkerhetskopiering och återställning på en Azure Cosmos DB-databas.
keywords: säkerhetskopiering och återställning, säkerhetskopiering online
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: cf4579705e5910f62ca07223cb16405140926119
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859208"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatisk online säkerhetskopiering och återställning med Azure Cosmos DB
Azure Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Automatisk säkerhetskopiering är hämtade utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Alla säkerhetskopior lagras separat i en annan lagringstjänst och säkerhetskopieringarna replikeras globalt för återhämtning mot regionala problem. Automatisk säkerhetskopiering är avsett för scenarion med när du av misstag tar bort din Cosmos DB-behållare och senare kräver återställning av data eller en lösning för haveriberedskap.  

Den här artikeln börjar med en snabb sammanfattning av dataredundans och tillgänglighet i Cosmos DB och beskriver säkerhetskopieringar. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Hög tillgänglighet med Cosmos DB – en sammanfattning
Cosmos DB är avsett att vara [globalt distribuerade](distribute-data-globally.md) – du kan skala dataflöde i flera Azure-regioner tillsammans med principen driven redundans och transparent flera API: er. Azure Cosmos DB erbjuder [tillgänglighet på 99,99% serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) för alla och alla konton i flera regioner med restriktiva konsekvens och 99,999% läsningstillgänglighet för alla databaskonton för flera regioner konton. Alla skrivningar i Azure Cosmos DB är varaktigt strävar till lokala diskar genom att ett kvorum av kopior på ett lokalt Datacenter innan uppmärksammades till klienten. Tillgängligheten för Cosmos DB är beroende av lokal lagring och är beroende inte av några externa lagringstekniker. Dessutom kan replikeras ditt databaskonto är associerad med fler än en Azure-region, dina skrivåtgärder i andra regioner samt. Om du vill skala ditt dataflöde och komma åt data på låg fördröjning, kan du ha många Läs regioner som associeras med ditt databaskonto du vill. I varje läsregionen bevaras varaktigt (replikerade) data mellan en replikuppsättning.  

Enligt beskrivningen i följande diagram, en enda Cosmos DB-behållare är [vågrätt partitionerad](partition-data.md). En ”partition” markeras med en cirkel i följande diagram, och varje partition görs med hög tillgänglighet via en replikuppsättning. Det här är den lokala distributionsplatsen i en enda Azure-region (betecknas med X-axeln). Dessutom kan distribueras varje partition (med dess motsvarande replikuppsättningen) sedan globalt över flera regioner som associeras med ditt databaskonto (till exempel i den här bilden tre regioner – östra USA, västra USA och centrala Indien). ”Partitionsuppsättning” är en globalt distribuerad entitet består av flera kopior av dina data i varje region (betecknas med Y-axeln). Du kan tilldela prioritet till de regioner som associeras med ditt databaskonto och Cosmos DB kommer transparent sätt växla över till nästa region händelse av katastrof. Du kan också manuellt simulerar redundans för att testa tillgängligheten slutpunkt till slutpunkt för ditt program.  

Följande bild illustrerar hög grad av redundans med Cosmos DB.

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Fullständiga, automatisk, online säkerhetskopieringar
Hoppsan, bort jag min behållare eller databasen! Med Cosmos DB, inte bara dina data, men säkerhetskopior av dina data görs också med hög redundant och flexibel till regionala katastrofer. Dessa automatiska säkerhetskopior tas för närvarande cirka var fjärde timme och senaste två säkerhetskopior lagras vid alla tidpunkter. Om data tas bort av misstag eller vara skadade, kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom åtta timmar. 

Säkerhetskopieringar kommer utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Cosmos DB tar säkerhetskopian i bakgrunden utan att använda ditt etablerade ru: er eller påverkar prestanda och utan att påverka tillgängligheten för din databas. 

Till skillnad från dina data som lagras i Cosmos DB, lagras de automatiska säkerhetskopiorna i Azure Blob Storage-tjänsten. För att garantera med låg latens/effektiv överföringen överförs ögonblicksbilden av säkerhetskopian till en instans av Azure Blob storage i samma region som den aktuella skrivregionen av ditt Cosmos DB-databaskonto. Skydd mot regionalt haveri replikeras igen varje ögonblicksbild av dina säkerhetskopierade data i Azure Blob Storage via geo-redundant lagring (GRS) till en annan region. Följande diagram visar att hela Cosmos DB-behållare (med alla tre primära partitioner i västra USA, i det här exemplet) har säkerhetskopierats i ett remote Azure Blob Storage-konto i västra USA och sedan GRS replikeras till östra USA. 

Följande bild illustrerar regelbundna fullständiga säkerhetskopieringar för alla Cosmos DB-entiteter i GRS Azure Storage.

![Regelbundna fullständiga säkerhetskopieringar för alla Cosmos DB-entiteter i GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Kvarhållningsperiod för säkerhetskopiering
Enligt beskrivningen ovan, tar Azure Cosmos DB ögonblicksbilder av dina data var fjärde timme på nivån partition. Endast de två sista ögonblicksbilderna bevaras vid en given tidpunkt. Men om behållare/databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder för alla borttagna partitioner inom den angivna behållaren/databasen i 30 dagar.

SQL-API: t, om du vill ha kvar din egen ögonblicksbilder, du kan använda export till JSON-alternativet i Azure Cosmos DB [datamigreringsverktyget](import-data.md#export-to-json-file) att schemalägga ytterligare säkerhetskopieringar.

> [!NOTE]
> Om du ”etablera dataflöde för en uppsättning behållare på databasnivå –” Kom ihåg sker återställningen på fullständig kontonivå för databasen. Du måste också se till att kontakta dig inom 8 timmar supportteamet om du råkar ta bort behållaren - samling/tabell/graph om du använder den här nya funktionen. 


## <a name="restoring-a-database-from-an-online-backup"></a>Återställa en databas från en onlinesäkerhetskopiering

Om du råkar ta bort din databas eller en behållare kan du [öppna ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [anropa Azure-supporten](https://azure.microsoft.com/support/options/) att återställa data från den senaste automatiska säkerhetskopieringen. Azure-support är tillgänglig för valda planer endast som Standard, utvecklare, support är inte tillgänglig med Basic-avtal. Läs mer om olika supportplaner, i [supportavtal](https://azure.microsoft.com/support/plans/) sidan. 

Om du vill återställa databasen på grund av data felärende (inklusive fall där dokument i en behållare tas bort), se [hantering av skadade data](#handling-data-corruption) eftersom du måste vidta ytterligare åtgärder för att förhindra skadade data skriver över befintliga säkerhetskopior. Cosmos DB kräver att data som var tillgängliga under hela säkerhetskopieringscykel för denna ögonblicksbild för en specifik ögonblicksbild av säkerhetskopian ska återställas.

## <a name="handling-data-corruption"></a>Hantering av skadade data

Azure Cosmos DB behåller de senaste två säkerhetskopiorna av varje partition i databaskontot. Den här modellen fungerar bra när en behållare (samling av dokument, diagram, tabell) eller en databas tas bort av misstag eftersom en av de senaste versionerna kan återställas. Men i fallet när användarna kan införa en data-felärende, Azure Cosmos DB kan vara ovetande om skadade data och det är möjligt att skadan kan ha över befintliga säkerhetskopior. 

När felaktiga data upptäcks, kontakta Kundsupport med information för databas konto och en behållare med ungefärliga tid om felet. En annan åtgärd som användaren kan göra i händelse av skadad (data tas bort/uppdatering) användaren bör ta bort den skadade behållaren (samling/diagram/tabell) så att säkerhetskopieringar skyddas från att skrivas över med skadade data.  

Följande bild illustrerar skapandet stöd för återställning av container(collection/graph/table) via Azure portal för oavsiktlig borttagning eller uppdatering av data i en behållare

![Återställa en behållare för felaktiga uppdatera eller ta bort data i Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

När återställningen är klar för den här typen av scenarier – återställa data till ett annat konto (med suffixet ”-återställts”) och en behållare. Den här återställningen görs inte på plats för att tillhandahålla en chans till kunder för att utföra verifiering av data och flytta data vid behov. Återställda behållaren är i samma region med samma ru: er och indexering principer. 

## <a name="next-steps"></a>Nästa steg

Om du vill replikera din databas i flera datacenter, se [distribuera dina data globalt med Cosmos DB](distribute-data-globally.md). 

Till filen kontakta Azure-supporten [lämna in en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

