---
title: "Användardefinierade scheman i SQL Data Warehouse | Microsoft Docs"
description: "Tips för att använda Transact-SQL-scheman i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 52af5bd5-d5d3-4f9b-8704-06829fb924e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: dfb58956ad6637cf0f50b4c052ab98fb7c26139d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Användardefinierade scheman i SQL Data Warehouse
Traditionella informationslager använder ofta separata databaser för att skapa programmet gränser, baserat på arbetsbelastning, domän och säkerhet. En traditionell SQL Server-datalagret kan exempelvis innehålla en fristående databas, en databas för datalager och vissa data mart-databaser. Varje databas fungerar som en arbetsbelastning och säkerhetsgräns i arkitekturen i den här topologin.

Däremot kör SQL Data Warehouse hela arbetsbelastningen för informationslager inom en databas. Mellan databasen tillåts inte kopplingar. SQL Data Warehouse förväntar därför alla tabeller som används av för datalagret lagras i en databas.

> [!NOTE]
> SQL Data Warehouse stöder inte mellan databasfrågor av något slag. Därför behöver data warehouse implementeringar som använder det här mönstret ändras.
> 
> 

## <a name="recommendations"></a>Rekommendationer
Dessa är rekommendationer för att konsolidera arbetsbelastningar, säkerhet, domän och funktionella gränser med hjälp av användardefinierade scheman

1. Använd en SQL Data Warehouse-databas för att köra din hela arbetsbelastningen för informationslager
2. Konsolidera din befintliga data warehouse-miljö för att använda en SQL Data Warehouse-databas
3. Utnyttjar **användardefinierade scheman** att ange kolumnrubrikens tidigare implementeras med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du grunden. Använd bara gamla databasnamnet som bas för dina egna scheman i SQL Data Warehouse-databas.

Om scheman har redan använts har du några alternativ:

1. Ta bort de äldra schemanamn och börja om från början
2. Behålla de äldra schemanamn av före väntande äldre schemanamnet till tabellnamnet
3. Behåll det äldre schemat genom att implementera vyer över tabellen i en extra schemat att återskapa gamla datastrukturen.

> [!NOTE]
> På första inspektion verka alternativet intressanta alternativ 3. Djävulen är dock i informationen. Vyer är skrivskyddad i SQL Data Warehouse. Alla ändringar av data- eller behöver utföras mot bastabellen. Alternativ 3 introducerar också ett lager av vyer i systemet. Du kanske vill ge den här ytterligare upp om du använder vyer i din arkitektur redan.
> 
> 

### <a name="examples"></a>Exempel:
Implementera anpassade scheman baserat på databasnamn

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

Behålla äldre schemanamn av före väntande dem till tabellens namn. Scheman används för arbetsbelastningen gräns.

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

Behålla äldre schemanamn med hjälp av vyer

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
> Ändringar i schemat strategi måste en granskning av säkerhetsmodellen för databasen. I många fall kanske du kan förenkla säkerhetsmodellen genom att tilldela behörigheter på schemanivån. Om mer detaljerade behörigheter som krävs kan du använda databasroller.
> 
> 

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
