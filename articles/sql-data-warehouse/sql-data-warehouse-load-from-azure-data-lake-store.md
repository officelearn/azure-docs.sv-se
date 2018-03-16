---
title: "Läs in - Azure Data Lake Store till SQL Data Warehouse | Microsoft Docs"
description: "Lär dig använda PolyBase externa tabeller för att läsa in data från Azure Data Lake Store i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 3/14/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: f8cd293236255e227f80a42e78d25aebd8789bdd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Läs in data från Azure Data Lake Store till SQL Data Warehouse
Det här dokumentet får du alla steg som du behöver läsa in data från Azure Data Lake Store (ADLS) i SQL Data Warehouse med PolyBase.
När du ska kunna köra ad hoc-frågor över data som lagras i ADLS med hjälp av externa tabeller rekommenderar vi att importera data till SQL Data Warehouse för bästa prestanda.

I den här kursen får du lära dig hur du:

1. Skapa databasobjekt som krävs för att läsa in från Azure Data Lake Store.
2. Ansluta till ett Azure Data Lake Store-katalogen.
3. Läs in data till Azure SQL Data Warehouse.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill köra den här kursen behöver du:

* Azure Active Directory-program ska användas för autentisering för tjänst-till-tjänst. Så här skapar du [Active directory-autentisering](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> Du behöver klient-ID, nyckel och OAuth2.0 Token Endpoint-värdet för din Active Directory-program för att ansluta till Azure Data Lake från SQL Data Warehouse. Information om hur du hämtar dessa värden finns i länken ovan.
>Obs för registrering av Azure Active Directory App använder programmet ID som klient-ID.

* SQL Server Management Studio eller SQL Server Data Tools för att hämta SSMS och ansluta finns [fråga SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Ett Azure SQL Data Warehouse, skapa en Följ: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* Ett Azure Data Lake Store, skapa en Följ: https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal


###  <a name="create-a-credential"></a>Skapa en autentiseringsuppgift
För att komma åt din Azure Data Lake Store, behöver du skapa en huvudnyckel för databasen om du vill kryptera dina autentiseringsuppgifter hemlighet som används i nästa steg.
Sedan kan du skapa en Databasomfattande autentisering, som lagrar huvudnamn autentiseringsuppgifterna för tjänsten i AAD. Observera att credential-syntax är olika för de som har använt PolyBase för att ansluta till Windows Azure Storage-Blobbar.
Om du vill ansluta till Azure Data Lake Store, måste du **första** skapa ett Azure Active Directory-program, skapa en snabbtangent och ge programmet åtkomst till Azure Data Lake-resursen. Anvisningar för att utföra de här stegen finns [här](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```


### <a name="create-the-external-data-source"></a>Skapa den externa datakällan
Använd den här [Skapa extern DATAKÄLLA] [ CREATE EXTERNAL DATA SOURCE] kommando för att lagra platsen för data. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Konfigurera dataformat
Du måste ange det externa filformatet om du vill importera data från ADLS. Det här objektet definierar hur filerna skrivs i ADLS.
Titta på vår T-SQL-dokumentation för en fullständig lista [skapa externt FILFORMAT][CREATE EXTERNAL FILE FORMAT]

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Skapa externa tabeller
Nu när du har angett käll- och dataformatet, är du redo att skapa externa tabeller. Externa tabeller är hur du interagerar med externa data. Platsparametern kan ange en fil eller katalog. Om det anger en katalog, laddas alla filer i katalogen.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Överväganden för extern tabell
Är enkelt att skapa en extern tabell, men det finns några olika delarna som behöver tas upp.

Externa tabeller har strikt typkontroll. Detta innebär att varje rad med data som inhämtas måste uppfylla tabellschemadefinitionen.
Om en rad inte matchar schemadefinitionen avvisas raden från belastningen.

Alternativen REJECT_TYPE och REJECT_VALUE kan du definiera hur många rader eller vilken procentandel av data måste finnas i den slutliga tabellen. Om värdet avvisa uppnås under belastning misslyckas belastningen. Den vanligaste orsaken Avvisade rader stämmer inte schemat definition. Till exempel om en kolumn anges felaktigt schemat för int när data i filen är en sträng varje rad inte laddas.

 Azure Data Lake store använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till data. Detta innebär att tjänstens huvudnamn måste ha läsbehörighet till kataloger som anges i platsparametern och underordnade slutliga katalogen och filerna. Detta gör att PolyBase för att autentisera och läsa in data. 

## <a name="load-the-data"></a>Läs in data
Att läsa in data från Azure Data Lake Store användning av [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] instruktionen. 

CTAS skapar en ny tabell och fylls med resultatet av en select-instruktion. CTAS definierar den nya tabellen om du vill ha samma kolumner och datatyper som resultat av select-satsen. Om du väljer alla kolumner från en extern tabell är en replik för kolumner och datatyperna i den externa tabellen i den nya tabellen.

I det här exemplet skapar vi en distribuerad hash-tabell som kallas DimProduct från våra extern tabell DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimera columnstore komprimering
Som standard lagrar SQL Data Warehouse tabellen som ett grupperat columnstore-index. När en belastning är klar, kan vissa av datarader inte komprimeras i columnstore.  Det finns många olika orsaker till detta kan inträffa. Läs mer i [hantera kolumnlagringsindex][manage columnstore indexes].

Återskapa tabellen om du vill framtvinga kolumnlagringsindexet att komprimera alla rader för att optimera prestanda för frågor och columnstore komprimering efter en belastning.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Mer information om hur du underhåller columnstore-index finns i [hantera kolumnlagringsindex] [ manage columnstore indexes] artikel.

## <a name="optimize-statistics"></a>Optimera statistik
Det är bäst att skapa enkolumns-statistik omedelbart efter en belastning. Det finns vissa alternativ för statistik. Om du skapar enkolumns-statistik på alla kolumner kan det ta lång tid att återskapa all statistik. Om du vet att vissa kolumner inte ska vara i frågan predikat kan du hoppa över att skapa statistik på de kolumnerna.

Om du vill skapa enkolumns-statistik för varje kolumn i varje tabell kan du använda lagrade proceduren kodexemplet `prc_sqldw_create_stats` i den [statistik] [ statistics] artikel.

I följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar enkolumns-statistik för varje kolumn i dimensionstabellen och på varje anslutande kolumn i faktatabellerna. Du kan alltid lägga till en eller flera kolumner statistik andra fakta tabellkolumner vid ett senare tillfälle.


## <a name="achievement-unlocked"></a>Uppnå låsas upp!
Data har lästs in Azure SQL Data Warehouse. Bra jobbat!

## <a name="next-steps"></a>Nästa steg
Data läses in är det första steget för att utveckla en lösning för data warehouse med hjälp av SQL Data Warehouse. Kolla in våra development resurser på [tabeller](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) och [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops).


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
