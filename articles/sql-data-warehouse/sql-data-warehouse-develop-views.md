---
title: "Använda T-SQL-vyer i Azure SQL Data Warehouse | Microsoft Docs"
description: "Tips för att använda Transact-SQL-vyer i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Vyer i SQL Data Warehouse
Vyer är särskilt användbart i SQL Data Warehouse. De kan användas i ett antal olika sätt att förbättra kvaliteten på din lösning.  Den här artikeln visar några exempel på hur du utöka din lösning med vyer, samt begränsningarna som måste beaktas.

> [!NOTE]
> Syntaxen för `CREATE VIEW` beskrivs inte i den här artikeln. Mer information finns i [skapa vy] [ CREATE VIEW] -artikel på MSDN denna referens.
> 
> 

## <a name="architectural-abstraction"></a>Arkitektur abstraction
Ett mycket vanligt mönster för programmet är att skapa tabeller med Skapa tabell AS Välj (CTAS) följt av ett objekt som ett mönster medan data läses in igen.

Exemplet nedan lägger till nya datum poster till en datumdimension. Observera hur en ny tabble DimDate_New, skapas först och sedan ett nytt namn om du vill ersätta den ursprungliga versionen av tabellen.

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

Den här metoden kan emellertid resultera i tabeller som visas och försvinner från vyn för en användare som ”tabellen finns inte” felmeddelanden. Vyer kan användas för att ge användarna en konsekvent presentation layer samtidigt som de underliggande objekt ändras. Genom att ge användare åtkomst till data via en vyer, innebär att användarna inte behöver ha synlighet för de underliggande tabellerna. Detta ger en konsekvent användarupplevelse medan säkerställer att datalagret designers kan utvecklas datamodellen och maximera prestanda med hjälp av CTAS under processen för datainläsning.    

## <a name="performance-optimization"></a>Prestandaoptimering
Vyer kan också användas för att genomdriva prestanda optimerade kopplingar mellan tabeller. En vy kan inkludera en redundant distribution tangent som en del av anslutande villkoren för att minimera dataflyttning.  En annan fördel med en vy kan vara att tvinga en specifik fråga eller anslutande tipset. Använda vyer i det här sättet garanterar att kopplingar utförs alltid på ett optimalt sätt att undvika behovet för användare att komma ihåg rätt konstruktion för deras kopplingar.

## <a name="limitations"></a>Begränsningar
Vyer i SQL Data Warehouse är endast metadata.  Följande alternativ är därför inte tillgängliga:

* Det finns inget alternativ för bindning av schemat
* Bastabeller kan inte uppdateras via vyn
* Att går inte skapa vyer över temporära tabeller
* Det finns inget stöd för Expandera / NOEXPAND-tips
* Det finns inga indexerade vyer i SQL Data Warehouse

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][SQL Data Warehouse development overview].
För `CREATE VIEW` syntax Se [skapa vy][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
