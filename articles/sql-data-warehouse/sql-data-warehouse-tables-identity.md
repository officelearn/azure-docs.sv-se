---
title: Använda identitet för att skapa surrogate nycklar – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och exempel för identitetsegenskapen för att skapa surrogate nycklar på tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: abe245e6174cb947e78252941c71ce6857b77f77
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306799"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Med IDENTITETEN för att skapa surrogate nycklar i Azure SQL Data Warehouse
Rekommendationer och exempel för identitetsegenskapen för att skapa surrogate nycklar på tabeller i Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>Vad är en surrogatnyckel?
En surrogatnyckel för en tabell är en kolumn med en unik identifierare för varje rad. Nyckeln genereras inte från tabelldata. Datamodellerare som att skapa surrogate nycklar på tabellerna när man utformar data warehouse-modeller. Du kan använda egenskapen identitet för att uppnå det här målet enkelt och effektivt sätt utan att påverka prestandan.  

## <a name="creating-a-table-with-an-identity-column"></a>Skapa en tabell med en IDENTITY-kolumn
Identitetsegenskapen har utformats för att skala ut över alla distributioner i informationslagret utan att påverka prestandan. Därför är implementeringen av identitet riktade mot att uppnå dessa mål. 

Du kan definiera en tabell med egenskapen identitet när du skapar tabellen med hjälp av syntaxen som liknar följande uttryck:

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

Du kan sedan använda `INSERT..SELECT` att fylla i tabellen.

Den här resten av det här avsnittet beskriver de olika delarna i implementeringen för att förstå dem mer i detalj.  

### <a name="allocation-of-values"></a>Allokering av värden
Identitetsegenskapen garanterar inte samma ordning som allokeras surrogate värdena, som funktionen hos SQL Server och Azure SQL Database. Men i Azure SQL Data Warehouse är avsaknad av en garanti tydligare. 

I följande exempel är en bild:

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

I föregående exempel landat två rader i distribution 1. Den första raden har surrogate värdet 1 i kolumnen `C1`, och den andra raden har 61 surrogate-värdet. Båda dessa värden har genererats av identititetsegenskapen. Allokeringen av värdena är dock inte sammanhängande. Det här beteendet är avsiktligt.

### <a name="skewed-data"></a>Skeva data 
Intervallet för datatypen är jämnt fördelade över distributioner. Om en distribuerad tabell drabbas från skeva data, kan värdeintervallet som är tillgängliga för datatypen utjämnas för tidigt. Till exempel om alla data som avslutas med en enkel distribution, har sedan effektivt tabellen åtkomst till endast 1-/ 60 av värdena för datatypen. Därför identitetsegenskapen är begränsad till `INT` och `BIGINT` datatyper endast.

### <a name="selectinto"></a>VÄLJ... I
När en befintlig identitetskolumn väljs i en ny tabell, ärver den nya kolumnen identitetsegenskapen, såvida inte någon av följande villkor föreligger:
- SELECT-instruktionen innehåller en koppling.
- Flera SELECT-satser kopplas med hjälp av UNION.
- Identitetskolumnen visas mer än en gång i SELECT-listan.
- IDENTITY-kolumn är en del av ett uttryck.
    
Om någon av dessa villkor uppfylls, skapas inte är NULL i stället för ärver identititetsegenskapen i kolumnen.

### <a name="create-table-as-select"></a>SKAPA TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) följer samma SQL Server-beteende som beskrivs i SELECT... I. Men du kan inte ange en IDENTITY-egenskapen i kolumndefinitionen för den `CREATE TABLE` ingår i instruktionen. Du kan också använda funktionen identitet i den `SELECT` en del av CTAS. För att fylla i en tabell, måste du använda `CREATE TABLE` att definiera tabellen följt av `INSERT..SELECT` att fylla i den.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicit infoga värden i en IDENTITY-kolumn 
SQL Data Warehouse stöder `SET IDENTITY_INSERT <your table> ON|OFF` syntax. Du kan använda den här syntaxen för att uttryckligen infoga värden i identitetskolumnen.

Många datamodellerare vilja använda fördefinierade negativa värden för vissa rader i dimensionerna. Ett exempel är 1 eller ”okänd medlem” rad. 

Nästa skriptet visar hur du uttryckligen lägga till den här raden genom att använda ställa in IDENTITY_INSERT:

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

Förekomst av identitetsegenskapen har vissa konsekvenser i koden inläsning av data. Det här avsnittet beskrivs vissa grundläggande mönster för inläsning av data i tabeller med hjälp av identitet. 

Skapa tabellen för att läsa in data i en tabell och generera en surrogatnyckel identiteten, och sedan använda INSERT... Välj eller infoga... VÄRDEN att utföra belastningen.

I följande exempel visar det grundläggande mönstret:
 
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

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Det går inte att använda Skapa tabell AS TUMINTERVALL ' för närvarande vid inläsning av data i en tabell med en identitetskolumn.
> 

Läs mer om att läsa in data, [designa extrahering, inläsning och transformera (ELT) för Azure SQL Data Warehouse](design-elt-data-loading.md) och [Metodtips för inläsning av](guidance-for-loading-data.md).


## <a name="system-views"></a>Systemvyer
Du kan använda den [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) catalog vyn för att identifiera en kolumn som innehåller egenskapen identitet.

För att hjälpa dig att bättre förstå databasschemat, det här exemplet visar hur du integrerar sys.identity_column' med andra systemkatalogvyer:

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
IDENTITY-egenskapen kan inte användas:
- När Kolumndatatypen inte är INT- eller BIGINT
- När kolumnen är också distributionsnyckeln
- När tabellen är en extern tabell 

Följande relaterade funktioner stöds inte i SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Vanliga åtgärder

Det här avsnittet visar exempelkod som du kan använda för att utföra vanliga uppgifter när du arbetar med identitetskolumner. 

Kolumnen C1 är identitet i alla följande uppgifter.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Hitta det högsta allokerade värdet för en tabell
Använd den `MAX()` funktionen för att fastställa det högsta värdet som allokerats för en distribuerad tabell:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Hitta start- och ökningsvärden för identitetsegenskapen
Du kan använda katalogvyerna för att identifiera identitet Öknings- och seed konfigurationsvärden för en tabell med följande fråga: 

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

* Mer information om hur du utvecklar tabeller finns [tabellöversikt] [översikt].  

