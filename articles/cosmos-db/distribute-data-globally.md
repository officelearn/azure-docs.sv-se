---
title: Distribuera data globalt med Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om global skala geo-replikering, multimaster, redundans och data med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408903"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Globala data-distribution med Azure Cosmos DB

Azure är allt vanligare – den har ett globala fotavtryck över 50 + geografiska områden och utökas ständigt. Med dess global närvaro och stöd för flera huvudservrar är en differentierad funktioner som Azure erbjuder till dess utvecklare möjlighet att bygga, distribuera och hantera enkelt globalt distribuerade program.

[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB tillhandahåller nyckelfärdig global distribution, [elastisk skalning av dataflöde och lagring](../cosmos-db/partition-data.md) världen över, ensiffrig läsning och skrivning svarstid 99: e percentilen [väldefinierade konsekvens modeller](consistency-levels.md), och garanterat hög tillgänglighet, allt uppbackat av [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar automatiskt alla dina data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att behöva bry dig om schema- eller indexhantering.

## <a name="global-distribution-with-multi-master"></a>Global distribution med flera master

Azure Cosmos DB är noggrant utformat för att stödja flera innehavare, global distribution och multimaster för dokument, nyckel / värde-, graph och kolumnserier modeller som en molntjänst.

![Azure Cosmos DB-behållare partitionerade och fördelade på tre regioner](./media/distribute-data-globally/global-apps.png)

**En enskild Azure Cosmos DB-behållare partitionerade och distribueras över flera Azure-regioner**

Vi har lärt dig när du skapar Azure Cosmos DB kan inte att lägga till global distribution vara en eftertanke. Den kan inte ”bult-på” på ett ”plats”-databassystem. Funktioner som erbjuds av en globalt distribuerad databas som sträcker sig över utöver de funktioner som erbjuds av en traditionell geografiska haveriberedskap (Geo DR) erbjuds av ”enskild plats”-databaser. Plats-databaser som erbjuder Geo-DR-funktionen är en strikt delmängd av globalt distribuerade databaser.

Med Azure Cosmos DB nyckelfärdig global distribution, behöver utvecklare inte skapa sina egna replikering ställningar genom att använda antingen Lambda mönstret över databasloggen eller genom att utföra ”dubbla skrivningar” i flera regioner. Vi gör *inte* rekommenderar dessa metoder, eftersom det är omöjligt att se till att är korrekt av dessa metoder och ge bra serviceavtal.

## <a id="Next Steps"></a>Nästa steg

* [Hur Azure Cosmos DB tillhandahåller nyckelfärdig global distribution](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB globalt viktiga fördelar](distribute-data-globally-benefits.md)

* [Så här konfigurerar du global databasreplikering för Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Så här aktiverar du multimaster för Azure Cosmos DB-konton](enable-multi-master.md)

* [Så här automatisk och manuell redundans fungerar i Azure Cosmos DB](regional-failover.md)

* [Förstå konfliktlösning i Azure Cosmos DB](multi-master-conflict-resolution.md)

* [med hjälp av multimaster med öppen källkod NoSQL-databaser](multi-master-oss-nosql.md)
