---
title: Använda T-SQL-vyer i Azure SQL Data Warehouse | Microsoft Docs
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
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479528"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vyer i Azure SQL Data Warehouse
Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för att utveckla lösningar. 

## <a name="why-use-views"></a>Varför ska jag använda vyer?
Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning.  I den här artikeln beskrivs några exempel på hur du kan utöka din lösning med vyer, samt de begränsningar som måste beaktas.


> [!IMPORTANT]
> Se den nya materialiserade vyns syntaxen i [skapa materialiserad vy som Välj](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Mer information finns i versions [anteckningarna](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> Syntaxen för CREATE VIEW beskrivs inte i den här artikeln. Mer information finns i dokumentationen för att [Skapa vy](/sql/t-sql/statements/create-view-transact-sql) .
> 

## <a name="architectural-abstraction"></a>Arkitektur abstraktion

Ett vanligt program mönster är att återskapa tabeller med CREATE TABLE som SELECT (CTAS) följt av ett objekt byter namn på ett mönster medan data läses in.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Den här metoden kan dock leda till att tabeller visas och visas inte i en användares vy, och att tabellen inte finns, och att det inte finns några fel meddelanden. Vyer kan användas för att ge användare ett konsekvent presentations lager, medan de underliggande objekten byter namn. Genom att ge åtkomst till data via vyer behöver användarna inte insyn i de underliggande tabellerna. Det här lagret ger en konsekvent användar upplevelse och säkerställer att data lagrets designers kan utveckla data modellen. Att kunna utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under inläsningen av data.   

## <a name="performance-optimization"></a>Prestanda optimering
Vyer kan också användas för att upprätthålla prestanda optimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributions nyckel som en del av villkoren för att minimera data flytten. En annan fördel med en vy kan vara att framtvinga en speciell fråga eller delta i tips. Med hjälp av vyer på det här sättet garanterar vi att kopplingar alltid utförs på ett optimalt sätt så att användarna inte behöver komma ihåg rätt konstruktion för sina kopplingar.

## <a name="limitations"></a>Begränsningar
Vyer i SQL Data Warehouse lagras endast som metadata. Det innebär att följande alternativ inte är tillgängliga:

* Det finns inget schema bindnings alternativ
* Det går inte att uppdatera bas tabeller via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för utöknings-och NOEXPAND-tips
* Det finns inga indexerade vyer i SQL Data Warehouse

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).


