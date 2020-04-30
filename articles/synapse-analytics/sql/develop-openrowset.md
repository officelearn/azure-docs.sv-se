---
title: Använda OpenRowSet i SQL på begäran (för hands version)
description: I den här artikeln beskrivs syntaxen för OpenRowSet i SQL på begäran (för hands version) och information om hur du använder argument.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680498"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Använda OpenRowSet med SQL på begäran (för hands version)

Funktionen OpenRowSet (Mass...) gör att du kan komma åt filer i Azure Storage. I SQL on-demand-resursen (för hands version) kan du komma åt OpenRowSet-providern för OpenRowSet genom att anropa funktionen OpenRowSet och ange alternativet för Mass rad uppsättning.  

Funktionen OpenRowSet kan refereras i FROM-satsen i en fråga som om den vore ett tabell namn OpenRowSet. Det stöder Mass åtgärder via en inbyggd Mass leverantör som gör det möjligt att läsa och returnera data från en fil som en rad uppsättning.

OpenRowSet stöds inte för närvarande i SQL-poolen.

## <a name="syntax"></a>Syntax

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argument

Det finns två alternativ för indatafiler som innehåller mål data för frågor. Giltiga värden är:

- ' CSV ' – innehåller en avgränsad textfil med avgränsare för rad/kolumn. Valfritt tecken kan användas som fält avgränsare, till exempel TSV: FIELDTERMINATOR = tab.

- ' PARQUET '-binär fil i Parquet-format 

**unstructured_data_path**

Unstructured_data_path som upprättar en sökväg till data struktureras enligt följande:  
\<prefixet>://\<storage_account_path>/\<storage_path>
 
 
 Nedan hittar du relevanta sökvägar för lagrings konton som länkar till din specifika externa data källa. 

| Extern data Källa       | Prefix | Sökväg till lagrings konto                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>. blob.core.windows.net             |
| Azure Data Lake Store gen1 | https  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>. dfs.core.windows.net              |
||||

\<storage_path>

 Anger en sökväg i lagrings utrymmet som pekar på den mapp eller fil som du vill läsa. Om sökvägen pekar på en behållare eller mapp kommer alla filer att läsas från den aktuella behållaren eller mappen. Filer i undermappar tas inte med. 
 
 Du kan använda jokertecken för att rikta in flera filer eller mappar. Användning av flera jokertecken som inte är i följd tillåts.
Nedan visas ett exempel som läser alla *CSV* -filer som börjar med *ifyllning* från alla mappar som börjar med */CSV/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Om du anger att unstructured_data_path ska vara en mapp, kommer en fråga om SQL på begäran att hämta filer från den mappen. 

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar SQL on-demand inte undermappar. Till skillnad från Hadoop och PloyBase returnerar till exempel SQL on-demand filer för vilka fil namnet börjar med en understrykning (_) eller en punkt (.).

I exemplet nedan, om unstructured_data_path =`https://mystorageaccount.dfs.core.windows.net/webdata/`, kommer en SQL på begäran-fråga att returnera rader från data. txt och _hidden. txt. Den returnerar inte mydata2. txt och mydata3. txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

MED WITH-satsen kan du ange kolumner som du vill läsa från filer.

- För CSV-datafiler, för att läsa alla kolumner, anger du kolumn namn och deras data typer. Om du vill använda en delmängd av kolumner använder du ordnings tal för att välja kolumner från de ursprungliga datafilerna enligt ordnings tal. Kolumnerna binds enligt ordnings beteckningen. 

> [!IMPORTANT]
> WITH-satsen är obligatorisk för CSV-filer.
- För Parquet-datafiler, ange kolumn namn som matchar kolumn namnen i de ursprungliga datafilerna. Kolumnerna kommer att bindas efter namn. Om WITH-satsen utelämnas returneras alla kolumner från Parquet-filer.

column_name = namn för kolumnen utdata. Om det här namnet anges åsidosätts kolumn namnet i käll filen.

column_type = datatyp för kolumnen utdata. Konvertering av implicit datatyp sker här.

column_ordinal = ordnings talet för kolumnen i käll filen/källfilerna. Det här argumentet ignoreras för Parquet-filer eftersom bindningen görs efter namn. I följande exempel returneras endast en andra kolumn från en CSV-fil:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR = field_terminator

Anger vilken fält avslutning som ska användas. Standard fält avslutning är ett kommatecken ("**,**").

ROWTERMINATOR = row_terminator

Anger den rad avslutning som ska användas. Standard rads avgränsaren är ett rad matnings tecken, till exempel \r\n.

ESCAPE_CHAR = char

Anger det tecken i filen som används för att undanta sig själv och alla avgränsare värden i filen. Om Escape-symbolen följs av ett annat värde än sig själv, eller någon av avgränsarna, ignoreras escape-tecken vid läsning av värdet. 

Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktive rad. Den används inte för att undanta citat tecken. Citat tecken är avbrotts bara med dubbla citat tecken i justering med Excel CSV-beteende.

FIRSTROW = first_row 

Anger numret på den första raden som ska läsas in. Standard är 1. Detta anger den första raden i den angivna data filen. Rad numren bestäms genom att räkna rad avbrotten. FIRSTROW är 1-baserad.

FIELDQUOTE = field_quote 

Anger ett tecken som ska användas som citat tecken i CSV-filen. Om inget anges används citat tecknet ("). 

## <a name="examples"></a>Exempel

I följande exempel returneras bara två kolumner med ordnings tal 1 och 4 från population*. csv-filerna. Eftersom det inte finns någon rubrik rad i filerna börjar den läsa från den första raden:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



I följande exempel returneras alla kolumner för den första raden från data uppsättningen för inventering i Parquet-format utan att ange kolumn namn och data typer: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [snabb starter](query-data-storage.md) eller spara resultatet av din fråga för att Azure Storage med [CETAS](develop-tables-cetas.md).
