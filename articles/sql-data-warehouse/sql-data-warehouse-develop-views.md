---
title: Använda T-SQL-vyer i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 28280a067e7008c20361e0a0041c81ba84e7f74c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vyer i Azure SQL Data Warehouse
Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för utveckling av lösningar. 

## <a name="why-use-views"></a>Varför använda vyer?
Vyer kan användas i ett antal olika sätt att förbättra kvaliteten på din lösning.  Den här artikeln visar några exempel på hur du utöka din lösning med vyer, samt begränsningarna som måste beaktas.

> [!NOTE]
> Syntaxen för att skapa vy beskrivs inte i den här artikeln. Mer information finns i [skapa vy](/sql/t-sql/statements/create-view-transact-sql) dokumentation.
> 
> 

## <a name="architectural-abstraction"></a>Arkitektur abstraction
En gemensam programmönster är att skapa tabeller med Skapa tabell AS Välj (CTAS) följt av ett objekt som ett mönster medan data läses in igen.

I följande exempel lägger till nya datum poster en datumdimension. Observera hur en ny tabell DimDate_New, skapas först och sedan ett nytt namn om du vill ersätta den ursprungliga versionen av tabellen.

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

Den här metoden kan emellertid resultera i tabeller som visas och försvinner från vyn för en användare som ”tabellen finns inte” felmeddelanden. Vyer kan användas för att ge användarna en konsekvent presentation layer samtidigt som de underliggande objekt ändras. Användarna behöver inte synlig för de underliggande tabellerna genom att ge åtkomst till data i vyer. Det här lagret ger en konsekvent användarupplevelse medan säkerställer att datalagret designers kan utvecklas datamodellen. Går innebär att utvecklas underliggande tabeller designers kan använda CTAS för att maximera prestanda under processen för datainläsning.   

## <a name="performance-optimization"></a>Prestandaoptimering
Vyer kan också användas för att genomdriva prestanda optimerade kopplingar mellan tabeller. En vy kan inkludera en redundant distribution tangent som en del av anslutande villkoren för att minimera dataflyttning. En annan fördel med en vy kan vara att tvinga en specifik fråga eller anslutande tipset. Använda vyer i det här sättet garanterar att kopplingar utförs alltid på ett optimalt sätt att undvika behovet för användare att komma ihåg rätt konstruktion för deras kopplingar.

## <a name="limitations"></a>Begränsningar
Vyer i SQL Data Warehouse lagras som endast metadata. Följande alternativ är därför inte tillgängliga:

* Det finns inget alternativ för bindning av schemat
* Bastabeller kan inte uppdateras via vyn
* Att går inte skapa vyer över temporära tabeller
* Det finns inget stöd för Expandera / NOEXPAND-tips
* Det finns inga indexerade vyer i SQL Data Warehouse

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).


