---
title: Använda användardefinierade scheman i SQL Data Warehouse | Microsoft Docs
description: Tips för att använda användardefinierade T-SQL-scheman i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e0ae00e0fca5ed4c6fba04444e5c50424462d297
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479572"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Använda användardefinierade scheman i SQL Data Warehouse
Tips för att använda användardefinierade T-SQL-scheman i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="schemas-for-application-boundaries"></a>Scheman för program gränser

Traditionella informations lager använder ofta separata databaser för att skapa program gränser baserat på antingen arbets belastning, domän eller säkerhet. Till exempel kan ett traditionellt SQL Server informations lager omfatta en mellanlagringsdatabas, en informations lager databas och några data mart-databaser. I den här topologin fungerar varje databas som arbets belastning och säkerhets gränser i arkitekturen.

SQL Data Warehouse kör däremot hela arbets belastningen för data lagret i en databas. Kopplingar mellan databaser tillåts inte. Därför förväntas SQL Data Warehouse att alla tabeller som används av lagret ska lagras i en databas.

> [!NOTE]
> SQL Data Warehouse stöder inte kors databas frågor av någon typ. Det innebär att data lager implementeringar som utnyttjar det här mönstret måste ändras.
> 
> 

## <a name="recommendations"></a>Rekommendationer
Detta är rekommendationer för att konsolidera arbets belastningar, säkerhet, domän-och funktions gränser med hjälp av användardefinierade scheman

1. Använd en SQL Data Warehouse Database för att köra hela arbets belastningen för data lager
2. Konsolidera din befintliga data lager miljö för att använda en SQL Data Warehouse databas
3. Använd **användardefinierade scheman** för att tillhandahålla den gräns som tidigare implementerats med hjälp av databaser.

Om användardefinierade scheman inte har använts tidigare har du en ren Skriv platta. Använd bara det gamla databas namnet som grund för användardefinierade scheman i SQL Data Warehouse databasen.

Om scheman redan har använts har du några alternativ:

1. Ta bort de gamla schema namnen och börja om på nytt
2. Behåll gamla schema namn genom att vänta det gamla schema namnet till tabell namnet
3. Behåll gamla schema namn genom att implementera vyer över tabellen i ett extra schema för att återskapa den gamla schema strukturen.

> [!NOTE]
> Vid första inspektions alternativ 3 kan det verka som det mest tilltalande alternativet. Devil är dock i detalj. Vyer är skrivskyddade i SQL Data Warehouse. Data-eller tabell ändringar måste utföras mot bas tabellen. Alternativ 3 introducerar också ett lager med vyer i systemet. Du kanske vill ge detta ytterligare en tanke på att du redan använder vyer i arkitekturen.
> 
> 

### <a name="examples"></a>Exempel:
Implementera användardefinierade scheman baserat på databas namn

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

Behåll gamla schema namn genom att vänta dem i tabell namnet. Använd scheman för arbets belastnings gränser.

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

Behåll gamla schema namn med vyer

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
> Alla ändringar i schema strategin behöver en granskning av databasens säkerhets modell. I många fall kanske du kan förenkla säkerhets modellen genom att tilldela behörigheter på schema nivå. Om mer detaljerade behörigheter krävs kan du använda databas roller.
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).

