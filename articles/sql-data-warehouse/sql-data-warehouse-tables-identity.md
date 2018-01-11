---
title: Skapa surrogat nycklar identiteten | Microsoft Docs
description: "Lär dig hur du använder identitet för att skapa surrogat nycklar för tabellerna."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: e10b58743fad5f7c2c4f00b51f06d4ec9bcb6768
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Skapa surrogat nycklar identiteten
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Datatyper][Data Types]
> * [Distribuera][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistik][Statistics]
> * [Tillfällig][Temporary]
> * [Identitet][Identity]
> 
> 

Många data Modellerare som att skapa surrogat nycklar för sina tabeller när de skapar data warehouse-modeller. Du kan använda egenskapen identitet för att åstadkomma detta enkelt och effektivt sätt utan att läsa in prestanda påverkas. 

## <a name="get-started-with-identity"></a>Kom igång med identitet
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

## <a name="behavior"></a>Beteende
Egenskapen identitet är utformat för att skala ut över alla distributioner i datalagret utan att läsa in prestanda påverkas. Därför är implementeringen av IDENTITETEN riktade mot att uppnå dessa mål. Det här avsnittet beskrivs de olika delarna i implementeringen som hjälper dig att förstå hur de mer i detalj.  

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

## <a name="explicitly-insert-values-into-an-identity-column"></a>Uttryckligen lägga till värden i en identitetskolumn 
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

## <a name="load-data-into-a-table-with-identity"></a>Läs in data i en tabell med identitet

Förekomsten av egenskapen identitet har vissa effekter datainläsning koden. Det här avsnittet beskrivs vissa grundläggande mönster för att läsa in data i tabeller identiteten. 

### <a name="load-data-with-polybase"></a>Läs in data med PolyBase
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
> Det går inte att använda `CREATE TABLE AS SELECT` för närvarande vid inläsning av data i en tabell med en identitetskolumn.
> 

Mer information om att läsa in data med hjälp av verktyget bulk copy program (BCP) finns i följande artiklar:

- [Läsa in med PolyBase][]
- [PolyBase bästa praxis][]

### <a name="load-data-with-bcp"></a>Läs in data med BCP
BCP är ett kommandoradsverktyg som du kan använda för att läsa in data till SQL Data Warehouse. En av dess parametrar (-E) styr beteendet för BCP vid inläsning av data i en tabell med en identitetskolumn. 

När E - anges är de värden som lagras i filen för kolumnen med IDENTITETEN kvar. Om E - *inte* anges kommer värden i den här kolumnen ignoreras. Om identitetskolumnen inte finns sedan data har lästs in som vanligt. Värdena genereras enligt principen ökning och startvärde för egenskapen.

Mer information om att läsa in data med hjälp av BCP finns i följande artiklar:

- [Läsa in med BCP][]
- [BCP i MSDN][]

## <a name="catalog-views"></a>Katalogvyer
SQL Data Warehouse stöder den `sys.identity_columns` vyn katalog. Den här vyn kan användas för att identifiera en kolumn med egenskapen identitet.

För att hjälpa dig att bättre förstå databasschemat det här exemplet illustrerar hur du integrerar `sys.identity_columns` med andra system katalogvyer:

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
Egenskapen identitet kan inte användas i följande scenarier:
- Där Kolumndatatypen inte är INT eller BIGINT
- Om kolumnen är också distributionsnyckeln
- Om tabellen är en extern tabell 

Följande relaterade funktioner stöds inte i SQL Data Warehouse:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Uppgifter

Det här avsnittet innehåller även kodexempel som du kan använda för att utföra vanliga uppgifter när du arbetar med identitetskolumner.

> [!NOTE] 
> Kolumnen C1 är IDENTITETEN i följande uppgifter.
> 
 
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

* Mer information om hur du utvecklar tabeller finns [tabell översikt][Overview], [tabell datatyper][Data Types], [distribuera en tabell][Distribute], [Index i en tabell][Index], [partitionera en tabell][Partition], och [temporära tabeller][Temporary]. 
* Mer information om metodtips finns [SQL Data Warehouse metodtips][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Läsa in med bcp]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Läsa in med PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[PolyBase bästa praxis]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[BCP i MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
