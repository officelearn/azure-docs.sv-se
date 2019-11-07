---
title: Använda T-SQL-vyer
description: Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14ff990fa21f4af3c77b4dba10b4fea758ff32b6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685751"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vyer i Azure SQL Data Warehouse
Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning. 

Azure SQL Data Warehouse stöder standard-och materialiserade vyer. Båda är virtuella tabeller som skapats med SELECT-uttryck och visas för frågor som logiska tabeller. Vyer kapslar in komplexiteten för vanlig data beräkning och lägger till ett abstraktions lager för beräknings ändringar så att du inte behöver skriva om frågor.

## <a name="standard-view"></a>Standardvy
En standardvy beräknar data varje gång som vyn används. Det finns inga data lagrade på disken. Användarna använder vanligt vis standardvyer som ett verktyg som hjälper dig att ordna de logiska objekten och frågorna i en databas. Om du vill använda en standardvy måste en fråga hänvisa till den. Mer information finns i dokumentationen för att [Skapa vy](/sql/t-sql/statements/create-view-transact-sql) .

Vyer i SQL Data Warehouse lagras endast som metadata. Det innebär att följande alternativ inte är tillgängliga:
* Det finns inget schema bindnings alternativ
* Det går inte att uppdatera bas tabeller via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för utöknings-och NOEXPAND-tips
* Det finns inga indexerade vyer i SQL Data Warehouse

Standardvyer kan användas för att upprätthålla prestanda optimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributions nyckel som en del av villkoren för att minimera data flytten. En annan fördel med en vy kan vara att framtvinga en speciell fråga eller delta i tips. Med hjälp av vyer på det här sättet garanterar vi att kopplingar alltid utförs på ett optimalt sätt så att användarna inte behöver komma ihåg rätt konstruktion för sina kopplingar.

## <a name="materialized-view"></a>Materialiserad vy
En materialiserad vy för beräkning, lager och underhåll av data i Azure SQL Data Warehouse precis som en tabell. Ingen omberäkning krävs varje gång en materialiserad vy används. När data läses in i bas tabeller Azure SQL Data Warehouse synkront uppdaterar de materialiserade vyerna.  I frågans optimering används automatiskt distribuerade materialiserade vyer för att förbättra frågans prestanda även om vyerna inte refereras till i frågan.  Frågor som har störst nytta av materialiserade vyer är komplexa frågor (vanligt vis frågor med kopplingar och agg regeringar) i stora tabeller som producerar små resultat uppsättningar.  

Mer information om syntaxen för materialiserad vy och andra krav finns i [skapa materialiserad vy som Välj](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Kontrol lera [prestanda justering med materialiserade vyer](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)för att ställa frågor om frågor. 

## <a name="example"></a>Exempel
Ett vanligt program mönster är att återskapa tabeller med CREATE TABLE som SELECT (CTAS) följt av ett objekt byter namn på ett mönster medan data läses in.  I följande exempel läggs nya datum poster till i en datum dimension. Observera att en ny tabell, DimDate_New, först skapas och sedan byter namn till den ursprungliga versionen av tabellen.

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
Den här metoden kan dock leda till att tabeller visas och visas inte i en användares vy, och att tabellen inte finns, och att det inte finns några fel meddelanden. Vyer kan användas för att ge användare ett konsekvent presentations lager, medan de underliggande objekten byter namn. Genom att ge åtkomst till data via vyer behöver användarna inte insyn i de underliggande tabellerna. Det här lagret ger en konsekvent användar upplevelse och säkerställer att data lagrets designers kan utveckla data modellen. Att kunna utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under inläsningen av data.   

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).


