---
title: Online säkerhetskopiering och återställning med Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om att utföra automatisk säkerhetskopiering och återställning i en Azure Cosmos-DB-databas.
keywords: säkerhetskopiering och återställning, säkerhetskopiering online
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 19f61893eb9250fbd5bbf930e98aa89ac74fd0c3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028744"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatisk online säkerhetskopiering och återställning med Azure Cosmos DB
Azure Cosmos-DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Automatisk säkerhetskopiering tas utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Alla säkerhetskopior lagras separat i en annan storage-tjänst och dessa säkerhetskopieringar replikeras globalt för återhämtning mot regionala katastrofer. Automatisk säkerhetskopiering är avsedda för situationer när du av misstag tar bort Cosmos-DB-behållaren och senare behöver återställa data eller en lösning för katastrofåterställning.  

Den här artikeln börjar med en snabb sammanfattning dataredundans och tillgänglighet i Cosmos-DB och beskriver säkerhetskopieringar. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Hög tillgänglighet med Cosmos-DB - en sammanfattning
Cosmos DB är avsedd att vara [globalt distribuerade](distribute-data-globally.md) – du kan skala genomströmning över flera Azure-regioner tillsammans med principen som drivs redundans och transparent flera API: er. Azure Cosmos-DB erbjuder [99,99% tillgänglighet SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) för alla enskild region och konton för alla flera region med en avvikelse konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen. Alla skrivningar i Azure Cosmos DB genomförs varaktigt till lokala diskar genom att ett kvorum av replikerna i ett lokalt Datacenter innan bekräftades till klienten. Hög tillgänglighet för Cosmos DB förlitar sig på lokal lagring och är beroende inte av någon extern lagringstekniker. Dessutom, om ditt konto är kopplat till mer än en Azure-region, replikeras din skrivningar samt andra regioner. Om du vill skala din genomflöde och komma åt data på låg latens, kan du ha många läsa regioner som är kopplad till ditt konto som du vill. I varje skrivskyddade region sparas varaktigt (replikerade) data i en replik.  

Enligt beskrivningen i följande diagram, en enskild Cosmos-DB-behållare är [vågrätt partitionerade](partition-data.md). En ”partition” markeras med en cirkel i följande diagram och varje partition görs högtillgänglig via en replik. Det här är den lokala distributionsplatsen i ett enda Azure-region (betecknas med X-axeln). Dessutom kan distribueras varje partition (med dess motsvarande replikuppsättningen) sedan globalt över flera regioner som är kopplad till ditt konto (till exempel i den här bilden till tre regioner – östra USA, västra USA och centrala Indien). ”Ange partitionen” är ett globalt distribuerade entitet som består av flera kopior av dina data i varje region (betecknas med Y-axeln). Du kan tilldela prioritet till de regioner som är kopplad till ditt konto och transparent Cosmos DB växlar till nästa region händelse av katastrof. Du kan också manuellt simulerar redundans för att testa tillgängligheten för slutpunkt till slutpunkt för ditt program.  

Följande bild illustrerar hög grad av redundans med Cosmos DB.

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Fullständig, automatisk, online-säkerhetskopieringar
OJ, bort jag min behållare eller databasen! Med Cosmos DB, inte bara data, men säkerhetskopior av dina data görs också mycket överflödiga och flexibel till regionala katastrofer. Dessa säkerhetskopior av automatiserad tas för närvarande cirka var fjärde timme och lagras senaste två säkerhetskopior vid alla tidpunkter. Om data är skadad eller av misstag har släppts Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom åtta timmar. 

Säkerhetskopieringar tas utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Cosmos DB tar säkerhetskopian i bakgrunden utan att använda din etablerade RUs eller påverka prestandan och utan att påverka tillgängligheten för din databas. 

Till skillnad från dina data som lagras i Cosmos DB lagras automatiska säkerhetskopieringar i Azure Blob Storage-tjänst. Om du vill garantera låg latens/effektiv överföring överförs ögonblicksbilden av säkerhetskopian till en instans av Azure Blob storage i samma region som det aktuella skrivning den regionen där din Cosmos-DB-databaskonto. För återhämtning mot regionala replikeras igen varje ögonblicksbild av dina säkerhetskopierade data i Azure Blob Storage via geo-redundant lagring (GRS) till en annan region. Följande diagram visar att hela Cosmos-DB-behållaren (med alla tre primära partitioner i västra USA i det här exemplet) säkerhetskopieras i en fjärransluten Azure Blob Storage-konto i USA, västra och sedan GRS replikeras till östra USA. 

Följande bild illustrerar periodiska fullständiga säkerhetskopieringar för alla Cosmos-DB-enheter i GRS Azure Storage.

![Periodiska fullständiga säkerhetskopieringar för alla Cosmos-DB-enheter i Azure Storage för GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Period för lagring av säkerhetskopior.
Enligt beskrivningen ovan tar Azure Cosmos DB ögonblicksbilder av data var fjärde timme på nivån partition. Endast de två sista ögonblicksbilderna bevaras vid en given tidpunkt. Men om insamlingsdatabasen raderas behåller Azure Cosmos DB befintliga ögonblicksbilder för alla borttagna partitioner inom den angivna insamlingsdatabasen i 30 dagar.

För SQL-API, om du vill behålla dina egna ögonblicksbilder, du kan använda export till JSON-alternativet i Azure Cosmos-DB [datamigreringsverktyget](import-data.md#export-to-json-file) att schemalägga ytterligare säkerhetskopieringar.

> [!NOTE]
> Om du ”etablera genomströmning för en uppsättning behållare på databasnivå –” Kom ihåg att sker återställningen på fullständig kontonivå för databasen. Du måste också se till att nå ut inom 8 timmar till supportteamet om du av misstag tar bort din behållare - diagram-samling/tabell om du använder den här nya funktionen. 


## <a name="restoring-a-database-from-an-online-backup"></a>Återställa en databas från en onlinesäkerhetskopiering

Om du av misstag tar bort den databas eller en samling kan du [filen ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [kontakta Azure support](https://azure.microsoft.com/support/options/) att återställa data från den senaste automatiska säkerhetskopieringen. Azure-supporten är tillgänglig för valda planer endast som Standard, utvecklare kan stöd är inte tillgänglig med grundläggande planen. Mer information om olika supportplaner, se [Azure-supportplaner](https://azure.microsoft.com/support/plans/) sidan. 

Om du behöver återställa databasen på grund av data felärende (inklusive fall där dokument i en samling tas bort), se [hantering datafel](#handling-data-corruption) som du måste vidta ytterligare åtgärder för att förhindra skadade data skriver över befintliga säkerhetskopior. För en specifik ögonblicksbild av säkerhetskopian ska återställas kräver Cosmos DB att data var tillgängliga under säkerhetskopiering för den ögonblicksbilden.

## <a name="handling-data-corruption"></a>Hantering av skadade data

Azure Cosmos-DB behåller senaste två säkerhetskopieringar för varje partition i databaskontot. Den här modellen fungerar bra när en behållare (insamling av dokument, diagram, tabell) eller en databas av misstag tas bort eftersom en av de senaste versionerna kan återställas. Men i fall när ett problem med skadade data kan innebära att användare, Azure Cosmos DB kan vara ovetande om skadade data, och det är möjligt att skadan kan ha över befintliga säkerhetskopior. 

Så snart skadad är, nå ut till kundsupport med information och samling med ungefärliga tid om felet. En annan åtgärd som användaren kan göra i händelse av skadad (data borttagning/uppdatering) användaren bör ta bort behållaren skadat (tabell-samling/diagram) så att säkerhetskopieringar skyddas från att skrivas över med skadade data.  

Följande bild illustrerar stöder begäran skapande för container(collection/graph/table) återställning via Azure portal för oavsiktlig borttagning eller uppdatering av data i en behållare

![Återställa en samling för felaktig uppdatera eller ta bort data i Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

När återställningen är klar för den här typen av scenarier - data har återställts till ett annat konto (med suffixet ”-återställts”) och samling. Den här återställningen görs inte för att ge möjlighet till kunden för att göra validering av data och flytta data vid behov. Återställda samlingen är i samma region med samma RUs och principer för indexering. 

## <a name="next-steps"></a>Nästa steg

Om du vill replikera databasen i flera datacenter, se [distribuera dina data globalt med Cosmos DB](distribute-data-globally.md). 

Filen kontakta Azure-Support, [filen en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

