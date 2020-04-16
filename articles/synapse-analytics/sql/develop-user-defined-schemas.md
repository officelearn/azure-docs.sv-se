---
title: Användardefinierade scheman i Synapse SQL
description: I avsnitten nedan hittar du olika tips om hur du använder T-SQL-användardefinierade scheman för att utveckla lösningar med Synapse SQL-funktionen i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428711"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Användardefinierade scheman i Synapse SQL

I avsnitten nedan hittar du olika tips om hur du använder T-SQL-användardefinierade scheman för att utveckla lösningar inom Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Scheman för programgränser

Traditionell analysarkitektur använder ofta separata databaser för att skapa programgränser baserat på arbetsbelastning, domän eller säkerhet. En traditionell SQL Server-analysinfrastruktur kan till exempel innehålla en mellanlagringsdatabas, en analysdatabas och datamardatabaser. I den här topologin fungerar varje databas som en arbetsbelastning och säkerhetsgräns i arkitekturen.

I stället kör Synapse SQL hela analysarbetsbelastningen i en databas. Korsdatabaskopplingar är inte tillåtna. Synapse SQL förväntar sig att alla tabeller som används av distributionslagret ska lagras i en databas.

> [!NOTE]
> SQL-pooler stöder inte korsdatabasfrågor av något slag. Därför måste analysimplementeringar som utnyttjar det här mönstret revideras. SQL on-demand (preview) stöder frågor över flera databaser.

## <a name="user-defined-schema-recommendations"></a>Användardefinierade schemarekommendationer

Här ingår rekommendationer för att konsolidera arbetsbelastningar, säkerhet, domäner och funktionsgränser med hjälp av användardefinierade scheman:

- Använd en databas för att köra hela analysarbetsbelastningen.
- Konsolidera din befintliga analysmiljö så att den använder en databas.
- Utnyttja **användardefinierade scheman** för att ange den gräns som tidigare implementerats med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du en ren griffeltavla. Använd det gamla databasnamnet som grund för dina användardefinierade scheman i Synapse SQL-databasen.

Om scheman redan har använts har du några alternativ:

- Ta bort äldre schemanamn och börja om från början
- Behåll de äldre schemanamnen före väntande det äldre schemanamnet till tabellnamnet
- Behåll de äldre schemanamnen genom att implementera vyer över tabellen i ett extra schema som återskapar den gamla schemastrukturen.

> [!NOTE]
> Vid första inspektionen kan alternativ 3 verka som det mest tilltalande valet. Vyerna skrivs bara i Synapse SQL. Alla data eller tabelländringar måste utföras mot bastabellen. Alternativ 3 introducerar också ett lager av vyer i ditt system. Du kanske vill ge detta ytterligare en tanke om du redan använder vyer i din arkitektur.
> 
> 

### <a name="examples"></a>Exempel

Implementera användardefinierade scheman baserat på databasnamn.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behåll de äldre schemanamnen genom att vänta i väntan på dem till tabellnamnet. Använd scheman för arbetsbelastningsgränsen.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behåll de äldre schemanamnen med hjälp av vyer.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Alla ändringar i schemastrategin kräver en granskning av databasens säkerhetsmodell. I många fall kanske du kan förenkla säkerhetsmodellen genom att tilldela behörigheter på schemanivå.

Om fler detaljerade behörigheter krävs kan du använda databasroller. Mer information om databasroller finns i artikeln [Hantera databasroller och användare.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [Synapse SQL-utvecklingsöversikt](develop-overview.md).
