---
title: Azure Cosmos-DB som en nyckel/värde-lagring – kostnaden översikt | Microsoft Docs
description: Läs mer om den låga kostnaden för att använda Azure Cosmos DB som en nyckel/värde-arkivet.
keywords: värdet för nyckeln store
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: bcbe4afe5ca5abf9a709f5abbcdffa474c44702c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612130"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos-DB som en nyckel/värde-lagring – kostnaden översikt

Azure Cosmos DB är en global och flera olika modeller databastjänst för att skapa hög tillgänglighet, storskaliga program enkelt. Som standard indexerar Azure Cosmos DB automatiskt alla data som den en effektivt. Detta gör att snabb och konsekvent [SQL](sql-api-sql-query.md) (och [JavaScript](programming.md)) frågor för alla typer av data. 

Den här artikeln beskriver kostnaden för Azure Cosmos DB för att enkelt skriva och läsåtgärder när den används som en nyckel/värde-arkivet. Skriva operations inkluderar infogningar, ersätter, borttagningar och upserts av dokument. Förutom att garantera en 99,99% läsa tillgänglighets-SLA för alla enskild region och konton för alla flera regioner med Avslappnad konsekvens och 99,999% tillgänglighet för alla flera regioner databasen konton Azure Cosmos DB erbjudanden garanteras < 10 ms latens för läser och < 15 ms latens för (indexerade) skriver respektive vid 99th percentilen. 

## <a name="why-we-use-request-units-rus"></a>Varför vi använda begära enheter (RUs)

Azure DB Cosmos-prestanda baserat på mängden etablerade [begära enheter](request-units.md) (RU) för partitionen. Etablering på en andra kornighet och köps i RUs per sekund ([ska inte förväxlas med timvis fakturering](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs bör betraktas som en valuta som förenklar etableringen av nödvändiga genomströmning för programmet. Våra kunder behöver inte tänka på att skilja läsa och skriva kapacitetsenheter. Den enda valuta modellen RUs skapar effektivitet om du vill dela etablerad kapacitet mellan läsningar och skrivningar. Den här modellen etablerad kapacitet gör att tjänsten ger en förutsägbar och konsekvent genomströmning garanteras låg latens och hög tillgänglighet. Slutligen vi använder RU att modellen dataflöde, men varje etablerade RU har även en angiven mängd resurser (minne, Core). RU/s är inte bara IOPS.

Som ett globalt distribuerad databassystem är Cosmos DB endast Azure-tjänst som tillhandahåller ett serviceavtal på svarstid och genomströmning konsekvenskontroll förutom hög tillgänglighet. Genomströmning du etablera tillämpas på var och en av de regioner som är kopplad till ditt konto för Cosmos-DB-databasen. För läsåtgärder Cosmos DB erbjuder flera väldefinierade [konsekvensnivåer](consistency-levels.md) som du kan välja från. 

I följande tabell visas antalet RUs krävs för att utföra läsa och skriva transaktioner som är baserade på dokumentstorlek på 1 KB och 100 KBs.

|Objektets storlek|1 Läs|1 skrivning|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Kostnaden för läsning och skrivning

Om du etablerar 1 000 kostnaden RU/sek, detta motsvarar 3,6 m RU per timme och kommer $0.08 för timme (i USA och Europa). För ett dokument på 1 KB storlek, innebär detta att du kan använda 3,6 m läser eller skriver 0.72-m (3,6 m RU / 5) med hjälp av din dataflöde. Normaliserade till miljoner läsningar och skrivningar, kostnaden är $0,022 /m läsningar ($0.08 / 3,6) och $0.111/ m skriver ($0.08 / 0.72). Kostnaden per miljoner blir minimal som visas i tabellen nedan.

|Objektets storlek|1-m Läs|1-m skrivning|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


De flesta av grundläggande blob eller objektet lagrar services kostnad $0,40 per miljoner lästransaktioner och $5 per transaktion miljoner skrivåtgärder. Om används optimalt vara Cosmos DB 98% billigare än dessa lösningar (för 1 KB transaktioner).

## <a name="next-steps"></a>Nästa steg

Håll ögonen öppna för nya artiklar om hur du optimerar Azure Cosmos DB resursetablering. Under tiden kan passa på att använda våra [RU Kalkylatorn](https://www.documentdb.com/capacityplanner).

