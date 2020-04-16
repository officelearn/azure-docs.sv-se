---
title: Fråga lagringsfiler med SQL on-demand (preview) i Synapse SQL
description: Beskriver frågor om lagringsfiler med hjälp av SQL on-demand (preview) resurser inom Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 48e2ec7f853ce94263f4e843b529609d5376ed5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424043"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Fråga lagringsfiler med SQL on-demand (preview) resurser inom Synapse SQL

Med SQL on-demand (förhandsversion) kan du fråga data i datasjön. Den erbjuder en T-SQL-frågeyta som rymmer halvstrukturerade och ostrukturerade datafrågor.

För frågor stöds följande T-SQL-aspekter:

- Full [SELECT yta,](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) inklusive majoriteten av SQL-funktioner, operatörer och så vidare.
- SKAPA EXTERN TABELL SOM SELECT ([CETAS](develop-tables-cetas.md)) skapar en [extern tabell](develop-tables-external-tables.md) och exporterar sedan parallellt resultaten av en Transact-SQL SELECT-sats till Azure Storage.

Mer information om vad som är kontra vad som för närvarande inte stöds finns i översiktsartikeln [för SQL on-demand.](on-demand-workspace-overview.md)

När Azure AD-användare kör frågor är standardinställningen för lagringskonton som ska nås med azure AD-direktautentiseringsprotokollet. Som sådan kommer användare att personifieras och behörigheter kontrolleras på lagringsnivå. Du kan [styra lagringsåtkomsten](develop-storage-files-storage-access-control.md) efter dina behov.

## <a name="extensions"></a>Tillägg

För att stödja en smidig upplevelse för att fråga efter data som finns i Azure Storage-filer använder SQL on-demand [OPENROWSET-funktionen](develop-openrowset.md) med ytterligare funktioner:

- [Fråga flera filer eller mappar](#query-multiple-files-or-folders)
- [PARETT filformat](#parquet-file-format)
- [Ytterligare alternativ för att arbeta med avgränsad text (fältterminering, radsluter, escape-tecken)](#additional-options-for-working-with-delimited-text)
- [Läsa en vald delmängd av kolumner](#read-a-chosen-subset-of-columns)
- [Inferens för schema](#schema-inference)
- [funktionen filnamn](#filename-function)
- [funktionen filsöka](#filepath-function)
- [Arbeta med komplexa typer och kapslade eller upprepade datastrukturer](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Fråga flera filer eller mappar

Om du vill köra en T-SQL-fråga över en uppsättning filer i en mapp eller uppsättning mappar när du behandlar dem som en enda entitet eller raduppsättning anger du en sökväg till en mapp eller ett mönster (med jokertecken) över en uppsättning filer eller mappar.

Följande regler gäller:

- Mönster kan visas antingen i en del av en katalogsökväg eller i ett filnamn.
- Flera mönster kan visas i samma katalogsteg eller filnamn.
- Om det finns flera jokertecken inkluderas filer i alla matchande sökvägar i den resulterande filuppsättningen.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Mer information finns [i Frågemappar och flera filer.](query-folders-multiple-csv-files.md)

### <a name="parquet-file-format"></a>PARETT filformat

Om du vill fråga om parkettkällasdata använder du FORMAT = "PARKETT"

```sql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Läs artikeln [Query Parquet Files](query-parquet-files.md) för användningsexempel.

### <a name="additional-options-for-working-with-delimited-text"></a>Ytterligare alternativ för att arbeta med avgränsad text

Dessa ytterligare parametrar introduceras för att arbeta med CSV-filer (avgränsad text) filer:

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Anger tecknet i filen som används för att fly från sig själv och alla avgränsare värden i filen. Om escape-tecknet följs av antingen ett annat värde än sig själv eller något av avgränsarvärdena, tas escape-tecknet bort när värdet läses.
Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktiverad. Det kommer inte att användas för att undkomma citattecken. Citattecknet escapes med dubbla citattecken i linje med Excel CSV-beteendet.
- FIELDTERMINATOR ='field_terminator' Anger den fältterminering som ska användas. Standardterminatorn för fält är ett kommatecken ("**,**")
- ROWTERMINATOR ='row_terminator' Anger den radterminer som ska användas. Standardslutaren är ett nyradstecken: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Läsa en vald delmängd av kolumner

Om du vill ange kolumner som du vill läsa kan du ange en valfri WITH-sats i OPENROWSET-satsen.

- Om det finns CSV-datafiler anger du kolumnnamn och deras datatyper om du vill läsa alla kolumner. Om du vill ha en delmängd av kolumner använder du ordningstal för att välja kolumnerna från de ursprungliga datafilerna efter ordningsman. Kolumnerna binds av ordningsbeteckningen.
- Om det finns parquetdatafiler anger du kolumnnamn som matchar kolumnnamnen i de ursprungliga datafilerna. Kolumnerna är bundna av namn.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Exempel finns i [Läsa CSV-filer utan att ange alla kolumner](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Inferens för schema

Genom att utelämna WITH-satsen från OPENROWSET-satsen kan du instruera tjänsten att automatiskt identifiera (dra slutsatsen) schemat från underliggande filer.

> [!NOTE]
> Detta fungerar för närvarande endast för PARQUET filformat.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');

```

### <a name="filename-function"></a>Funktionen Filnamn

Den här funktionen returnerar filnamnet som raden kommer från.

Om du vill fråga efter specifika filer läser du avsnittet Filnamn i artikeln [Fråga specifika filer.](query-specific-files.md#filename)

### <a name="filepath-function"></a>Funktionen Filsökväg

Den här funktionen returnerar en hel sökväg eller en del av banan:

- När den anropas utan parameter returnerar du den fullständiga filsökvägen som en rad kommer från.
- När den anropas med parametern returneras en del av sökvägen som matchar jokertecknet på den position som anges i parametern. Parametervärde 1 returnerar till exempel en del av sökvägen som matchar det första jokertecknet.

Mer information finns i avsnittet Filsökväg i artikeln [Frågespecifika filer.](query-specific-files.md#filepath)

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Arbeta med komplexa typer och kapslade eller upprepade datastrukturer

För att möjliggöra en smidig upplevelse när du arbetar med data som lagras i kapslade eller upprepade datatyper, till exempel i [Parkettfiler,](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) har Starlight lagt till tilläggen nedan.

#### <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

Om du vill projicera data kör du en SELECT-sats över parettfilen som innehåller kolumner med kapslade datatyper. Vid utdata serialiseras kapslade värden till JSON och returneras som en VARchar(8000) SQL-datatyp.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Mer detaljerad information finns i avsnittet Projekt kapslade eller upprepade data i artikeln [Frågeparkettkapslade typer.](query-parquet-nested-types.md#project-nested-or-repeated-data)

#### <a name="access-elements-from-nested-columns"></a>Komma åt element från kapslade kolumner

Om du vill komma åt kapslade element från en kapslad kolumn, till exempel Struct, använder du "punktatering" för att sammanfoga fältnamn i banan. Ange sökvägen som column_name i FUNKTIONEN MED i OPENROWSET-funktionen.

Syntaxfragmentexempelet är följande:

```sql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Som standard matchar openrowset-funktionen källfältsnamnet och sökvägen med kolumnnamnen i WITH-satsen. Element som finns på olika kapslingsnivåer inom samma källa Parquet fil kan nås via WITH-satsen.

**Returvärden**

- Funktionen returnerar ett skalärvärde, till exempel int, decimal och varchar, från det angivna elementet och på den angivna banan för alla parketttyper som inte finns i gruppen Kapslade typ.
- Om banan pekar på ett element av kapslad typ returnerar funktionen ett JSON-fragment som börjar från det översta elementet på den angivna banan. JSON-fragmentet är av typen varchar(8000).
- Om egenskapen inte kan hittas vid den angivna column_name returnerar funktionen ett fel.
- Om egenskapen inte kan hittas vid den angivna column_path, beroende på [banläge,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)returnerar funktionen ett fel när den är i strikt läge eller null när den är i släppläge.

För frågeexempel läser du avsnittet Access-element från kapslade kolumner i artikeln [Frågeparkettkapslade typer.](query-parquet-nested-types.md#access-elements-from-nested-columns)

#### <a name="access-elements-from-repeated-columns"></a>Komma åt element från upprepade kolumner

Om du vill komma åt element från en upprepad kolumn, till exempel ett element i en array eller karta, använder [du JSON_VALUE-funktionen](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för varje skalärelement som du behöver projicera och tillhandahålla:

- Kapslad eller upprepad kolumn, som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som anger vilket element eller egenskap som ska komma åt, som en andra parameter

Om du vill komma åt icke-skalärelement från en upprepad kolumn använder du [JSON_QUERY-funktionen](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för varje icke-skalär element som du behöver för att projicera och tillhandahålla:

- Kapslad eller upprepad kolumn, som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som anger vilket element eller egenskap som ska komma åt, som en andra parameter

Se syntaxfragment nedan:

```sql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Du kan hitta frågeexempel för åtkomst till element från upprepade kolumner i artikeln [Frågeparkettkapslade typer.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du frågar olika filtyper och skapar och använder vyer finns i följande artiklar:

- [Fråga en enda CSV-fil](query-single-csv-file.md)
- [Fråga parquet-filer](query-parquet-files.md)
- [Fråga JSON-filer](query-json-files.md)
- [Frågor Parkett kapslade typer](query-parquet-nested-types.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Använda filmetadata i frågor](query-specific-files.md)
- [Skapa och använda vyer](create-use-views.md)
