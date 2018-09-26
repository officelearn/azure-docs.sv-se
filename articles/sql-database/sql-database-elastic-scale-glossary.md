---
title: Ordlista för verktyg för elastiska databaser | Microsoft Docs
description: Förklaring av termer som används för elastiska Databasverktyg
services: sql-database
ms.service: sql-database
subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 354d972e78a7fb7270b1b09f4af5aa95709fcd06
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162584"
---
# <a name="elastic-database-tools-glossary"></a>Ordlista för verktyg för elastiska databaser
Följande villkor har definierats för den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [shardkartor](sql-database-elastic-scale-shard-map-management.md), och inkluderar den [klientbiblioteket](sql-database-elastic-database-client-library.md), [dela / sammanslå verktyget](sql-database-elastic-scale-overview-split-and-merge.md), [elastiska pooler](sql-database-elastic-pool.md), och [frågor](sql-database-elastic-query-overview.md). 

Dessa villkor används i [att lägga till en shard med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [korrigera shard-kartproblem med RecoveryManager-klassen](sql-database-elastic-database-recovery-manager.md).

![Villkor för elastisk skala][1]

**Databasen**: en Azure SQL-databas. 

**Databeroende routning**: funktioner som gör att ett program kan ansluta till en shard som ett specifikt horisontell partitionering nyckel. Se [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med  **[Multi-shard fråga](sql-database-elastic-scale-multishard-querying.md)**.

**Global fragmentkartan**: mappningen mellan nycklar för horisontell partitionering och deras respektive fragment i en **shard set**. Global fragmentkartan lagras i den **karthanteraren**. Jämför med **lokala fragmentkartan**.

**Listfragmentkarta**: en skärvkarta i vilken horisontell partitionering nycklar mappas individuellt. Jämför med **intervall Fragmentkartan**.   

**Lokala fragmentkartan**: lagras på ett fragment, lokala fragmentkartan innehåller mappningar för shardletar som finns på fragmentet.

**Multi-shard fråga**: möjligheten att köra en fråga mot flera shards; uppsättningar resultat som returneras med hjälp av UNION ALL semantik (även kallat ”förgreningsfråga”). Jämför med **databeroende routning**.

**Flera innehavare** och **enda klient**: Detta visar en enda klient-databas och en databas för flera innehavare:

![Enstaka och flera innehavare databaser](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **shardade** enstaka och flera innehavare databaser. 

![Enstaka och flera innehavare databaser](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Intervallfragmentkarta**: en skärvkarta där shard-distributionsstrategi utifrån flera intervall med sammanhängande värden. 

**Tabeller**: tabeller som inte är fragmenterade (sharded) men replikeras över shards. Postnummer kan till exempel lagras i en tabell. 

**Shard**: en Azure SQL-databas som lagrar data från en shardade data. 

**Skärvelasticitet**: möjligheten att utföra både **horisontell skalning** och **vertikal skalning**.

**Shardade tabeller**: tabeller som är shardade, d.v.s., vars data är fördelad i shards baserat på deras nyckelvärden för horisontell partitionering. 

**Shardingnyckel**: ett kolumnvärde som avgör hur data ska distribueras över shards. Värdetypen kan vara något av följande: **int**, **bigint**, **varbinary**, eller **uniqueidentifier**. 

**Shard set**: insamling av shards som hänföras till på samma fragmentkartan i fragmentkartehanteraren.  

**Shardlet**: alla data som är associerade med ett enda värde för en shardingnyckel på en shard. En shardlet är den minsta enheten av dataförflyttning som möjligt när distribuera shardade tabeller. 

**Fragmentkartan**: uppsättningen mappningar mellan horisontell partitionering nycklar och deras respektive shards.

**Karthanteraren**: en management-objekt och i datalagret som innehåller den shard map(s) och platser för shards mappningar för en eller flera fragment uppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Horisontell skalning**: av skala ut (eller i) i en samling fragment genom att lägga till eller ta bort en skärvkarta partitioner enligt nedan.

![Vågrät och lodrät skalning][3]

**Sammanfoga**: flytta shardletar från två fragment till en shard och därefter uppdatera fragmentkartan.

**Shardlet flytta**: act att flytta en enda shardlet till en annan shard. 

**Shard**: av horisontell partitionering identiskt strukturerade data över flera databaser baserat på en shardingnyckel.

**Dela**: av flytta flera shardletar från en shard till en annan (normalt nya) shard. En shardingnyckel tillhandahålls av användaren som delad punkt.

**Lodrät skalning**: av skala upp (eller ned) beräkningsstorleken för en enskild fragment. Till exempel ändrar ett fragment från Standard till Premium (vilket resulterar i fler datorresurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

