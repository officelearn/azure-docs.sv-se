---
title: Använda IDENTITY för att skapa surrogatnycklar
description: Rekommendationer och exempel för att använda egenskapen IDENTITY för att skapa surrogatnycklar på tabeller i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d4a9880ed7ab26d0127026f49c0bc781cfc2a941
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586339"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Använda IDENTITY för att skapa surrogatnycklar i Synapse SQL-pool

Rekommendationer och exempel för att använda egenskapen IDENTITY för att skapa surrogatnycklar på tabeller i Synapse SQL-pool.

## <a name="what-is-a-surrogate-key"></a>Vad är en surrogatnyckel

En surrogatnyckel i en tabell är en kolumn med en unik identifierare för varje rad. Nyckeln genereras inte från tabelldata. Datamodellerare gillar att skapa surrogatnycklar på sina tabeller när de utformar datalagermodeller. Du kan använda egenskapen IDENTITY för att uppnå detta mål enkelt och effektivt utan att påverka belastningsprestanda.  

## <a name="creating-a-table-with-an-identity-column"></a>Skapa en tabell med en IDENTITETSKOLUMN

Egenskapen IDENTITY är utformad för att skala ut över alla distributioner i Synapse SQL-poolen utan att påverka belastningsprestanda. Därför är genomförandet av IDENTITY inriktad på att uppnå dessa mål.

Du kan definiera en tabell som att ha egenskapen IDENTITY när du först skapar tabellen med syntax som liknar följande uttryck:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Du kan `INSERT..SELECT` sedan använda för att fylla i tabellen.

I det här avsnittet beskrivs nyanserna i implementeringen som hjälper dig att förstå dem mer fullständigt.  

### <a name="allocation-of-values"></a>Fördelning av värden

Egenskapen IDENTITY garanterar inte den ordning i vilken surrogatvärdena allokeras, vilket återspeglar beteendet för SQL Server och Azure SQL Database. Men i Synapse SQL-pool är frånvaron av en garanti mer uttalad.

Följande exempel är en illustration:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

I föregående exempel landade två rader i fördelning 1. Den första raden har surrogatvärdet `C1`1 i kolumnen och den andra raden har surrogatvärdet 61. Båda dessa värden genererades av egenskapen IDENTITY. Fördelningen av värdena är dock inte sammanhängande. Det här beteendet är avsiktligt.

### <a name="skewed-data"></a>Skeva data

Intervallet med värden för datatypen fördelas jämnt över distributionerna. Om en distribuerad tabell lider av skeva data kan det värdeintervall som är tillgängligt för datatypen vara uttömt i förtid. Om till exempel alla data hamnar i en enda fördelning har tabellen i själva verket endast åtkomst till en sixtieth av datatypens värden. Därför är egenskapen IDENTITY begränsad `INT` `BIGINT` till och endast datatyper.

### <a name="selectinto"></a>Välj.. I

När en befintlig IDENTITETskolumn markeras i en ny tabell ärver den nya kolumnen egenskapen IDENTITY, såvida inte något av följande villkor är sant:

- SELECT-satsen innehåller en koppling.
- Flera SELECT-satser får sällskap med hjälp av UNION.
- KOLUMNEN IDENTITET visas mer än en gång i SELECT-listan.
- KOLUMNEN IDENTITET är en del av ett uttryck.

Om något av dessa villkor är sant skapas kolumnen INTE NULL i stället för att ärva egenskapen IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) följer samma SQL Server-beteende som dokumenteras för SELECT.. I. Du kan dock inte ange en IDENTITETsegenskap i kolumndefinitionen för den `CREATE TABLE` del av uttrycket. Du kan inte heller använda funktionen `SELECT` IDENTITET i den del av CTAS. Om du vill fylla i `CREATE TABLE` en tabell måste `INSERT..SELECT` du använda för att definiera tabellen följt av för att fylla i den.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Infoga värden uttryckligen i en IDENTITETskolumn

Synapse SQL-pool stöder `SET IDENTITY_INSERT <your table> ON|OFF` syntax. Du kan använda den här syntaxen för att uttryckligen infoga värden i kolumnen IDENTITET.

Många datamodellerare gillar att använda fördefinierade negativa värden för vissa rader i sina dimensioner. Ett exempel är raden -1 eller "okänd medlem".

Nästa skript visar hur du uttryckligen lägger till den här raden med hjälp av SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Läsa in data

Förekomsten av egenskapen IDENTITY har vissa konsekvenser för din datainläsningskod. I det här avsnittet beskrivs några grundläggande mönster för att läsa in data i tabeller med hjälp av IDENTITY.

Om du vill läsa in data i en tabell och generera en surrogatnyckel med hjälp av IDENTITY skapar du tabellen och använder sedan INSERT.. MARKERA ELLER INFOGA.. VÄRDEN för att utföra belastningen.

I följande exempel framhäver det grundläggande mönstret:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Det går inte att `CREATE TABLE AS SELECT` använda för närvarande när data läses in i en tabell med en IDENTITETskolumn.
>

Mer information om hur du läser in data finns i [Designa extrahera, läsa in och transformera (ELT) för Synapse SQL-pool](design-elt-data-loading.md) och [läs in metodtips](guidance-for-loading-data.md).

## <a name="system-views"></a>Systemvyer

Du kan använda [katalogvyn sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) för att identifiera en kolumn som har egenskapen IDENTITY.

För att hjälpa dig att bättre förstå databasschemat visar det här exemplet hur du integrerar sys.identity_column" med andra systemkatalogvyer:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Begränsningar

Egenskapen IDENTITY kan inte användas:

- När kolumndatatypen inte är INT eller BIGINT
- När kolumnen också är distributionsnyckeln
- När tabellen är en extern tabell

Följande relaterade funktioner stöds inte i Synapse SQL-pool:

- [IDENTITET()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Vanliga åtgärder

Det här avsnittet innehåller en del exempelkod som du kan använda för att utföra vanliga uppgifter när du arbetar med IDENTITETskolumner.

Kolumn C1 är IDENTITETen i alla följande aktiviteter.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Hitta det högsta tilldelade värdet för en tabell

Använd `MAX()` funktionen för att bestämma det högsta värdet som allokerats för en distribuerad tabell:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Hitta fröet och öka för egenskapen IDENTITY

Du kan använda katalogvyerna för att identifiera identitetssteg och såddkonfigurationsvärden för en tabell med hjälp av följande fråga:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över tabell](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [SKAPA TABELL (Egenskap för Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
