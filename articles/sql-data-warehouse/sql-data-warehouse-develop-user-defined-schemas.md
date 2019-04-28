---
title: Med användardefinierade scheman i SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-användardefinierade scheman i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: ae017461767a207deae1d990980258a1f661df3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439153"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Med användardefinierade scheman i SQL Data Warehouse
Tips för att använda T-SQL-användardefinierade scheman i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="schemas-for-application-boundaries"></a>Scheman för programmet

Traditionella informationslager använder ofta separata databaser för att skapa programmet gränser baserat på arbetsbelastning, domän eller säkerhet. En traditionell SQL Server data warehouse kan exempelvis innehålla en mellanlagrings-databas och en datalagerdatabas vissa data mart-databaser. Varje databas fungerar som en arbetsbelastning och säkerhetsgräns i arkitekturen i den här topologin.

Däremot kör hela arbetsbelastningen i informationslagret i en databas i SQL Data Warehouse. Mellan databasen tillåts inte kopplingar. SQL Data Warehouse förväntar sig därför alla tabeller som används av lagret som ska lagras i en databas.

> [!NOTE]
> SQL Data Warehouse stöder inte frågor mellan databaser av något slag. Data warehouse-implementeringar som använder det här mönstret måste därför ändras.
> 
> 

## <a name="recommendations"></a>Rekommendationer
Här följer rekommendationer om konsolidera arbetsbelastningar, säkerhet, domän och funktionella gränser med hjälp av användardefinierade scheman

1. Använd en SQL Data Warehouse-databas för att köra dina hela arbetsbelastningen för informationslager
2. Konsolidera din befintliga data warehouse-miljö för att använda ett SQL Data Warehouse-databas
3. Utnyttja **användardefinierade scheman** att tillhandahålla den gräns som tidigare implementeras med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare finns så det en tom arbetsyta. Använd bara gamla databasnamnet som grund för din användardefinierade scheman i SQL Data Warehouse-databas.

Om scheman har redan använts har du flera alternativ:

1. Ta bort äldre schemanamnen och börja om från början
2. Behålla äldre schemanamnen genom förväg väntande äldre schemanamnet till tabellens namn
3. Behåll det äldre schemat genom att implementera vyer över tabellen i ett extra schema du återskapa den gamla schemastrukturen.

> [!NOTE]
> Alternativ 3 kan verka som alternativet satsa på första kontroll. Djävulen är dock i informationen. Vyer är skrivskyddade endast i SQL Data Warehouse. Alla ändringar av data eller tabell måste utföras mot bastabellen. Alternativ 3 introducerar också ett lager av vyer i systemet. Du kanske vill ge detta ytterligare upp om du använder vyer i din arkitektur redan.
> 
> 

### <a name="examples"></a>Exempel:
Implementera användardefinierade scheman baserat på databasnamn

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behålla äldre schemanamn genom förväg väntande dem till tabellens namn. Använda scheman för arbetsbelastningen gräns.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behåll äldre schemanamn använda vyer

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Ändringar i schemat strategi måste en granskning av säkerhetsmodellen för databasen. I många fall kanske du kan förenkla säkerhetsmodellen genom att tilldela behörigheter på nivån schemat. Om det krävs mer detaljerade behörigheter kan du använda databasroller.
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

