---
title: Använda identitet för att skapa surrogat nycklar – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och exempel för att skapa surrogat nycklar för tabellerna i Azure SQL Data Warehouse med egenskapen identitet.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Med IDENTITETEN för att skapa surrogat nycklar i Azure SQL Data Warehouse
Rekommendationer och exempel för att skapa surrogat nycklar för tabellerna i Azure SQL Data Warehouse med egenskapen identitet.

## <a name="what-is-a-surrogate-key"></a>Vad är ett surrogat-nyckeln?
En surrogat nyckel för en tabell är en kolumn med en unik identifierare för varje rad. Nyckeln genereras inte från tabelldata. Data Modellerare som att skapa surrogat nycklar för sina tabeller när de skapar data warehouse-modeller. Du kan använda egenskapen identitet för att åstadkomma detta enkelt och effektivt sätt utan att läsa in prestanda påverkas.  

## <a name="creating-a-table-with-an-identity-column"></a>Skapa en tabell med en identitetskolumn
Egenskapen identitet är utformat för att skala ut över alla distributioner i datalagret utan att läsa in prestanda påverkas. Därför är implementeringen av IDENTITETEN riktade mot att uppnå dessa mål. 

Du kan definiera en tabell med egenskapen identitet när du skapar tabellen med hjälp av syntaxen som liknar följande sats:

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

Den här resten av det här avsnittet beskrivs de olika delarna i implementeringen som hjälper dig att förstå hur de mer i detalj.  

### <a name="allocation-of-values"></a>Allokering av värden
Egenskapen identitet garanterar inte den ordning som tilldelas surrogat värden, som funktionen hos SQL Server och Azure SQL Database. Men i Azure SQL Data Warehouse är avsaknaden av garanti tydligare. 

Följande exempel är en bild:

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

I föregående exempel landat två rader i distribution 1. Den första raden har värdet 1 surrogat i kolumnen `C1`, och den andra raden har 61 surrogat-värdet. Båda dessa värden har genererats av egenskapen identitet. Fördelningen av värden är inte sammanhängande. Det här beteendet är avsiktligt.

### <a name="skewed-data"></a>Skeva data 
Värdeintervall för datatypen jämnt fördelade över distributioner. Om en distribuerad tabell drabbas från skeva data, kan värdeintervall tillgängliga för datatypen utjämnas för tidigt. Till exempel om alla data som avslutas med en enda distributionsplats, har sedan effektivt tabellen åtkomst till endast 1-/ 60 av värdena för datatypen. Därför egenskapen identitet är begränsad till `INT` och `BIGINT` datatyperna endast.

### <a name="selectinto"></a>VÄLJ... I
När en befintlig identitetskolumn väljs i en ny tabell, ärver den nya kolumnen som identititetsegenskapen, såvida inte någon av följande villkor är uppfyllda:
- SELECT-satsen innehåller en koppling.
- Flera SELECT-satser kopplas med hjälp av UNION.
- Identitetskolumnen visas mer än en gång i SELECT-listan.
- IDENTITY-kolumn är en del av ett uttryck.
    
Om någon av dessa villkor är SANT skapas kolumnen inte är NULL i stället för att ärva egenskapen identitet.

### <a name="create-table-as-select"></a>SKAPA TABLE AS SELECT
Skapa tabell AS Välj (CTAS) följer samma SQL Server-beteende som beskrivs i SELECT... I. Men du kan inte ange en IDENTITY-egenskapen i kolumndefinitionen av den `CREATE TABLE` ingår i instruktionen. Du kan också använda funktionen identitet i den `SELECT` ingår i CTAS. Om du vill fylla i en tabell, måste du använda `CREATE TABLE` att definiera tabellen följt av `INSERT..SELECT` ska fyllas.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicit infogar värden i en identitetskolumn 
SQL Data Warehouse stöder `SET IDENTITY_INSERT <your table> ON|OFF` syntax. Du kan använda den här syntaxen för att explicit infoga värden i identitetskolumnen.

Många data Modellerare vill använda fördefinierade negativa värden för vissa rader i sina dimensioner. Ett exempel är 1 eller ”okänd medlem” rad. 

Nästa skript visar hur du uttryckligen lägga till den här raden med hjälp av ange IDENTITY_INSERT:

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

Förekomsten av egenskapen identitet har vissa effekter datainläsning koden. Det här avsnittet beskrivs vissa grundläggande mönster för att läsa in data i tabeller identiteten. 

Om du vill läsa in data i en tabell och skapa surrogat nyckel identiteten, skapa tabellen och sedan använda INSERT... Välj eller infoga... VÄRDEN att utföra belastningen.

I följande exempel visar grundläggande mönster:
 
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
> Det går inte att använda Skapa tabell AS TIFIKATET ' för närvarande vid inläsning av data i en tabell med en identitetskolumn.
> 

Läs mer om att läsa in data, [designa extrahera, Load and Transform ELT () för Azure SQL Data Warehouse](design-elt-data-loading.md) och [inläsning metodtips](guidance-for-loading-data.md).


## <a name="system-views"></a>Systemvyer
Du kan använda den [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalogen vyn för att identifiera en kolumn med egenskapen identitet.

För att hjälpa dig att bättre förstå databasschemat det här exemplet illustrerar hur du integrerar sys.identity_column' med andra system katalogvyer:

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
Egenskapen identitet kan inte användas:
- När kolumnens datatyp är inte INT- eller BIGINT
- Om kolumnen är också distributionsnyckeln
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

Det här avsnittet innehåller även kodexempel som du kan använda för att utföra vanliga uppgifter när du arbetar med identitetskolumner. 

Kolumnen C1 är IDENTITETEN i följande uppgifter.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Hitta det högsta allokerade värdet för en tabell
Använd den `MAX()` funktionen för att fastställa det högsta värdet som allokerats för en distribuerad tabell:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Hitta start- och ökningsvärden för egenskapen identitet
Du kan använda katalogvyerna för att identifiera identitet ökning och seed konfigurationsvärden för en tabell med hjälp av följande fråga: 

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

* Mer information om hur du utvecklar tabeller finns [tabellen översikt] [översikt].  

