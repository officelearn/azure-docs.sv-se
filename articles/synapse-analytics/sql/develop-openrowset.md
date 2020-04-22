---
title: Så här använder du OPENROWSET i SQL on-demand (förhandsversion)
description: I den här artikeln beskrivs syntaxen för OPENROWSET i SQL on-demand (preview) och hur du använder argument.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680498"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Så här använder du OPENROWSET med SQL on-demand (förhandsversion)

Med funktionen OPENROWSET(BULK...) kan du komma åt filer i Azure Storage. I sql on-demand -resursen (förhandsversion) används OPENROWSET-massradsetprovidern genom att anropa OPENROWSET-funktionen och ange massalternativet.  

Openrowset-funktionen kan refereras i FROM-satsen för en fråga som om det vore ett tabellnamn OPENROWSET. Den stöder massåtgärder via en inbyggd BULK-provider som gör att data från en fil kan läsas och returneras som en raduppsättning.

OPENROWSET stöds för närvarande inte i SQL-poolen.

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

Du har två alternativ för indatafiler som innehåller måldata för frågor. Giltiga värden är:

- CSV - Innehåller alla avgränsade textfiler med rad-/kolumnavgränsare. Alla tecken kan användas som fältavgränsare, till exempel TSV: FIELDTERMINATOR = på fliken .

- "PARKETT" - Binär fil i parkettformat 

**"unstructured_data_path"**

Den unstructured_data_path som upprättar en sökväg till data är strukturerad på följande sätt:  
"\<prefix>://\<storage_account_path>/\<storage_path>"
 
 
 Nedan hittar du relevanta sökvägar för lagringskonto som länkar till just din externa datakälla. 

| Extern datakälla       | Prefix | Sökväg till lagringskonto                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

"\<storage_path>"

 Anger en sökväg i lagringsutrymmet som pekar på mappen eller filen som du vill läsa. Om sökvägen pekar på en behållare eller mapp kommer alla filer att läsas från just den behållaren eller mappen. Filer i undermappar inkluderas inte. 
 
 Du kan använda jokertecken för att rikta in dig på flera filer eller mappar. Användning av flera icke-samtidiga jokertecken är tillåten.
Nedan är ett exempel som läser alla *CSV-filer* som börjar med *befolkning* från alla mappar som börjar med */ csv / befolkning:*  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Om du anger unstructured_data_path ska vara en mapp hämtas filer från mappen i en SQL on-demand-fråga. 

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar SQL on-demand inte undermappar. Till skillnad från Hadoop och PloyBase returnerar SQL on-demand filer som filnamnet börjar med en understrykning (_) eller en punkt (.).

Om unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`returnerar en SQL on-demand-fråga rader från mydata.txt och _hidden.txt i exemplet nedan. Det kommer inte att returnera mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Med satsen MED MED kan du ange kolumner som du vill läsa från filer.

- För CSV-datafiler, för att läsa alla kolumner, ange kolumnnamn och deras datatyper. Om du vill ha en delmängd av kolumner använder du ordningstal för att välja kolumnerna från de ursprungliga datafilerna efter ordningsman. Kolumnerna binds av ordningsbeteckningen. 

> [!IMPORTANT]
> WITH-satsen är obligatorisk för CSV-filer.
- För parettdatafiler anger du kolumnnamn som matchar kolumnnamnen i de ursprungliga datafilerna. Kolumnerna är bundna av namn. Om WITH-satsen utelämnas returneras alla kolumner från parquet-filer.

column_name = Namn på utdatakolumnen. Om det anges åsidosätter det här namnet kolumnnamnet i källfilen.

column_type = Datatyp för utdatakolumnen. Den implicita datatypkonverteringen kommer att äga rum här.

column_ordinal = Ordningstal för kolumnen i källfilerna. Detta argument ignoreras för parquet filer eftersom bindning görs med namn. I följande exempel returneras en andra kolumn endast från en CSV-fil:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Anger den fältslutare som ska användas. Standardtermineraren är ett kommatecken ("**,**").

ROWTERMINATOR ='row_terminator''

Anger den radslutare som ska användas. Standardslutaren för rad är ett nyradstecken som \r\n.

ESCAPE_CHAR = "röding"

Anger tecknet i filen som används för att undkomma sig själv och alla avgränsare i filen. Om escape-tecknet följs av ett annat värde än sig själv, eller något av avgränsarvärdena, tas escape-tecknet bort när värdet läses. 

Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktiverad. Det kommer inte att användas för att undkomma citattecken. Citattecknet escapes med dubbla citattecken i linje med Excel CSV-beteendet.

FIRSTROW = "first_row" 

Anger numret på den första raden som ska läsas in. Standard är 1. Detta indikerar den första raden i den angivna datafilen. Radnumren bestäms genom att räkna radavslutade. FIRSTROW är 1-baserad.

FIELDQUOTE = "field_quote" 

Anger ett tecken som ska användas som citattecken i CSV-filen. Om inget anges används citattecknet ("). 

## <a name="examples"></a>Exempel

I följande exempel returneras bara två kolumner med ordningstalen 1 och 4 från population*.csv-filerna. Eftersom det inte finns någon rubrikrad i filerna börjar den läsa från den första raden:

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



I följande exempel returneras alla kolumner på den första raden från folkräkningsdatauppsättningen i parkettformat utan att ange kolumnnamn och datatyper: 

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

Fler exempel finns i [snabbstarter](query-data-storage.md) eller spara resultatet av din fråga i Azure Storage med [CETAS](develop-tables-cetas.md).
