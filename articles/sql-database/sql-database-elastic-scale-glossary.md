---
title: Ord lista för Elastic Database tools
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
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823633"
---
# <a name="elastic-database-tools-glossary"></a>Ord lista för Elastic Database tools

Följande villkor definieras för [Elastic Database-verktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [Shard Maps](sql-database-elastic-scale-shard-map-management.md)och inkluderar [klient biblioteket](sql-database-elastic-database-client-library.md), [verktyget för delad sammanslagning](sql-database-elastic-scale-overview-split-and-merge.md), [elastiska pooler](sql-database-elastic-pool.md)och [frågor](sql-database-elastic-query-overview.md). 

Dessa villkor används när du [lägger till en Shard med hjälp av Elastic Database verktyg](sql-database-elastic-scale-add-a-shard.md) och [använder klassen RecoveryManager för att åtgärda Shard Map-problem](sql-database-elastic-database-recovery-manager.md).

![Villkor för elastisk skala][1]

**Databas**: en Azure SQL-databas. 

**Data beroende routning**: funktionen som gör det möjligt för ett program att ansluta till en Shard som har fått en specifik horisontell partitionering-nyckel. Se [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med **[multi-Shard-fråga](sql-database-elastic-scale-multishard-querying.md)**.

**Global Shard-karta**: kartan mellan horisontell partitionering-nycklar och deras respektive Shards i en **Shard uppsättning**. Den globala Shard-kartan lagras i **Shard Map Manager**. Jämför med den **lokala Shard-kartan**.

**Visa lista Shard-karta**: en Shard-karta där horisontell partitionering-nycklar mappas individuellt. Jämför med **intervall Shard-kartan**.   

**Lokal Shard-karta**: lagrad på en Shard innehåller den lokala Shard-kartan mappningar för shardletar som finns på Shard.

**Shard fråga**: möjlighet att skicka en fråga till flera Shards; resultat uppsättningar returneras med UNION ALL semantik (kallas även "fläkt-ut-fråga"). Jämför med **data beroende routning**.

**Flera innehavare** och en **enda klient**: här visas en databas för en enda klient och en databas för flera klienter:

![Databaser med en och flera klienter](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **shardade** -databaser med en och flera innehavare. 

![Databaser med en och flera klienter](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Range Shard-karta**: en Shard-karta där Shard distributions strategin baseras på flera intervall av sammanhängande värden. 

**Referens tabeller**: tabeller som inte är shardade men replikeras över Shards. Post nummer kan till exempel lagras i en referens tabell. 

**Shard**: en Azure SQL-databas som lagrar data från en shardade-data uppsättning. 

**Shard elastiskhet**: möjlighet att utföra både **horisontell skalning** och **vertikal skalning**.

**Shardade-tabeller**: tabeller som är shardade, d.v.s. vars data fördelas över Shards baserat på deras horisontell partitionering nyckel värden. 

**Horisontell partitionering-nyckel**: ett kolumn värde som avgör hur data distribueras mellan Shards. Värde typen kan vara något av följande: **int**, **bigint**, **varbinary**eller **uniqueidentifier**. 

**Shard set**: den samling av Shards som har attribut till samma Shard-karta i Shard Map Manager.  

**Shardlet**: alla data som är associerade med ett enda värde för en horisontell partitionering-nyckel på en Shard. En shardlet är den minsta enheten av data förflyttning möjlig när du distribuerar om shardade-tabeller. 

**Shard-karta**: uppsättning mappningar mellan horisontell partitionering-nycklar och deras respektive Shards.

**Shard Map Manager**: ett hanterings objekt och data lager som innehåller Shard-karta, Shard-platser och mappningar för en eller flera Shard uppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Horisontell skalning**: funktionen för att skala ut (eller i) en samling av Shards genom att lägga till eller ta bort Shards till en Shard-karta, som du ser nedan.

![Vågrät och lodrät skalning][3]

**Sammanslagning**: Syftet med att flytta shardletar från två Shards till en Shard och uppdatera Shard-kartan enligt detta.

**Shardlet flytt**: åtgärden att flytta en enskild Shardlet till en annan Shard. 

**Shard**: Act av vågrätt strukturerade data över flera databaser baserat på en horisontell partitionering nyckel.

**Dela**: åtgärden att flytta flera shardletar från en Shard till en annan (vanligt vis ny) Shard. En horisontell partitionering-nyckel tillhandahålls av användaren som delnings punkt.

**Vertikal skalning**: Syftet med att skala upp (eller ned) beräknings storleken för enskilda Shard. Du kan till exempel ändra en Shard från standard till Premium (vilket resulterar i mer data bearbetnings resurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

