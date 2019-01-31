---
title: Migrera ditt schema till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera ditt schema till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461692"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrera dina scheman till SQL Data Warehouse
Riktlinjer för att migrera dina SQL-scheman till SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planera schemamigreringen

När du planerar en migrering finns i den [tabellöversikt] [ table overview] att bekanta dig med tabellen designöverväganden, till exempel statistik, distribution, partitionering och indexering.  Dessutom visas några [table-funktioner som inte stöds] [ unsupported table features] och sina lösningar.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Användardefinierade scheman används för att konsolidera databaser

Din befintliga arbetsbelastning har förmodligen mer än en databas. Ett SQL Server data warehouse kan exempelvis innehålla en mellanlagrings-databas och en datalagerdatabas vissa data mart-databaser. I den här topologin körs varje databas som en separat arbetsbelastning med separata säkerhetsprinciper.

Däremot kör hela arbetsbelastningen i informationslagret i en databas i SQL Data Warehouse. Mellan databasen tillåts inte kopplingar. SQL Data Warehouse förväntar sig därför alla tabeller som används av datalagret lagras i en databas.

Vi rekommenderar att du använder användardefinierade scheman för att konsolidera din befintliga arbetsbelastning till en databas. Exempel finns i [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Använda kompatibla datatyper
Ändra datatyper för att vara kompatibel med SQL Data Warehouse. En lista över datatyper som stöds respektive inte stöds finns i [datatyper][data types]. Avsnittet ger lösningar för typerna som inte stöds. Det ger också en fråga för att identifiera befintliga typer som inte stöds i SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimera Radstorleken
Minimera dina tabeller radlängden för bästa prestanda. Eftersom kortare rad längder leda till bättre prestanda, kan du använda de minsta datatyper som fungerar för dina data. 

För tabellens rad bredd har PolyBase en gräns på 1 MB.  Om du planerar att läsa in data i SQL Data Warehouse med PolyBase kan du uppdatera dina tabeller om du vill ha maximal rad bredden på mindre än 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Ange distributionsalternativ
SQL Data Warehouse är ett distribuerat databassystem. Varje tabell som är distribuerade eller replikerade över Compute-noder. Det finns ett Tabellalternativ som låter dig ange hur du distribuerar data. Alternativen är resursallokering, replikeras, eller hash-distribueras. Var och en har- och nackdelar. Om du inte anger distributionsalternativ, används SQL Data Warehouse resursallokering som standard.

- Resursallokering är standardinställningen. Det är enklast att använda och belastningar data så snabbt som möjligt, men kopplingar kräver dataförflyttning som saktar frågeprestanda.
- Replikerade lagrar en kopia av tabellen på varje beräkningsnod. Replikerade tabeller är högpresterande eftersom de inte behöver Dataförflyttning för kopplingar och aggregeringar. De kräver extra lagring och därför fungerar bäst för mindre tabeller.
- Hash-distribueras distribuerar raderna för alla noder via en hash-funktionen. Hash-distribueras tabeller är hjärtat i SQL Data Warehouse eftersom de har utformats för att tillhandahålla hög frågeprestanda för stora tabeller. Det här alternativet kräver vissa planerar att välja den bästa kolumnen som du vill distribuera data. Men om du inte väljer den bästa kolumnen första gången kan kan du enkelt nytt distribuera data på en annan kolumn. 

Det bästa alternativet för distribution för varje tabell finns [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Nästa steg
När du har migrerat ditt databasschema till SQL Data Warehouse, kan du gå vidare till någon av följande artiklar:

* [Migrera dina data][Migrate your data]
* [Migrera din kod][Migrate your code]

Mer information om metodtips för SQL Data Warehouse finns i den [bästa praxis] [ best practices] artikeln.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
