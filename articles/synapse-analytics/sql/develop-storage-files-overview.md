---
title: Fråga Storage-filer med SQL on-demand (för hands version) i Synapse SQL
description: Beskriver hur du frågar lagrings filer med hjälp av SQL on-demand-resurser (för hands version) i Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bfea79fe232fbb6f1b39c03a5cc8e9fe06bee867
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204947"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Fråga Storage-filer med hjälp av SQL on-demand-resurser (för hands version) i Synapse SQL

SQL på begäran (för hands version) gör det möjligt att fråga efter data i data Lake. Den innehåller en fråge yta för T-SQL-frågor som hanterar halv strukturerade och ostrukturerade data frågor.

För frågor stöds följande T-SQL-aspekter:

- Fullständigt [val](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) av yta, inklusive majoriteten av SQL functions, operatorer och så vidare.
- Skapa extern tabell som SELECT ([CETAS](develop-tables-cetas.md)) skapar en [extern tabell](develop-tables-external-tables.md) och sedan exporterar parallellt resultatet av en Transact-SQL SELECT-instruktion till Azure Storage.

Mer information om vad är vs. vad som inte stöds för närvarande finns i [översikts artikeln om SQL på begäran](on-demand-workspace-overview.md) .

När Azure AD-användare kör frågor är standardvärdet för lagrings konton som ska nås med hjälp av Azure AD-vidarekoppling. Det innebär att användarna personifieras och att behörigheter kontrol leras på lagrings nivå. Du kan [kontrol lera lagrings åtkomsten](develop-storage-files-storage-access-control.md) så att den passar dina behov.

## <a name="extensions"></a>Tillägg

För att ge stöd för en smidig upplevelse för att skicka frågor till data som finns i Azure Storage filer, använder SQL på begäran funktionen [OpenRowSet](develop-openrowset.md) med ytterligare funktioner:

- [Fråga efter flera filer eller mappar](#query-multiple-files-or-folders)
- [PARQUET-filformat](#parquet-file-format)
- [Ytterligare alternativ för att arbeta med avgränsad text (fält avslutning, rad avslutning, escape-tecken)](#additional-options-for-working-with-delimited-text)
- [Läs en vald delmängd av kolumner](#read-a-chosen-subset-of-columns)
- [Schema härledning](#schema-inference)
- [funktionen filename](#filename-function)
- [funktionen filsökväg](#filepath-function)
- [Arbeta med komplexa typer och kapslade eller upprepade data strukturer](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Fråga efter flera filer eller mappar

Om du vill köra en T-SQL-fråga över en uppsättning filer i en mapp eller en uppsättning mappar samtidigt som du behandlar dem som en enda enhet eller rad uppsättning, anger du en sökväg till en mapp eller ett mönster (med jokertecken) över en uppsättning filer eller mappar.

Följande regler gäller:

- Mönster kan visas antingen som en del av en katalog Sök väg eller i ett fil namn.
- Flera mönster kan visas i samma katalog steg eller fil namn.
- Om det finns flera jokertecken tas filer i alla matchande sökvägar med i den resulterande fil uppsättningen.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Se [fråge mappar och flera filer](query-folders-multiple-csv-files.md) för användnings exempel.

### <a name="parquet-file-format"></a>PARQUET-filformat

Om du vill fråga Parquet-källdata använder du FORMAT = ' PARQUET '

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Granska artikeln [query Parquet-filer](query-parquet-files.md) för användnings exempel.

### <a name="additional-options-for-working-with-delimited-text"></a>Ytterligare alternativ för att arbeta med avgränsad text

Dessa ytterligare parametrar införs för att arbeta med CSV-filer (avgränsad text):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = char anger det tecken i filen som används för att undanta sig själv och alla avgränsare värden i filen. Om escape-tecken följs av antingen ett annat värde än sig själv eller någon av avgränsarna, ignoreras escape-tecken vid läsning av värdet.
Parametern ESCAPE_CHAR tillämpas om FIELDQUOTE är eller inte är aktive rad. Den används inte för att undanta citat tecken. Citat tecken måste föregås av ett annat citat tecken. Citat tecken får bara förekomma i kolumn värden om värdet är kapslat med citat tecken.
- FIELDTERMINATOR = ' field_terminator ' anger vilken fält avslutning som ska användas. Standard fält avslutning är ett kommatecken ("**,**")
- ROWTERMINATOR = ' row_terminator ' anger rad avslutningen som ska användas. Standard rads avgränsaren är ett rad matnings tecken: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Läs en vald delmängd av kolumner

Om du vill ange kolumner som du vill läsa kan du ange en valfri WITH-sats i OpenRowSet-instruktionen.

- Om det finns CSV-datafiler kan du läsa alla kolumner genom att ange kolumn namn och deras data typer. Om du vill använda en delmängd av kolumner använder du ordnings tal för att välja kolumner från de ursprungliga datafilerna enligt ordnings tal. Kolumnerna binds enligt ordnings beteckningen.
- Om det finns Parquet-datafiler, anger du kolumn namn som matchar kolumn namnen i de ursprungliga datafilerna. Kolumnerna kommer att bindas efter namn.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Exempel finns i [läsa CSV-filer utan att ange alla kolumner](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Schema härledning

Genom att utelämna satsen WITH från OpenRowSet kan du instruera tjänsten att automatiskt identifiera (Härled) schemat från underliggande filer.

> [!NOTE]
> Detta fungerar för närvarande endast för PARQUET-filformat.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Kontrol lera att [rätt härledda data typer](best-practices-sql-on-demand.md#check-inferred-data-types) används för optimala prestanda. 

### <a name="filename-function"></a>Funktionen filename

Den här funktionen returnerar fil namnet som raden kommer från. 

Om du vill fråga efter vissa filer läser du avsnittet filename i artikeln [query Specific Files](query-specific-files.md#filename) .

Retur data typen är nvarchar (1024). För bästa prestanda måste du alltid omvandla resultatet av fil namns funktionen till rätt datatyp. Om du använder tecken data typ ser du till att lämplig längd används.

### <a name="filepath-function"></a>Funktionen filsökväg

Den här funktionen returnerar en fullständig sökväg eller en del av sökvägen:

- När den anropas utan parameter returnerar den fullständiga fil Sök vägen som en rad kommer från.
- När den anropas med parameter, returnerar den en del av sökvägen som matchar jokertecknet i den position som anges i parametern. Parameter värde 1 skulle till exempel returnera en del av sökvägen som matchar det första jokertecknet.

Mer information finns i avsnittet sökväg i artikeln [query Specific Files](query-specific-files.md#filepath) .

Retur data typen är nvarchar (1024). För bästa prestanda måste du alltid skicka resultatet av funktionen fil Sök väg till rätt datatyp. Om du använder tecken data typ ser du till att lämplig längd används.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Arbeta med komplexa typer och kapslade eller upprepade data strukturer

Om du vill aktivera en smidig upplevelse när du arbetar med data som lagras i kapslade eller upprepade data typer, t. ex. i [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) -filer har SQL på begäran lagt till tilläggen nedan.

#### <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

Om du vill projicera data kör du ett SELECT-uttryck över Parquet-filen som innehåller kolumner med kapslade data typer. Vid utdata serialiseras kapslade värden i JSON och returneras som en SQL-datatyp av typen VARCHAR (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Mer detaljerad information finns i avsnittet projekt-kapslade eller upprepade data i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#project-nested-or-repeated-data) .

#### <a name="access-elements-from-nested-columns"></a>Få åtkomst till element från kapslade kolumner

Om du vill komma åt kapslade element från en kapslad kolumn, till exempel struct, använder du "punkt notation" för att sammanfoga fält namn i sökvägen. Ange sökvägen som column_name i WITH-satsen i funktionen OpenRowSet.

Exempel på syntaxfel är följande:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Som standard matchar OpenRowSet-funktionen käll fält namn och sökväg med kolumn namnen som anges i WITH-satsen. Element som finns på olika kapslings nivåer i samma käll Parquet-fil kan nås via WITH-satsen.

**Returvärden**

- Funktionen returnerar ett skalärt värde, till exempel int, decimal och varchar, från det angivna elementet och på den angivna sökvägen för alla Parquet typer som inte finns i den kapslade typ gruppen.
- Om sökvägen pekar på ett element som är av en kapslad typ returnerar funktionen ett JSON-fragment som börjar från det översta elementet på den angivna sökvägen. JSON-fragmentet är av typen VARCHAR (8000).
- Om egenskapen inte kan hittas på den angivna column_name, returnerar funktionen ett fel.
- Om egenskapen inte kan hittas på den angivna column_path, beroende på [Sök vägs läge](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), returnerar funktionen ett fel när det är i strikt läge eller null i lax-läge.

Läs avsnittet åtkomst element från kapslade kolumner i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#access-elements-from-nested-columns) för fråge exempel.

#### <a name="access-elements-from-repeated-columns"></a>Få åtkomst till element från upprepade kolumner

För att få åtkomst till element från en upprepad kolumn, t. ex. ett element i en matris eller karta, använder du funktionen [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för varje skalärt element som du behöver för att projicera och tillhandahålla:

- Kapslad eller upprepad kolumn som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som anger det element eller den egenskap som ska kommas åt, som en andra parameter

Om du vill komma åt icke-skalära element från en upprepad kolumn använder du funktionen [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för varje icke-skalärt element som du behöver för att projicera och tillhandahålla:

- Kapslad eller upprepad kolumn som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som anger det element eller den egenskap som ska kommas åt, som en andra parameter

Se syntaxen nedan:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Du kan hitta exempel på frågor för att komma åt element från upprepade kolumner i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#access-elements-from-repeated-columns) .

## <a name="next-steps"></a>Nästa steg

Mer information om hur du frågar olika filtyper och skapar och använder vyer finns i följande artiklar:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Använda fil-metadata i frågor](query-specific-files.md)
- [Skapa och använda vyer](create-use-views.md)
