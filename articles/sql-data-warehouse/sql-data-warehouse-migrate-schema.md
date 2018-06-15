---
title: Migrera schemat till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera schemat till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fb1085450a16acb0f9a06a9dea9d91fc5ca23363
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525173"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrera dina scheman till SQL Data Warehouse
Riktlinjer för att migrera dina SQL-scheman till SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planera migrering av schemat

När du planerar en migrering finns i [tabell översikt] [ table overview] att bekanta dig med tabellen designöverväganden, till exempel statistik, distribution, partitionering och indexering.  Den listar också några [stöds inte i tabellen funktioner] [ unsupported table features] och sina lösningar.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Användardefinierade scheman används för att konsolidera databaser

Din befintliga arbetsbelastning har antagligen mer än en databas. Ett informationslager i SQL Server kan exempelvis innehålla en fristående databas, en databas för datalager och vissa data mart-databaser. I den här topologin körs varje databas som en separat arbetsbelastning med separata säkerhetsprinciper.

Däremot kör SQL Data Warehouse hela arbetsbelastningen för informationslager inom en databas. Mellan databasen tillåts inte kopplingar. SQL Data Warehouse förväntar därför alla tabeller som används i datalagret lagras i en databas.

Vi rekommenderar att du använder användardefinierade scheman för att konsolidera din befintliga arbetsbelastning i en databas. Exempel finns [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Använd kompatibla datatyper
Ändra datatyper för att vara kompatibel med SQL Data Warehouse. En lista över datatyper som stöds respektive inte stöds, se [datatyper][data types]. Avsnittet ger lösningar för typer som inte stöds. Det ger också en fråga för att identifiera befintliga typer som inte stöds i SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimera Radstorleken
Minimera radlängden tabeller för bästa prestanda. Eftersom kortare raden längder leda till bättre prestanda, kan du använda de minsta datatyper som fungerar för dina data. 

Tabellens rad bredd har PolyBase en gräns på 1 MB.  Om du planerar att läsa in data till SQL Data Warehouse med PolyBase, uppdatera tabellerna om du vill ha maximal raden bredden på mindre än 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Ange distributionsalternativ för
SQL Data Warehouse är en distribuerad databas. Varje tabell distribueras eller replikeras över Compute-noder. Det finns ett Tabellalternativ som du kan ange hur du distribuerar data. Alternativen är resursallokering, replikeras, eller hash distribueras. Varje har- och nackdelar. Om du inte anger distributionsalternativet använder SQL Data Warehouse resursallokering som standard.

- Resursallokering är standardinställningen. Den är enkel att använda och belastningar data så snabbt som möjligt och kopplingar kräver dataflyttning som minskar frågeprestanda.
- Replikerade lagrar en kopia av tabellen på varje Compute-nod. Replikerade tabeller är performant eftersom de inte kräver dataflyttning för kopplingar och aggregeringar. De kräver extra lagring och därför fungerar bäst för mindre tabeller.
- Hash distribuerade distribuerar rader för alla noder via en hash-funktionen. Distribuerad hash-tabeller är hjärtat i SQL Data Warehouse eftersom de har utformats för att tillhandahålla hög frågeprestanda för stora tabeller. Det här alternativet kräver vissa planerar att välja den bästa kolumn som ska distribuera data. Men om du inte väljer den bästa kolumnen första gången, kan du enkelt nytt distribuera data på en annan kolumn. 

Om du vill välja den bästa distributionsalternativ för varje tabell, se [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Nästa steg
När du har migrerat dina databasschemat till SQL Data Warehouse, kan du gå vidare till någon av följande artiklar:

* [Migrera dina data][Migrate your data]
* [Migrera koden][Migrate your code]

Mer information om metodtips för SQL Data Warehouse, finns det [metodtips] [ best practices] artikel.

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
