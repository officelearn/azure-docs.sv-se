---
title: Ordlista för elastiska databasverktyg
description: Förklaring av termer som används för elastiska databasverktyg
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823633"
---
# <a name="elastic-database-tools-glossary"></a>Ordlista för elastiska databasverktyg

Följande termer definieras för [elastiska databasverktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [fragmentkartor](sql-database-elastic-scale-shard-map-management.md)och inkluderar [klientbiblioteket,](sql-database-elastic-database-client-library.md) [verktyget för delad sammanfogning,](sql-database-elastic-scale-overview-split-and-merge.md) [elastiska pooler](sql-database-elastic-pool.md)och [frågor](sql-database-elastic-query-overview.md). 

Dessa termer används i [Lägga till en shard med hjälp av elastiska databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [Använda klassen RecoveryManager för att åtgärda problem med fragmentkarten](sql-database-elastic-database-recovery-manager.md).

![Termer för elastisk skala][1]

**Databas**: En Azure SQL-databas. 

**Databeroende routning**: Den funktion som gör att ett program kan ansluta till en shard med en specifik sharding-nyckel. Se [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med **[multi-Shard-fråga](sql-database-elastic-scale-multishard-querying.md)**.

**Global fragmentkarta**: Kartan mellan sharding nycklar och deras respektive shards inom en **fragmentuppsättning**. Den globala fragmentkartan lagras i **fragmentkarthanteraren**. Jämför med **lokal fragmentkarta**.

**Lista shard karta**: En fragmentkarta där fragmenteringsnycklar mappas individuellt. Jämför med **range shard karta**.   

**Lokal fragmentkarta**: Lagrad på en fragment, den lokala fragmentkartan innehåller mappningar för shardlets som finns på shard.

**Multi-shard fråga**: Möjligheten att utfärda en fråga mot flera shards; resultatuppsättningar returneras med UNION ALL semantik (kallas även "fan-out query"). Jämför med **databeroende routning**.

**Multi-tenant** och **Single-tenant**: Detta visar en databas med en klientorganisation och en databas med flera innehavare:

![Databaser med en innehavare och flera innehavare](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **fragmenterade** databaser med en och flera innehavare. 

![Databaser med en innehavare och flera innehavare](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Intervallsvantredningskarta**: En fragmentkarta där fragmentfördelningsstrategin baseras på flera intervall med sammanhängande värden. 

**Referenstabeller**: Tabeller som inte är fragmenterade men replikeras över shards. Postnummer kan till exempel lagras i en referenstabell. 

**Shard**: En Azure SQL-databas som lagrar data från en fragmenterad datauppsättning. 

**Shard elasticitet**: Förmågan att utföra både **horisontell skalning** och **vertikal skalning**.

**Fragmenterade tabeller**: Tabeller som är fragmenterade, dvs vars data distribueras över shards baserat på deras fragmentnyckelvärden. 

**Sharding key**: Ett kolumnvärde som bestämmer hur data fördelas över shards. Värdetypen kan vara något av följande: **int**, **bigint,** **varbinary**eller **uniqueidentifier**. 

**Fragmentuppsättning**: Samlingen med shards som tillskrivs samma fragmentkarta i fragmentkarthanteraren.  

**Shardlet**: Alla data som är associerade med ett enda värde för en sharding-nyckel på en shard. En shardlet är den minsta enheten för dataförflyttning som är möjlig vid omfördelning av fragmenterade tabeller. 

**Fragmentkarta**: Uppsättningen mappningar mellan sharding nycklar och deras respektive shards.

**Shard map manager**: Ett hanteringsobjekt och datalager som innehåller fragmentmappningarna, fragmentplatser och mappningar för en eller flera fragmentuppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Vågrät skalning**: Handlingen att skala ut (eller i) en samling shards genom att lägga till eller ta bort shards till en fragmentkarta, som visas nedan.

![Vågrät och lodrät skalning][3]

**Sammanfoga**: Handlingen att flytta shardlets från två shards till en fragment och uppdatera fragmentkartan därefter.

**Shardlet flytta:** Handlingen att flytta en enda shardlet till en annan shard. 

**Shard**: Handlingen att horisontellt partitionera identiskt strukturerade data över flera databaser baserat på en sharding-nyckel.

**Split**: Handlingen att flytta flera shardlets från en fragment till en annan (vanligtvis ny) fragment. En sharding-nyckel tillhandahålls av användaren som delningspunkt.

**Vertikal skalning:** Handlingen att skala upp (eller ned) beräkningsstorleken för en enskild fragment. Om du till exempel ändrar en shard från Standard till Premium (vilket resulterar i fler datorresurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

