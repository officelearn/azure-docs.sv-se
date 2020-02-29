---
title: Använda identitet för att skapa surrogat nycklar
description: Rekommendationer och exempel för att använda identitets egenskapen för att skapa surrogat nycklar i tabeller i SQL Analytics.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c29b83b3473b8a4224587195587feacf834f2d72
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199435"
---
# <a name="using-identity-to-create-surrogate-keys-in-sql-analytics"></a>Använda identitet för att skapa surrogat nycklar i SQL Analytics

Rekommendationer och exempel för att använda identitets egenskapen för att skapa surrogat nycklar i tabeller i SQL Analytics.

## <a name="what-is-a-surrogate-key"></a>Vad är en surrogat nyckel?

En surrogat nyckel i en tabell är en kolumn med en unik identifierare för varje rad. Nyckeln genereras inte från tabell data. Data modellerare som skapar surrogat nycklar i sina tabeller när de utformar SQL Analytics-modeller. Du kan använda identitets egenskapen för att uppnå det här målet enkelt och effektivt utan att påverka belastnings prestanda.  

## <a name="creating-a-table-with-an-identity-column"></a>Skapa en tabell med en identitets kolumn

IDENTITETS egenskapen är utformad för att skala ut över alla distributioner i SQL Analytics-databasen utan att påverka belastnings prestanda. Därför orienteras implementeringen av identitet mot att uppnå dessa mål.

Du kan definiera en tabell som har egenskapen IDENTITY när du först skapar tabellen med hjälp av syntaxen som liknar följande uttryck:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Du kan sedan använda `INSERT..SELECT` för att fylla tabellen.

I den här återstoden av det här avsnittet beskrivs olika delarna för implementeringen för att hjälpa dig att förstå dem helt.  

### <a name="allocation-of-values"></a>Allokering av värden

IDENTITETS egenskapen garanterar inte i vilken ordning surrogat värden allokeras, vilket återspeglar beteendet för SQL Server och Azure SQL Database. Men i SQL-analys är frånvaron av en garanti mer uttalad.

Följande exempel är en illustration:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
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

I föregående exempel är två rader som landats i distribution 1. Den första raden har surrogat värdet 1 i kolumn `C1`och den andra raden har surrogat värdet 61. Båda värdena genererades av identitets egenskapen. Allokeringen av värdena är dock inte sammanhängande. Det här beteendet är avsiktligt.

### <a name="skewed-data"></a>Skevade data

Intervallet av värden för data typen sprids jämnt över distributionerna. Om en distribuerad tabell lider av skevade data kan det förfallna intervallet av värden som är tillgängliga för data typen vara förbrukat för tidigt. Om till exempel alla data avslutas i en enda distribution, så har tabellen i praktiken bara åtkomst till en-sixtieth av värdena för data typen. Av den anledningen är identitets egenskapen begränsad till `INT` och `BIGINT` endast data typer.

### <a name="selectinto"></a>Välj... IKRAFTTRÄDANDE

När en befintlig identitets kolumn väljs i en ny tabell ärver den nya kolumnen identitets egenskapen, om inte något av följande villkor är uppfyllt:

- SELECT-instruktionen innehåller en koppling.
- Flera SELECT-uttryck är anslutna med hjälp av UNION.
- IDENTITETS kolumnen visas mer än en tid i SELECT-listan.
- IDENTITETS kolumnen är en del av ett uttryck.

Om något av dessa villkor är uppfyllt, skapas kolumnen inte NULL i stället för att ärva identitets egenskapen.

### <a name="create-table-as-select"></a>CREATE TABLE SOM VÄLJ

CREATE TABLE AS SELECT (CTAS) följer samma SQL Server beteende som dokumenteras för SELECT. Ikraftträdande. Du kan dock inte ange en identitets egenskap i kolumn definitionen för `CREATE TABLE` delen av instruktionen. Du kan inte heller använda funktionen IDENTITY i `SELECT` delen av CTAS. Om du vill fylla i en tabell måste du använda `CREATE TABLE` för att definiera tabellen följt av `INSERT..SELECT` att fylla i den.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Infoga värden explicit i en identitets kolumn

SQL Analytics stöder `SET IDENTITY_INSERT <your table> ON|OFF` syntax. Du kan använda den här syntaxen för att explicit infoga värden i identitets kolumnen.

Många data modellerare som använder fördefinierade negativa värden för vissa rader i deras dimensioner. Ett exempel är raden-1 eller "okänd medlem".

Nästa skript visar hur du lägger till den här raden explicit genom att använda SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Läsa in data

Förekomsten av egenskapen IDENTITY har vissa följder för din data inläsnings kod. I det här avsnittet beskrivs några grundläggande mönster för att läsa in data i tabeller med hjälp av identitet.

Om du vill läsa in data i en tabell och generera en surrogat nyckel med hjälp av identitet skapar du tabellen och använder sedan INSERT.. Välj eller infoga.. VÄRDEN för att utföra belastningen.

I följande exempel visas ett grundläggande mönster:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Det går inte att använda `CREATE TABLE AS SELECT` för närvarande när data läses in i en tabell med en identitets kolumn.
>

Mer information om hur du läser in data finns i [utforma extrahering, belastning och transformering (ELT) för SQL-analys](design-elt-data-loading.md) och [inläsning av bästa praxis](guidance-for-loading-data.md).

## <a name="system-views"></a>Systemvyer

Du kan använda vyn [sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) Catalog för att identifiera en kolumn som har egenskapen Identity.

För att hjälpa dig att bättre förstå databasschemat, visar det här exemplet hur du integrerar sys. identity_column "med andra system katalog visningar:

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

Det går inte att använda identitets egenskapen:

- När kolumn data typen inte är INT eller BIGINT
- När kolumnen också är distributions nyckeln
- När tabellen är en extern tabell

Följande relaterade funktioner stöds inte i SQL Analytics:

- [IDENTITET ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Vanliga åtgärder

Det här avsnittet innehåller exempel kod som du kan använda för att utföra vanliga uppgifter när du arbetar med identitets kolumner.

Kolumn C1 är IDENTITETen i alla följande uppgifter.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Hitta det högsta allokerade värdet för en tabell

Använd funktionen `MAX()` för att fastställa det högsta tilldelade värdet för en distribuerad tabell:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Hitta dirigering och ökning för identitets egenskapen

Du kan använda katalogvyer för att identifiera identitets öknings-och Dirigerings konfigurations värden för en tabell med hjälp av följande fråga:

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

- [Table overview](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) (Tabellöversikt)
- [CREATE TABLE (Transact-SQL) identitet (egenskap)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
