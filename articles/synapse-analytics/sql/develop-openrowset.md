---
title: Använda OpenRowSet i SQL på begäran (för hands version)
description: I den här artikeln beskrivs syntaxen för OpenRowSet i SQL på begäran (för hands version) och information om hur du använder argument.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c4b0c5277fb826780ff0c103f011c26049282672
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201489"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Använda OpenRowSet med SQL på begäran (för hands version)

`OPENROWSET(BULK...)`Funktionen gör att du kan komma åt filer i Azure Storage. `OPENROWSET`funktionen läser innehåll i en fjärrdatakälla (till exempel fil) och returnerar innehållet som en uppsättning rader. I SQL on-demand-resursen (för hands version) kan du komma åt OpenRowSet-providern för OpenRowSet genom att anropa funktionen OpenRowSet och ange alternativet för Mass rad uppsättning.  

`OPENROWSET`Funktionen kan refereras i- `FROM` satsen i en fråga som om den vore ett tabell namn `OPENROWSET` . Det stöder Mass åtgärder via en inbyggd Mass leverantör som gör det möjligt att läsa och returnera data från en fil som en rad uppsättning.

## <a name="data-source"></a>Datakälla

OpenRowSet-funktionen i Synapse SQL läser innehållet i filen/filerna från en data källa. Data källan är ett Azure Storage-konto och det kan uttryckligen refereras till i `OPENROWSET` funktionen eller kan dynamiskt härledas från URL: en för de filer som du vill läsa.
`OPENROWSET`Funktionen kan också innehålla en `DATA_SOURCE` parameter för att ange den data källa som innehåller filer.
- `OPENROWSET`utan `DATA_SOURCE` kan användas för att läsa innehållet i filerna direkt från den URL-plats som anges som `BULK` alternativ:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

Det här är ett snabbt och enkelt sätt att läsa innehållet i filerna utan för konfigurering. Med det här alternativet kan du använda alternativet grundläggande autentisering för att komma åt lagringen (Azure AD-genomströmning för Azure AD-inloggningar och SAS-token för SQL-inloggningar). 

- `OPENROWSET`med `DATA_SOURCE` kan användas för att komma åt filer på ett angivet lagrings konto:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```

    Med det här alternativet kan du konfigurera lagrings kontots plats i data källan och ange den autentiseringsmetod som ska användas för åtkomst till lagringen. 
    
    > [!IMPORTANT]
    > `OPENROWSET`utan `DATA_SOURCE` ger ett snabbt och enkelt sätt att komma åt lagringsfiler, men erbjuder alternativ för begränsad autentisering. Till exempel kan Azure AD-huvudobjektet endast komma åt filer med sin [Azure AD-identitet](develop-storage-files-storage-access-control.md#user-identity) och kan inte komma åt offentligt tillgängliga filer. Om du behöver mer kraftfulla autentiseringsalternativ använder du `DATA_SOURCE` alternativet och definierar de autentiseringsuppgifter som du vill använda för att komma åt lagringen.

## <a name="security"></a>Säkerhet

En databas användare måste ha `ADMINISTER BULK OPERATIONS` behörighet att använda `OPENROWSET` funktionen.

Lagrings administratören måste också göra det möjligt för en användare att komma åt filerna genom att tillhandahålla en giltig SAS-token eller aktivera Azure AD-huvudobjektet för åtkomst till lagringsfiler Läs mer om åtkomst kontroll för lagring i [den här artikeln](develop-storage-files-storage-access-control.md).

`OPENROWSET`Använd följande regler för att avgöra hur du ska autentisera till lagring:
- I `OPENROWSET` med `DATA_SOURCE` autentiseringsmekanismen är beroende av samtals typ.
  - AAD-inloggningar kan bara komma åt filer med sin egen [Azure AD-identitet](develop-storage-files-storage-access-control.md#user-identity) om Azure Storage gör det möjligt för Azure AD-användaren att komma åt underliggande filer (till exempel om anroparen har behörighet för lagrings läsare för lagring) och om du [aktiverar Azure AD passthrough-autentisering](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through) på Synapse SQL-tjänsten.
  - SQL-inloggningar kan också använda `OPENROWSET` utan `DATA_SOURCE` åtkomst till offentligt tillgängliga filer, filer som skyddas med SAS-token eller hanterad identitet för Synapse-arbetsytan. Du måste [skapa server-begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md#examples) för att tillåta åtkomst till lagringsfiler. 
- I `OPENROWSET` med autentiseringsmekanismen `DATA_SOURCE` definieras den autentiseringsuppgifter som tilldelats den refererade data källan i databasens begränsade autentiseringsuppgifter. Med det här alternativet kan du få åtkomst till offentligt tillgängligt lagrings utrymme, eller åtkomst till lagring med SAS-token, hanterad identitet för arbets ytan eller [Azure AD-identiteten](develop-storage-files-storage-access-control.md#user-identity) (om anroparen är Azure AD-huvudobjekt). Om `DATA_SOURCE` du refererar till Azure Storage som inte är offentligt måste du [skapa databasens begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md#examples) och referera till den i `DATA SOURCE` för att tillåta åtkomst till lagringsfiler.

Anroparen måste ha `REFERENCES` behörighet för autentiseringsuppgifter för att kunna använda den för att autentisera till lagring.

## <a name="syntax"></a>Syntax

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argument

Det finns två alternativ för indatafiler som innehåller mål data för frågor. Giltiga värden är:

- ' CSV ' – innehåller en avgränsad textfil med avgränsare för rad/kolumn. Valfritt tecken kan användas som fält avgränsare, till exempel TSV: FIELDTERMINATOR = tab.

- ' PARQUET '-binär fil i Parquet-format 

**unstructured_data_path**

Unstructured_data_path som upprättar en sökväg till data kan vara en absolut eller relativ sökväg:
- Absolut sökväg i formatet \< prefix>:// \< storage_account_path>/ \< storage_path> ' gör att en användare kan läsa filerna direkt.
- Relativ sökväg i formatet <storage_path> som måste användas med `DATA_SOURCE` parametern och beskriver fil mönstret i <storage_account_path> plats som definierats i `EXTERNAL DATA SOURCE` . 

 Nedan hittar du relevanta <storage account path> värden som länkar till din specifika externa data källa. 

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

I exemplet nedan, om unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , kommer en SQL på begäran-fråga att returnera rader från data. txt och _hidden. txt. Den returnerar inte mydata2. txt och mydata3. txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

MED WITH-satsen kan du ange kolumner som du vill läsa från filer.

- För CSV-datafiler, för att läsa alla kolumner, anger du kolumn namn och deras data typer. Om du vill använda en delmängd av kolumner använder du ordnings tal för att välja kolumner från de ursprungliga datafilerna enligt ordnings tal. Kolumnerna binds enligt ordnings beteckningen. 

    > [!IMPORTANT]
    > WITH-satsen är obligatorisk för CSV-filer.
    >
    
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

Anger den rad avslutning som ska användas. Om ingen rad avgränsare anges används en av standard avsluten. Standard avslutare för PARSER_VERSION = ' 1,0 ' är \r\n, \n och \r. Standard avslutare för PARSER_VERSION = ' 2,0 ' är \r\n och \n.

ESCAPE_CHAR = char

Anger det tecken i filen som används för att undanta sig själv och alla avgränsare värden i filen. Om Escape-symbolen följs av ett annat värde än sig själv, eller någon av avgränsarna, ignoreras escape-tecken vid läsning av värdet. 

Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktive rad. Den används inte för att undanta citat tecken. Citat tecken är avbrotts bara med dubbla citat tecken i justering med Excel CSV-beteende.

FIRSTROW = first_row 

Anger numret på den första raden som ska läsas in. Standard är 1. Detta anger den första raden i den angivna data filen. Rad numren bestäms genom att räkna rad avbrotten. FIRSTROW är 1-baserad.

FIELDQUOTE = field_quote 

Anger ett tecken som ska användas som citat tecken i CSV-filen. Om inget anges används citat tecknet ("). 

DATA_COMPRESSION = data_compression_method

Anger komprimerings metod. Följande komprimerings metod stöds:

- org. apache. Hadoop. io. compress. GzipCodec

PARSER_VERSION = parser_version

Anger vilken parser-version som ska användas vid läsning av filer. För närvarande finns versioner för CSV-parser som stöds 1,0 och 2,0

- PARSER_VERSION = ' 1,0 '
- PARSER_VERSION = ' 2,0 '

CSV-parser version 1,0 är standard och funktions rik, medan 2,0 har skapats för prestanda och inte stöder alla alternativ och kodningar. 

CSV-parser version 2,0-information:

- Det finns inte stöd för alla typer av data.
- Maximal rad storleks gräns är 8 MB.
- Följande alternativ stöds inte: DATA_COMPRESSION.
- En tom sträng med citat tecken ("") tolkas som en tom sträng.

## <a name="examples"></a>Exempel

I följande exempel returneras bara två kolumner med ordnings tal 1 och 4 från population*. csv-filerna. Eftersom det inte finns någon rubrik rad i filerna börjar den läsa från den första raden:

```sql
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
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

Om du får ett fel meddelande om att filerna inte kan visas måste du aktivera åtkomst till offentlig lagring i Synapse SQL på begäran:
- Om du använder en SQL-inloggning måste du [skapa server-begränsade autentiseringsuppgifter som ger åtkomst till offentlig lagring](develop-storage-files-storage-access-control.md#examples).
- Om du använder en Azure AD-huvudenhet för att komma åt offentlig lagring måste du [skapa autentiseringsuppgifter för Server omfång som ger åtkomst till offentlig lagring](develop-storage-files-storage-access-control.md#examples) och inaktivera [Azure AD passthrough-autentisering](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i snabb starten för [fråga data Storage](query-data-storage.md) för att lära dig hur du använder "OpenRowSet" för att läsa [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md)och [JSON](query-json-files.md) -filformat. Du kan också lära dig hur du sparar resultatet av din fråga till Azure Storage med [CETAS](develop-tables-cetas.md).
