---
title: Ord lista för Elastic Database tools | Microsoft Docs
description: Förklaring av termer som används för Elastic Database-verktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568452"
---
# <a name="elastic-database-tools-glossary"></a>Ord lista för Elastic Database tools

Följande villkor definieras för [Elastic Database-verktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [Shard Maps](sql-database-elastic-scale-shard-map-management.md)och inkluderar [klient biblioteket](sql-database-elastic-database-client-library.md), [verktyget för delad sammanslagning](sql-database-elastic-scale-overview-split-and-merge.md), [elastiska pooler](sql-database-elastic-pool.md)och [frågor](sql-database-elastic-query-overview.md). 

Dessa villkor används när du [lägger till en Shard med hjälp av Elastic Database verktyg](sql-database-elastic-scale-add-a-shard.md) och [använder klassen RecoveryManager för att åtgärda Shard Map-problem](sql-database-elastic-database-recovery-manager.md).

![Villkor för elastisk skala][1]

**Databas**: En Azure SQL-databas. 

**Data beroende routning**: Funktionen som gör det möjligt för ett program att ansluta till en Shard som har fått en specifik horisontell partitionering-nyckel. Se [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med **[multi-Shard-fråga](sql-database-elastic-scale-multishard-querying.md)** .

**Global Shard-karta**: Kartan mellan horisontell partitionering-nycklar och deras respektive Shards i en **Shard-uppsättning**. Den globala Shard-kartan lagras i **Shard Map Manager**. Jämför med den **lokala Shard-kartan**.

**Visa lista Shard-karta**: En Shard-karta där horisontell partitionering-nycklar mappas individuellt. Jämför med **intervall Shard-kartan**.   

**Lokal Shard-karta**: Den lokala Shard-kartan innehåller mappningar för den shardletar som finns på Shard-Shard.

**Fråga om flera Shard**: Möjlighet att utfärda en fråga mot flera Shards; resultat uppsättningar returneras med UNION ALL semantik (kallas även "fläkt-ut-fråga"). Jämför med **data beroende routning**.

**Flera innehavare** och **en enda klient**: Detta visar en databas för en enskild klient och en databas för flera innehavare:

![Databaser med en och flera klienter](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **shardade** -databaser med en och flera innehavare. 

![Databaser med en och flera klienter](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Shard karta för intervall**: En Shard-karta där distributions strategin för Shard baseras på flera intervall av sammanhängande värden. 

**Referens tabeller**: Tabeller som inte är shardade men replikeras över Shards. Post nummer kan till exempel lagras i en referens tabell. 

**Shard**: En Azure SQL-databas som lagrar data från en shardade-data uppsättning. 

**Shard**-elastiskhet: Möjlighet att utföra både **horisontell skalning** och **vertikal skalning**.

**Shardade-tabeller**: Tabeller som är shardade, d.v.s. vars data fördelas på Shards baserat på deras horisontell partitionering nyckel värden. 

**Horisontell partitionering-nyckel**: Ett kolumn värde som avgör hur data fördelas mellan Shards. Värde typen kan vara något av följande: **int**, **bigint**, **varbinary**eller **uniqueidentifier**. 

**Shard uppsättning**: Samlingen av Shards som är attribut till samma Shard-karta i Shard Map Manager.  

**Shardlet**: Alla data som är associerade med ett enda värde för en horisontell partitionering-nyckel på en Shard. En shardlet är den minsta enheten av data förflyttning möjlig när du distribuerar om shardade-tabeller. 

**Shard-karta**: Uppsättningen mappningar mellan horisontell partitionering-nycklar och deras respektive Shards.

**Shard Map Manager**: Ett hanterings objekt och data lager som innehåller Shard-kartor, Shard-platser och mappningar för en eller flera Shard-uppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Vågrät skalning**: Vid utskalning (eller i) en samling av Shards genom att lägga till eller ta bort Shards till en Shard-mappning, som visas nedan.

![Vågrät och lodrät skalning][3]

**Sammanslagning**: Syftet med att flytta shardletar från två Shards till en Shard och uppdatera Shard-kartan enligt detta.

**Shardlet flytta**: Åtgärden att flytta en enskild shardlet till en annan Shard. 

**Shard**: Syftet med horisontell partitionering av identiskt strukturerade data över flera databaser baserat på en horisontell partitionering nyckel.

**Dela**: Åtgärden att flytta flera shardletar från en Shard till en annan (vanligt vis ny) Shard. En horisontell partitionering-nyckel tillhandahålls av användaren som delnings punkt.

**Lodrät skalning**: Att skala upp (eller ned) beräknings storleken för en enskild Shard. Du kan till exempel ändra en Shard från standard till Premium (vilket resulterar i mer data bearbetnings resurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

