---
title: Använda användardefinierade scheman
description: Tips om hur du använder T-SQL-användardefinierade scheman i Azure SQL Data Warehouse för att utveckla lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351534"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Använda användardefinierade scheman i SQL Data Warehouse
Tips om hur du använder T-SQL-användardefinierade scheman i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="schemas-for-application-boundaries"></a>Scheman för programgränser

Traditionella informationslager använder ofta separata databaser för att skapa programgränser baserat på antingen arbetsbelastning, domän eller säkerhet. Ett traditionellt SQL Server-informationslager kan till exempel innehålla en mellanlagringsdatabas, en datalagerdatabas och vissa datamardatabaser. I den här topologin fungerar varje databas som en arbetsbelastning och säkerhetsgräns i arkitekturen.

SQL Data Warehouse kör däremot hela datalagerarbetsbelastningen i en databas. Korsdatabaskopplingar är inte tillåtna. Sql Data Warehouse förväntar sig därför att alla tabeller som används av distributionslagret ska lagras i en databas.

> [!NOTE]
> SQL Data Warehouse stöder inte korsdatabasfrågor av något slag. Därför måste implementeringar av informationslager som utnyttjar detta mönster ses över.
> 
> 

## <a name="recommendations"></a>Rekommendationer
Det här är rekommendationer för att konsolidera arbetsbelastningar, säkerhets-, domän- och funktionsgränser med hjälp av användardefinierade scheman

1. Använd en SQL Data Warehouse-databas för att köra hela datalagerarbetsbelastningen
2. Konsolidera din befintliga informationslagermiljö för att använda en SQL Data Warehouse-databas
3. Utnyttja **användardefinierade scheman** för att ange den gräns som tidigare implementerats med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du en ren griffeltavla. Använd bara det gamla databasnamnet som grund för dina användardefinierade scheman i SQL Data Warehouse-databasen.

Om scheman redan har använts har du några alternativ:

1. Ta bort äldre schemanamn och börja om från början
2. Behåll de äldre schemanamnen genom att vänta på det äldre schemanamnet till tabellnamnet
3. Behåll de äldre schemanamnen genom att implementera vyer över tabellen i ett extra schema för att återskapa den gamla schemastrukturen.

> [!NOTE]
> Vid första inspektion alternativ 3 kan verka som det mest tilltalande alternativet. Men djävulen är i detalj. Vyer är skrivskyddade i SQL Data Warehouse. Alla data eller tabelländringar måste utföras mot bastabellen. Alternativ 3 introducerar också ett lager av vyer i ditt system. Du kanske vill ge detta lite ytterligare eftertanke om du redan använder vyer i din arkitektur.
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

Behåll äldre schemanamn genom att vänta på dem till tabellnamnet. Använd scheman för arbetsbelastningsgränsen.

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
> Alla ändringar i schemastrategin behöver en översyn av databasens säkerhetsmodell. I många fall kanske du kan förenkla säkerhetsmodellen genom att tilldela behörigheter på schemanivå. Om fler detaljerade behörigheter krävs kan du använda databasroller.
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

