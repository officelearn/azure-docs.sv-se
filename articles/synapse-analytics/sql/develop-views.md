---
title: T-SQL-vyer med Synapse SQL
description: Tips om hur du använder T-SQL-vyer och utvecklar lösningar med Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428659"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL-vyer med Synapse SQL
I den här artikeln hittar du tips om hur du använder T-SQL-vyer och utvecklar lösningar med Synapse SQL. 

## <a name="why-use-views"></a>Varför använda vyer?

Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning.  Den här artikeln belyser några exempel på hur du kan berika din lösning med vyer och innehåller de begränsningar som måste beaktas.

### <a name="sql-pool---create-view"></a>SQL pool - skapa vy

> [!NOTE]
> **SQL-pool**: Syntax för SKAPA VY beskrivs inte i den här artikeln. Mer information finns i [DOKUMENTATIONEN SKAPA VY.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="sql-on-demand-preview---create-view"></a>SQL on-demand (förhandsgranskning) - skapa vy

> [!NOTE]
> **SQL on-demand**: Syntax för SKAPA-VY beskrivs inte i den här artikeln. Mer information finns i [DOKUMENTATIONEN SKAPA VY.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="architectural-abstraction"></a>Arkitektonisk abstraktion

Ett vanligt programmönster är att återskapa tabeller med [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), som följs av ett objekt som byter namn på mönster när data läses in.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tänk på att den här metoden kan leda till att tabeller visas och försvinner från en användares vy och uppmanar felmeddelanden om att "tabellen inte finns". Vyer kan användas för att ge användarna ett konsekvent presentationslager medan de underliggande objekten byter namn.

Genom att ge åtkomst till data via vyer behöver användarna inte synlighet för de underliggande tabellerna. Förutom en konsekvent användarupplevelse säkerställer det här lagret att analysdesigners kan utveckla datamodellen. Möjligheten att utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under datainläsningsprocessen.

## <a name="performance-optimization"></a>Prestandaoptimering

Vyer kan också användas för att framtvinga prestandaoptimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributionsnyckel som en del av kopplingskriterierna för att minimera dataflyttning.

Att tvinga fram en specifik fråga eller gå med tips är en annan fördel med att använda T-SQL-vyer. Därför säkerställer vyfunktionen att kopplingar alltid utförs på ett optimalt sätt. Du undviker behovet av att användarna kommer ihåg rätt konstruktion för sina kopplingar.

## <a name="limitations"></a>Begränsningar

Vyer i Synapse SQL lagras bara som metadata. Därför är följande alternativ inte tillgängliga:

* Det finns inget schemabindningsalternativ
* Bastabeller kan inte uppdateras via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för EXPAND / NOEXPAND tips
* Det finns inga indexerade vyer i Synapse SQL

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [Synapse SQL-utvecklingsöversikt](develop-overview.md).



