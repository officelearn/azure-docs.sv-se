---
title: Använda T-SQL-vyer
description: Tips för att använda T-SQL-vyer och utveckla lösningar i Synapse SQL-poolen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212300"
---
# <a name="views-in-synapse-sql-pool"></a>Vyer i Synapse SQL-pool

Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning.

SQL-poolen stöder både standard-och materialiserade vyer. Båda är virtuella tabeller som skapats med SELECT-uttryck och visas för frågor som logiska tabeller.

Vyer kapslar in komplexiteten för vanlig data beräkning och lägger till ett abstraktions lager för beräknings ändringar så att du inte behöver skriva om frågor.

## <a name="standard-view"></a>Standardvy

En standardvy beräknar data varje gång som vyn används. Det finns inga data lagrade på disken. Användarna använder vanligt vis standardvyer som ett verktyg som hjälper dig att ordna de logiska objekten och frågorna i en databas.

Om du vill använda en standardvy måste en fråga hänvisa till den. Mer information finns i dokumentationen för att [Skapa vy](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Vyer i SQL-poolen lagras endast som metadata. Det innebär att följande alternativ inte är tillgängliga:

* Det finns inget schema bindnings alternativ
* Bas tabeller kan inte uppdateras via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för utöknings-och NOEXPAND-tips
* Det finns inga indexerade vyer i SQL-poolen

Standardvyer kan användas för att upprätthålla prestanda optimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributions nyckel som en del av villkoren för att minimera data flytten.

En annan fördel med en vy kan vara att framtvinga en speciell fråga eller delta i tips. Med hjälp av vyer på det här sättet garanterar vi att kopplingar alltid utförs på ett optimalt sätt så att användarna inte behöver komma ihåg rätt konstruktion för sina kopplingar.

## <a name="materialized-view"></a>Materialiserad vy

En materialiserad vy för beräkning, lager och underhåll av data i SQL-poolen precis som en tabell. Ingen omberäkning krävs varje gång en materialiserad vy används.

När data läses in i bas tabeller uppdaterar SQL-poolen de materialiserade vyerna synkront.  I frågans optimering används automatiskt distribuerade materialiserade vyer för att förbättra frågans prestanda även om vyerna inte refereras till i frågan.  

Frågor som har störst nytta av materialiserade vyer är komplexa frågor (vanligt vis frågor med kopplingar och agg regeringar) i stora tabeller som producerar små resultat uppsättningar.  

Mer information om syntaxen för materialiserad vy och andra krav finns i [skapa materialiserad vy som Välj](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Kontrol lera [prestanda justering med materialiserade vyer](performance-tuning-materialized-views.md)för att ställa frågor om frågor.

## <a name="example"></a>Exempel

Ett vanligt program mönster är att återskapa tabeller med CREATE TABLE som SELECT (CTAS) följt av ett objekt byter namn på ett mönster när data läses in.  

I följande exempel läggs nya datum poster till i en datum dimension. Observera att en ny tabell, DimDate_New, först skapas och sedan byter namn till den ursprungliga versionen av tabellen.

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

Den här metoden kan dock leda till att tabeller som visas och visas från en användares vy tillsammans med meddelande om att det inte finns några fel meddelanden om att tabellen inte finns.

Vyer kan användas för att ge användare ett konsekvent presentations lager medan de underliggande objekten byter namn. Genom att ge åtkomst till data via vyer behöver användarna inte insyn i de underliggande tabellerna.

Det här lagret ger en konsekvent användar upplevelse och säkerställer att data lagrets designers kan utveckla data modellen. Att kunna utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under inläsningen av data.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [Översikt över SQL-poolens utveckling](sql-data-warehouse-overview-develop.md).
