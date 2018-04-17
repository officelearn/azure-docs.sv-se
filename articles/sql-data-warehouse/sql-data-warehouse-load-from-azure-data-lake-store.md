---
title: 'Självstudier: Hämta från Azure Data Lake Store till Azure SQL Data Warehouse | Microsoft Docs'
description: Använd PolyBase externa tabeller för att läsa in data från Azure Data Lake Store i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 3c6907e8eb4ae4bbfae76a5a220d670427afd703
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Läs in data från Azure Data Lake Store till SQL Data Warehouse
Använd PolyBase externa tabeller för att läsa in data från Azure Data Lake Store i Azure SQL Data Warehouse. Även om du kan köra ad hoc-frågor på data som lagras i ADLS, rekommenderar vi att importera data till SQL Data Warehouse för bästa prestanda.

> [!div class="checklist"]
> * Skapa databasobjekt som krävs för att läsa in från Azure Data Lake Store.
> * Ansluta till ett Azure Data Lake Store-katalogen.
> * Läs in data till Azure SQL Data Warehouse.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar
Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Om du vill köra den här kursen behöver du:

* Azure Active Directory-program ska användas för autentisering för tjänst-till-tjänst. Så här skapar du [Active directory-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Du behöver klient-ID, nyckel och OAuth2.0 Token Endpoint-värdet för din Active Directory-program för att ansluta till Azure Data Lake från SQL Data Warehouse. Information om hur du hämtar dessa värden finns i länken ovan. Använda program-ID för Azure Active Directory Appregistrering som klient-ID.
> 

* Azure SQL Data Warehouse. Se [skapa och fråga och Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Ett Azure Data Lake Store. Se [Kom igång med Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Skapa en autentiseringsuppgift
För att komma åt din Azure Data Lake Store, behöver du skapa en huvudnyckel för databasen om du vill kryptera dina autentiseringsuppgifter hemlighet som används i nästa steg. Sedan kan du skapa en Databasomfattande autentisering, som lagrar huvudnamn autentiseringsuppgifterna för tjänsten i AAD. Observera att credential-syntax är olika för de som har använt PolyBase för att ansluta till Windows Azure Storage-Blobbar.

Om du vill ansluta till Azure Data Lake Store, måste du **första** skapa ett Azure Active Directory-program, skapa en snabbtangent och ge programmet åtkomst till Azure Data Lake-resursen. Instruktioner finns i [autentisera till Azure Data Lake Store med hjälp av Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

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

## <a name="create-the-external-data-source"></a>Skapa den externa datakällan
Använd den här [Skapa extern DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql) kommando för att lagra platsen för data. 

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
Titta på vår T-SQL-dokumentation för en fullständig lista [skapa externt FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Att läsa in data från Azure Data Lake Store användning av [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instruktionen. 

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
Som standard lagrar SQL Data Warehouse tabellen som ett grupperat columnstore-index. När en belastning är klar, kan vissa av datarader inte komprimeras i columnstore.  Det finns många olika orsaker till detta kan inträffa. Läs mer i [hantera kolumnlagringsindex](sql-data-warehouse-tables-index.md).

Återskapa tabellen om du vill framtvinga kolumnlagringsindexet att komprimera alla rader för att optimera prestanda för frågor och columnstore komprimering efter en belastning.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik
Det är bäst att skapa enkolumns-statistik omedelbart efter en belastning. Det finns vissa alternativ för statistik. Om du skapar enkolumns-statistik på alla kolumner kan det ta lång tid att återskapa all statistik. Om du vet att vissa kolumner inte ska vara i frågan predikat kan du hoppa över att skapa statistik på de kolumnerna.

Om du vill skapa enkolumns-statistik för varje kolumn i varje tabell kan du använda lagrade proceduren kodexemplet `prc_sqldw_create_stats` i den [statistik](sql-data-warehouse-tables-statistics.md) artikel.

I följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar enkolumns-statistik för varje kolumn i dimensionstabellen och på varje anslutande kolumn i faktatabellerna. Du kan alltid lägga till en eller flera kolumner statistik andra fakta tabellkolumner vid ett senare tillfälle.

## <a name="achievement-unlocked"></a>Uppnå låsas upp!
Data har lästs in Azure SQL Data Warehouse. Bra jobbat!

## <a name="next-steps"></a>Nästa steg 
I den här självstudiekursen skapade externa tabeller för att definiera strukturen för data som lagras i Azure Data Lake Store och sedan används PolyBase CREATE TABLE AS SELECT-instruktion för att läsa in data till datalagret. 

Du gjorde detta:
> [!div class="checklist"]
> * Skapade databasobjekt som krävs för att läsa in från Azure Data Lake Store.
> * Ansluten till ett Azure Data Lake Store-katalogen.
> * Läsa in data till Azure SQL Data Warehouse.
> 

Data läses in är det första steget för att utveckla en lösning för data warehouse med hjälp av SQL Data Warehouse. Kolla in våra development resurser.

> [!div class="nextstepaction"]
>[Lär dig hur du utvecklar tabeller i SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




