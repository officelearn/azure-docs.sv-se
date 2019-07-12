---
title: Använda T-SQL-vyer i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e8d516cfd764f947bd2fe7fc25f6394c313c0d9a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595506"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vyer i Azure SQL Data Warehouse
Tips för att använda T-SQL-vyer i Azure SQL Data Warehouse för utveckling av lösningar. 

## <a name="why-use-views"></a>Varför använda vyer?
Vyer kan användas i ett antal olika sätt att förbättra kvaliteten på din lösning.  Den här artikeln visar några exempel på hur du kan utöka din lösning med vyer, samt begränsningar som ska övervägas.


> [!IMPORTANT]
> Se den nya syntaxen för materialiserad vy på [skapa MATERIALISERADE VYN AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Mer information finns i den [viktig](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> Syntaxen för CREATE VIEW beskrivs inte i den här artikeln. Mer information finns i den [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) dokumentation.
> 

## <a name="architectural-abstraction"></a>Arkitektoniska abstraktion

Ett vanligt programmönster är att skapa tabeller med CREATE TABLE AS SELECT (CTAS) följt av ett objekt som byta namn på mönstret när inläsning av data på nytt.

I följande exempel lägger till nya datumposter till en datumdimension. Observera hur en ny tabell DimDate_New, skapas först och sedan har fått nytt namn om du vill ersätta den ursprungliga versionen av tabellen.

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

Den här metoden kan dock leda tabeller som visas och försvinner från en användare visa samt ”tabellen finns inte” felmeddelanden. Vyer kan användas för att ge användarna en konsekvent presentationslagret även om de underliggande objekt får ett nytt namn. Genom att ge åtkomst till data via vyer, behöver användarna inte synlighet för underliggande tabeller. Det här lagret tillhandahåller en konsekvent användarupplevelse även att säkerställa att datalagret designers kan utveckla datamodellen. Kan innebär utvecklas de underliggande tabellerna designers kan använda CTAS för att maximera prestanda under processen för datainläsning.   

## <a name="performance-optimization"></a>Prestandaoptimering
Vyer kan också användas om du vill framtvinga prestandaoptimerad kopplingar mellan tabeller. Till exempel kan en vy införliva en redundant distributionsnyckeln som en del av sammanbinder villkoren för att minimera dataförflyttning. En annan fördel med en vy kan vara att tvinga en specifik fråga eller slå samman tipset. Använda vyer i det här sättet garanterar att kopplingar utförs alltid på ett optimalt sätt undvika behovet för användare att komma ihåg rätt konstruktion för deras kopplingar.

## <a name="limitations"></a>Begränsningar
Vyer i SQL Data Warehouse lagras som endast metadata. Följande alternativ är därför inte tillgängliga:

* Det finns inget alternativ för bindning av schemat
* Bastabeller kan inte uppdateras i vyn
* Att går inte skapa vyer över temporära tabeller
* Det finns inget stöd för att expandera / NOEXPAND-tips
* Det finns inga indexerade vyer i SQL Data Warehouse

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).


