---
title: Ordlista för verktyg för elastiska databaser | Microsoft Docs
description: Förklaring av termer som används för elastiska Databasverktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585627"
---
# <a name="elastic-database-tools-glossary"></a>Ordlista för verktyg för elastiska databaser

Följande villkor har definierats för den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [shardkartor](sql-database-elastic-scale-shard-map-management.md), och inkluderar den [klientbiblioteket](sql-database-elastic-database-client-library.md), [dela / sammanslå verktyget](sql-database-elastic-scale-overview-split-and-merge.md), [elastiska pooler](sql-database-elastic-pool.md), och [frågor](sql-database-elastic-query-overview.md). 

Dessa villkor används i [att lägga till en shard med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [korrigera shard-kartproblem med RecoveryManager-klassen](sql-database-elastic-database-recovery-manager.md).

![Villkor för elastisk skala][1]

**Databasen**: En Azure SQL-databas. 

**Databeroende routning**: Funktioner som gör att ett program kan ansluta till en shard som ett specifikt horisontell partitionering nyckel. Se [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med  **[Multi-shard fråga](sql-database-elastic-scale-multishard-querying.md)**.

**Global fragmentkartan**: Mappningen mellan nycklar för horisontell partitionering och deras respektive fragment i en **shard set**. Global fragmentkartan lagras i den **karthanteraren**. Jämför med **lokala fragmentkartan**.

**Listfragmentkarta**: En skärvkarta i vilken horisontell partitionering nycklar mappas individuellt. Jämför med **intervall Fragmentkartan**.   

**Lokala fragmentkartan**: Lagras på ett fragment, innehåller lokala fragmentkartan mappningar för shardletar som finns på fragmentet.

**Multi-shard fråga**: Möjligheten att köra en fråga mot flera shards; Anger resultat returneras med hjälp av UNION ALL semantik (även kallat ”förgreningsfråga”). Jämför med **databeroende routning**.

**Flera innehavare** och **enda klient**: Detta visar en enda klient-databas och en databas för flera innehavare:

![Enstaka och flera innehavare databaser](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **shardade** enstaka och flera innehavare databaser. 

![Enstaka och flera innehavare databaser](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Intervallfragmentkarta**: En skärvkarta där shard-distributionsstrategi utifrån flera intervall med sammanhängande värden. 

**Tabeller**: Tabeller som inte är fragmenterade (sharded) men replikeras över shards. Postnummer kan till exempel lagras i en tabell. 

**Shard**: En Azure SQL-databas som lagrar data från en shardade data. 

**Skärvelasticitet**: Möjligheten att utföra både **horisontell skalning** och **vertikal skalning**.

**Shardade tabeller**: Tabeller som är shardade, d.v.s., vars data är fördelad i shards baserat på deras nyckelvärden för horisontell partitionering. 

**Shardingnyckel**: Ett kolumnvärde som avgör hur data ska distribueras över shards. Värdetypen kan vara något av följande: **int**, **bigint**, **varbinary**, eller **uniqueidentifier**. 

**Shard set**: Fragment som hänföras till på samma fragmentkartan i fragmentkartehanteraren samling.  

**Shardlet**: Alla data som är associerade med ett enda värde för en shardingnyckel på en shard. En shardlet är den minsta enheten av dataförflyttning som möjligt när distribuera shardade tabeller. 

**Fragmentkartan**: Uppsättningen mappningar mellan horisontell partitionering nycklar och deras respektive shards.

**Karthanteraren**: En management-objekt och i datalagret som innehåller den shard map(s) och platser för shards mappningar för en eller flera fragment uppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Horisontell skalning**: Av skala ut (eller i) i en samling fragment genom att lägga till eller ta bort en skärvkarta partitioner enligt nedan.

![Vågrät och lodrät skalning][3]

**Sammanfoga**: Act flyttar shardletar från två fragment till en shard och därefter uppdatera fragmentkartan.

**Shardlet flytta**: Åtgärden för att flytta en enda shardlet till en annan shard. 

**Shard**: Av horisontell partitionering identiskt strukturerade data över flera databaser baserat på en shardingnyckel.

**Dela**: Åtgärden för att överföra flera shardletar från en shard till en annan (normalt nya) shard. En shardingnyckel tillhandahålls av användaren som delad punkt.

**Vertikal skalning**: Av skala upp (eller ned) beräkningsstorleken för en enskild fragment. Till exempel ändrar ett fragment från Standard till Premium (vilket resulterar i fler datorresurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

