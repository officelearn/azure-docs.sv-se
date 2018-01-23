---
title: "Online säkerhetskopiering och återställning med Azure Cosmos DB | Microsoft Docs"
description: "Lär dig mer om att utföra automatisk säkerhetskopiering och återställning i en Azure Cosmos-DB-databas."
keywords: "säkerhetskopiering och återställning, säkerhetskopiering online"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: f88bdd6ffb70ccd2aa48453747964c4afb5bea46
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatisk online säkerhetskopiering och återställning med Azure Cosmos DB
Azure Cosmos-DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Automatisk säkerhetskopiering tas utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Alla säkerhetskopior lagras separat i en annan storage-tjänst och dessa säkerhetskopieringar replikeras globalt för återhämtning mot regionala katastrofer. Automatisk säkerhetskopiering är avsedda för situationer när du av misstag tar bort Cosmos-DB-behållaren och senare behöver återställa data eller en lösning för katastrofåterställning.  

Den här artikeln börjar med en snabb sammanfattning dataredundans och tillgänglighet i Cosmos-DB och beskriver säkerhetskopieringar. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Hög tillgänglighet med Cosmos-DB - en sammanfattning
Cosmos DB är avsedd att vara [globalt distribuerade](distribute-data-globally.md) – du kan skala genomströmning över flera Azure-regioner tillsammans med principen som drivs redundans och transparent flera API: er. Azure Cosmos-DB erbjuder [99,99% tillgänglighet SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) för alla enskild region och konton för alla flera region med en avvikelse konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen. Alla skrivningar i Azure Cosmos DB genomförs varaktigt till lokala diskar genom att ett kvorum av replikerna i ett lokalt Datacenter innan bekräftades till klienten. Observera att den höga tillgängligheten för Cosmos DB förlitar sig på lokal lagring och inte är beroende av någon extern lagringstekniker. Dessutom, om ditt konto är kopplat till mer än en Azure-region, replikeras din skrivningar samt andra regioner. Om du vill skala din genomflöde och komma åt data på låg latens, kan du ha många läsa regioner som är kopplad till ditt konto som du vill. I varje skrivskyddade region sparas varaktigt (replikerade) data i en replik.  

Enligt beskrivningen i följande diagram, en enskild Cosmos-DB-behållare är [vågrätt partitionerade](partition-data.md). En ”partition” markeras med en cirkel i följande diagram och varje partition görs högtillgänglig via en replik. Det här är den lokala distributionsplatsen i ett enda Azure-region (betecknas med X-axeln). Dessutom kan distribueras varje partition (med dess motsvarande replikuppsättningen) sedan globalt över flera regioner som är kopplad till ditt konto (till exempel i den här bilden till tre regioner – östra USA, västra USA och centrala Indien). ”Ange partitionen” är ett globalt distribuerade entitet som består av flera kopior av dina data i varje region (betecknas med Y-axeln). Du kan tilldela prioritet till de regioner som är kopplad till ditt konto och Cosmos DB att transparent redundansväxla till nästa område händelse av katastrof. Du kan också manuellt simulerar redundans för att testa tillgängligheten för slutpunkt till slutpunkt för ditt program.  

Följande bild illustrerar hög grad av redundans med Cosmos DB.

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Hög grad av redundans med Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Fullständig, automatisk, online-säkerhetskopieringar
OJ, bort jag min behållare eller databasen! Med Cosmos DB, inte bara data, men säkerhetskopior av dina data görs också mycket överflödiga och flexibel till regionala katastrofer. Dessa säkerhetskopior av automatiserad tas för närvarande cirka var fjärde timme och lagras senaste 2 säkerhetskopior vid alla tidpunkter. Om data är skadad eller av misstag har släppts ta [kontakta Azure-supporten](https://azure.microsoft.com/support/options/) inom åtta timmar. 

Säkerhetskopieringar tas utan att påverka prestanda eller tillgänglighet för dina databasåtgärder. Cosmos DB tar säkerhetskopian i bakgrunden utan att använda din etablerade RUs eller påverka prestandan och utan att påverka tillgängligheten för din databas. 

Till skillnad från dina data som lagras i Cosmos DB lagras automatiska säkerhetskopieringar i Azure Blob Storage-tjänst. Om du vill garantera låg latens/effektiv överföring överförs ögonblicksbilden av säkerhetskopian till en instans av Azure Blob storage i samma region som det aktuella skrivning den regionen där din Cosmos-DB-databaskonto. För återhämtning mot regionala replikeras igen varje ögonblicksbild av dina säkerhetskopierade data i Azure Blob Storage via geo-redundant lagring (GRS) till en annan region. Följande diagram visar att hela Cosmos-DB-behållaren (med alla tre primära partitioner i västra USA i det här exemplet) säkerhetskopieras i en fjärransluten Azure Blob Storage-konto i USA, västra och sedan GRS replikeras till östra USA. 

Följande bild illustrerar periodiska fullständiga säkerhetskopieringar för alla Cosmos-DB-enheter i GRS Azure Storage.

![Periodiska fullständiga säkerhetskopieringar för alla Cosmos-DB-enheter i Azure Storage för GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Period för lagring av säkerhetskopior.
Enligt beskrivningen ovan tar Azure Cosmos DB ögonblicksbilder av data var fjärde timme på nivån partition. Endast de två sista ögonblicksbilderna bevaras vid en given tidpunkt. Om insamlingsdatabasen raderas behålla vi befintliga ögonblicksbilder för alla borttagna partitioner inom den angivna insamlingsdatabasen för 30 dagar.

Om du vill att underhålla din egen ögonblicksbilder, du kan använda export till JSON-alternativet i Azure Cosmos DB [datamigreringsverktyget](import-data.md#export-to-json-file) att schemalägga ytterligare säkerhetskopieringar.

## <a name="restoring-a-database-from-an-online-backup"></a>Återställa en databas från en onlinesäkerhetskopiering
Om du av misstag tar bort den databas eller en samling kan du [filen ett supportärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [kontakta Azure support](https://azure.microsoft.com/support/options/) att återställa data från den senaste automatiska säkerhetskopieringen. Om du behöver återställa databasen på grund av data felärende (inklusive fall där dokument i en samling tas bort), se [hantering datafel](#handling-data-corruption) som du måste vidta ytterligare åtgärder för att förhindra skadade data skriver över befintliga säkerhetskopior. För en specifik ögonblicksbild av säkerhetskopian ska återställas kräver Cosmos DB att data var tillgängliga under säkerhetskopiering för den ögonblicksbilden.

## <a name="handling-data-corruption"></a>Hantering av skadade data
Azure Cosmos-DB behåller senaste två säkerhetskopieringar för varje partition i databaskontot. Den här modellen fungerar bra när en behållare (insamling av dokument, diagram, tabell) eller en databas av misstag tas bort eftersom en av de senaste versionerna kan återställas. Men i fall när ett problem med skadade data kan innebära att användare, Azure Cosmos DB kan vara ovetande om skadade data, och det är möjligt att skadan kan ha över befintliga säkerhetskopior. Så snart skadade identifieras användaren bör ta bort behållaren skadat (tabell-samling/diagram) så att säkerhetskopieringar skyddas från att skrivas över med skadade data.

## <a name="next-steps"></a>Nästa steg

Om du vill replikera databasen i flera datacenter, se [distribuera dina data globalt med Cosmos DB](distribute-data-globally.md). 

Filen kontakta Azure-Support, [filen en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

