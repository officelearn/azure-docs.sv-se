---
title: Ordlista för elastisk databas verktyg | Microsoft Docs
description: Förklaring av termer som används för elastiska Databasverktyg
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 010d4f6d9f33b8fb6f1622321a3b566f68f28f5f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-tools-glossary"></a>Ordlista för verktyg för elastisk databas
Följande villkor definieras för den [elastisk Databasverktyg](sql-database-elastic-scale-introduction.md), en funktion i Azure SQL Database. Verktygen används för att hantera [Fragmentera mappar](sql-database-elastic-scale-shard-map-management.md), och inkludera den [klientbiblioteket](sql-database-elastic-database-client-library.md), [för delade sökvägssammanslagning](sql-database-elastic-scale-overview-split-and-merge.md), [elastiska pooler](sql-database-elastic-pool.md), och [frågor](sql-database-elastic-query-overview.md). 

Dessa villkor används i [att lägga till en Fragmentera med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [med hjälp av klassen RecoveryManager för att åtgärda problem med Fragmentera kartan](sql-database-elastic-database-recovery-manager.md).

![Elastisk skala villkor][1]

**Databasen**: en Azure SQL-databas. 

**Data beroende routning**: funktioner som gör att ett program för att ansluta till en Fragmentera ges en specifik horisontell partitionering nyckel. Se [Data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). Jämför med  **[flera Fragmentera frågan](sql-database-elastic-scale-multishard-querying.md)**.

**Globala Fragmentera kartan**: mappningen mellan horisontell partitionering nycklar och deras respektive shards inom en **Fragmentera set**. Globala Fragmentera kartan lagras i den **Fragmentera kartan manager**. Jämför med **lokala Fragmentera kartan**.

**Lista Fragmentera kartan**: en Fragmentera karta i vilka horisontell partitionering nycklar är mappade individuellt. Jämför med **intervallet Fragmentera kartan**.   

**Lokala Fragmentera kartan**: lagras på en Fragmentera lokala Fragmentera kartan innehåller mappningar för shardlets som finns på Fragmentera.

**Flera Fragmentera frågan**: möjligheten att köra en fråga mot flera delar, anger resultat returneras med hjälp av UNION ALL semantik (även kallat ”fan-out query”). Jämför med **data beroende routning**.

**Flera innehavare** och **stöd för en innehavare**: här visas en enskild klient-databas och en databas med flera innehavare:

![Enstaka och flera innehavare](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **delat** enstaka och flera innehavare. 

![Enstaka och flera innehavare](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Intervallet Fragmentera kartan**: en Fragmentera karta som distributionsstrategi Fragmentera baseras på flera intervall med sammanhängande värden. 

**Refererar till tabeller**: tabeller som inte är delat men replikeras över shards. Postnummer kan till exempel lagras i en tabell. 

**Fragmentera**: en Azure SQL-databas som lagrar data i ett delat datamängd. 

**Fragmentera elasticitet**: möjligheten att utföra både **teckenbredden** och **lodräta skalning**.

**Delat tabeller**: tabeller som är delat, d.v.s., distribueras vars data på shards baserat på deras nyckelvärden för horisontell partitionering. 

**Horisontell partitionering nyckeln**: ett kolumnvärde som avgör hur data distribueras över shards. Värdetypen kan vara något av följande: **int**, **bigint**, **varbinary**, eller **uniqueidentifier**. 

**Fragmentera set**: insamling av shards som hänföras till samma Fragmentera kartan i hanteraren för kartan Fragmentera.  

**Shardlet**: alla data som är associerade med ett värde för en delning nyckel på en Fragmentera. En shardlet är den minsta enheten av dataflyttning som möjligt när omdistribuera delat tabeller. 

**Fragmentera kartan**: uppsättningen mappningar mellan horisontell partitionering nycklar och deras respektive shards.

**Fragmentera kartan manager**: en management-objekt och i datalagret som innehåller Fragmentera mappningen, Fragmentera platser och mappningar för en eller flera Fragmentera uppsättningar.

![Mappningar][2]

## <a name="verbs"></a>Verb
**Teckenbredden**: att skala ut (eller i) en mängd shards genom att lägga till eller ta bort delar på en karta Fragmentera, enligt nedan.

![Vågräta och lodräta skalning][3]

**Sammanfoga**: flytta shardlets från två delar till en Fragmentera och därefter uppdatera Fragmentera kartan.

**Shardlet flytta**: att flytta en enda shardlet till en annan Fragmentera. 

**Fragmentera**: act vågrätt partitionering identiskt strukturerade data över flera databaser baserat på en nyckel för horisontell partitionering.

**Dela**: att flytta flera shardlets från en Fragmentera till en annan (vanligtvis nya) Fragmentera. En nyckel för horisontell partitionering anges av användaren som dela punkt.

**Lodrät skalning**: att skala upp (eller ned) ett enskilt Fragmentera prestandanivå. Ändra exempelvis en Fragmentera från Standard till Premium (vilket resulterar i mer datorresurser). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

