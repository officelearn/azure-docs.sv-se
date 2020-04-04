---
title: Använda T-SQL-vyer
description: Tips om hur du använder T-SQL-vyer och utvecklar lösningar i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633396"
---
# <a name="views-in-synapse-sql-pool"></a>Vyer i Synapse SQL-pool

Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning.

SQL-poolen stöder både standard- och materialiserade vyer. Båda är virtuella tabeller som skapats med SELECT-uttryck och presenteras för frågor som logiska tabeller.

Vyer kapslar in komplexiteten i gemensam databeräkning och lägger till ett abstraktionslager i beräkningsändringar så att du inte behöver skriva om frågor.

## <a name="standard-view"></a>Standardvy

En standardvy beräknar sina data varje gång vyn används. Det finns inga data lagrade på disken. Personer använder vanligtvis standardvyer som ett verktyg som hjälper till att ordna logiska objekt och frågor i en databas.

Om du vill använda en standardvy måste en fråga referera direkt till den. Mer information finns i [DOKUMENTATIONEN SKAPA VY.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Vyer i SQL-poolen lagras endast som metadata. Därför är följande alternativ inte tillgängliga:

* Det finns inget schemabindningsalternativ
* Bastabeller kan inte uppdateras via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för EXPAND / NOEXPAND tips
* Det finns inga indexerade vyer i SQL-poolen

Standardvyer kan användas för att framtvinga prestandaoptimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributionsnyckel som en del av kopplingskriterierna för att minimera dataflyttning.

En annan fördel med en vy kan vara att tvinga fram en specifik fråga eller gå med tips. Genom att använda vyer på det här sättet garanteras att kopplingar alltid utförs på ett optimalt sätt och att användarna måste komma ihåg rätt konstruktion för sina kopplingar.

## <a name="materialized-view"></a>Materialiserad vy

En materialiserad vy förberäknar, lagrar och underhåller sina data i SQL-poolen precis som en tabell. Det behövs ingen omdämning varje gång en materialiserad vy används.

När data läses in i bastabeller uppdateras de materialiserade vyerna synkront.  Frågeoptimeraren använder automatiskt distribuerade materialiserade vyer för att förbättra frågeprestanda även om vyerna inte refereras i frågan.  

Frågor som drar mest nytta av materialiserade vyer är komplexa frågor (vanligtvis frågor med kopplingar och aggregeringar) på stora tabeller som ger liten resultatuppsättning.  

Mer information om den materialiserade vysyntaxen och andra krav finns i [SKAPA MATERIALISERAD VY SOM SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Om du vill ha vägledning för frågejusteringar kontrollerar du [Prestandajustering med materialiserade vyer](performance-tuning-materialized-views.md).

## <a name="example"></a>Exempel

Ett vanligt programmönster är att återskapa tabeller med CREATE TABLE AS SELECT (CTAS) följt av ett objekt som byter namn på mönstret när data läses in.  

I följande exempel läggs nya datumposter till i en datumdimension. Observera hur en ny tabell, DimDate_New, först skapas och sedan byter namn för att ersätta den ursprungliga versionen av tabellen.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Den här metoden kan dock resultera i att tabeller visas och försvinner från en användares vy tillsammans med att utfärda felmeddelanden om att en "tabell inte finns" visas.

Vyer kan användas för att ge användarna ett konsekvent presentationslager medan de underliggande objekten byter namn. Genom att ge åtkomst till data via vyer behöver användarna inte synlighet för de underliggande tabellerna.

Det här lagret ger en konsekvent användarupplevelse samtidigt som det säkerställer att informationslagerdesigners kan utveckla datamodellen. Att kunna utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under datainläsningsprocessen.

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [översikt över utveckling av SQL-pooler](sql-data-warehouse-overview-develop.md).
