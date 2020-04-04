---
title: Använda användardefinierade scheman
description: Tips om hur du använder T-SQL-användardefinierade scheman för att utveckla lösningar i Synapse SQL-pool.
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
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633459"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Användardefinierade scheman i Synapse SQL-pool
Den här artikeln fokuserar på att ge flera tips om hur du använder T-SQL-användardefinierade scheman för att utveckla lösningar i Synapse SQL-pool.

## <a name="schemas-for-application-boundaries"></a>Scheman för programgränser

Traditionella informationslager använder ofta separata databaser för att skapa programgränser baserat på antingen arbetsbelastning, domän eller säkerhet. 

Ett traditionellt SQL Server-informationslager kan till exempel innehålla en mellanlagringsdatabas, en datalagerdatabas och vissa datamardatabaser. I den här topologin fungerar varje databas som en arbetsbelastning och säkerhetsgräns i arkitekturen.

Sql-poolen kör däremot hela datalagerarbetsbelastningen i en databas. Korsdatabaskopplingar är inte tillåtna. SQL-poolen förväntar sig att alla tabeller som används av distributionslagret ska lagras i en databas.

> [!NOTE]
> SQL-poolen stöder inte korsdatabasfrågor av något slag. Därför måste implementeringar av informationslager som utnyttjar detta mönster ses över.
> 
> 

## <a name="recommendations"></a>Rekommendationer
Vad som följer är rekommendationer för att konsolidera arbetsbelastningar, säkerhet, domän och funktionella gränser med hjälp av användardefinierade scheman:

- Använd en SQL-pooldatabas för att köra hela datalagerarbetsbelastningen.
- Konsolidera din befintliga informationslagermiljö för att använda en SQL-pooldatabas.
- Utnyttja **användardefinierade scheman** för att ange den gräns som tidigare implementerats med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du en ren griffeltavla. Använd det gamla databasnamnet som grund för dina användardefinierade scheman i SQL-pooldatabasen.

Om scheman redan har använts har du några alternativ:

- Ta bort de äldre schemanamnen och börja om från början.
- Behåll de äldre schemanamnen genom att vänta på det äldre schemanamnet till tabellnamnet.
- Behåll de äldre schemanamnen genom att implementera vyer över tabellen i ett extra schema för att återskapa den gamla schemastrukturen.

> [!NOTE]
> Vid första inspektion alternativ 3 kan verka som det mest tilltalande alternativet. Men djävulen är i detalj. Vyer är skrivskyddade i SQL-poolen. Alla data eller tabelländringar måste utföras mot bastabellen. Alternativ 3 introducerar också ett lager av vyer i ditt system. Du kanske vill ge detta lite ytterligare eftertanke om du redan använder vyer i din arkitektur.
> 
> 

### <a name="examples"></a>Exempel:
Implementera användardefinierade scheman baserat på databasnamn:

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

Behåll äldre schemanamn genom att vänta på dem till tabellnamnet. Använd scheman för arbetsbelastningsgränsen:

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

Behåll äldre schemanamn med hjälp av vyer:

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

