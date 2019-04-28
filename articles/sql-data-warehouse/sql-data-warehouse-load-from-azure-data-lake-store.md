---
title: 'Självstudier: Belastningen från Azure Data Lake Storage Gen1 till Azure SQL Data Warehouse | Microsoft Docs'
description: Använd PolyBase externa tabeller för att läsa in data från Azure Data Lake Storage Gen1 till Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 32ac5b0841365acfc0a52e343eafc4f3760dffaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476144"
---
# <a name="load-data-from-azure-data-lake-storage-gen1-to-sql-data-warehouse"></a>Läsa in data från Azure Data Lake Storage Gen1 till SQL Data Warehouse
Använd PolyBase externa tabeller för att läsa in data från Azure Data Lake Storage Gen1 till Azure SQL Data Warehouse. Även om du kan köra ad hoc-frågor på data som lagras i Data Lake Storage Gen1, rekommenderar vi importerar data till SQL Data Warehouse för bästa prestanda.

> [!div class="checklist"]
> * Skapa databasobjekt som krävs för att läsa in från Data Lake Storage Gen1.
> * Anslut till en Data Lake Storage Gen1 katalog.
> * Läs in data till Azure SQL Data Warehouse.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar
Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Om du vill köra den här självstudien behöver du:

* Azure Active Directory-program för tjänst-till-tjänst-autentisering. Så här skapar du [Active directory-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Du behöver det klient-ID och nyckel OAuth2.0 Token slutpunktsvärdet för din Active Directory-programmet för att ansluta till ditt Data Lake Storage Gen1-konto från SQL Data Warehouse. Information om hur du hämtar dessa värden finns i länken ovan. Använda program-ID för Azure Active Directory-Appregistrering som klient-ID.
> 

* Ett Azure SQL Data Warehouse. Se [skapa och fråga och Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Ett Data Lake Storage Gen1-konto. Se [Kom igång med Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Skapa en autentiseringsuppgift
Tillgång till ditt Data Lake Storage Gen1-konto, behöver du skapa en databashuvudnyckel för att kryptera din autentiseringshemligheten som används i nästa steg. Du kan sedan skapa en Database Scoped Credential, som lagrar autentiseringsuppgifterna för tjänstobjektet i AAD. Observera att credential-syntax är olika för dig som har använt PolyBase för att ansluta till Windows Azure Storage-Blobbar.

Om du vill ansluta till Data Lake Storage Gen1, måste du **första** skapa ett Azure Active Directory-program, skapa en åtkomstnyckel och ge programmet åtkomst till Data Lake Storage Gen1 resursen. Anvisningar finns i [autentisera till Azure Data Lake Storage Gen1 med hjälp av Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Skapa extern datakälla
Använd det här [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) kommando för att lagra platsen för dina data. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage Gen1.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorageGen1
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSG1Credential
);
```

## <a name="configure-data-format"></a>Konfigurera dataformat
Om du vill importera data från Data Lake Storage Gen1, måste du ange det externa filformatet. Det här objektet definierar hur filerna som är skrivna i Data Lake Storage Gen1.
Titta på vår T-SQL-dokumentation för en fullständig lista [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Nu när du har angett dataformatet för käll- och filen, är du redo att skapa externa tabeller. Externa tabeller är hur du interagerar med externa data. Parametern plats kan ange en fil eller katalog. Om det anger en katalog, laddas alla filer i katalogen.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage Gen1 root folder.
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
,   DATA_SOURCE = AzureDataLakeStorageGen1
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Extern tabell överväganden
Det är enkelt att skapa en extern tabell, men det finns några olika delarna som behöver diskuteras.

Externa tabeller är starkt typbestämd. Det innebär att varje rad med data som matas in måste uppfylla tabellschemadefinitionen.
Om en rad inte matchar schemadefinitionen avvisade raden från belastningen.

Alternativen REJECT_TYPE och REJECT_VALUE kan du definiera hur många rader eller vilken procentandel av data måste finnas i den slutliga tabellen. Om värdet för avvisa har nåtts under belastning misslyckas belastningen. Den vanligaste orsaken för avvisade raden är en felaktig schemamatchning definition. Till exempel om en kolumn är felaktigt schemat för int när data i filen är en sträng varje rad inte laddas.

Data Lake Storage Gen1 använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till data. Det innebär att tjänstens huvudnamn måste ha läsbehörighet till kataloger som definierats i parametern plats och underordnade grupper till sista katalogen och filerna. På så sätt kan PolyBase för att autentisera och läsa in data. 

## <a name="load-the-data"></a>Läs in data
Att läsa in data från Data Lake Storage Gen1 användning den [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instruktionen. 

CTAS skapar en ny tabell och fylla den med resultatet av en select-instruktion. CTAS definierar den nya tabellen om du vill ha samma kolumner och datatyper som resultatet av select-instruktionen. Om du väljer alla kolumner från en extern tabell är en replik av kolumner och datatyper i den externa tabellen i den nya tabellen.

I det här exemplet skapar vi en distribuerad hash-tabell som heter DimProduct från våra extern tabell DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering
Som standard lagrar SQL Data Warehouse tabellen som ett grupperat kolumnlagringsindex. När en belastning är klar, kan vissa av datarader inte komprimeras till columnstore.  Det finns en mängd orsaker till varför detta kan inträffa. Mer information finns i [hantera kolumnlagringsindex](sql-data-warehouse-tables-index.md).

Återskapa tabellen om du vill framtvinga kolumnlagringsindexet Komprimera alla rader för att optimera prestanda för frågor och columnstore-komprimering efter en belastning.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik
Det är bäst att skapa enkolumns-statistik direkt efter en belastning. Det finns några alternativ för statistik. Om du skapar enkolumns-statistik för varje kolumn kan det ta lång tid att återskapa all statistik. Om du vet att vissa kolumner som inte kommer att vara i fråga predikat, kan du hoppa över skapa statistik på dessa kolumner.

Om du vill skapa enkolumns-statistik för varje kolumn för varje tabell som du kan använda lagrade proceduren kodexemplet `prc_sqldw_create_stats` i den [statistik](sql-data-warehouse-tables-statistics.md) artikeln.

I följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar enkolumns-statistik för varje kolumn i dimensionstabellen och på varje sammanbinder kolumn i faktatabellerna. Du kan alltid lägga till en eller flera kolumner statistik till andra fakta tabellkolumner vid ett senare tillfälle.

## <a name="achievement-unlocked"></a>Prestation låsas upp!
Du har har läst in data i Azure SQL Data Warehouse. Bra jobbat!

## <a name="next-steps"></a>Nästa steg 
I den här självstudien skapade externa tabeller för att definiera strukturen för data som lagras i Data Lake Storage Gen1 och sedan används PolyBase CREATE TABLE AS SELECT-instruktion för att läsa in data i ditt informationslager. 

Du gjorde detta:
> [!div class="checklist"]
> * Skapade databasobjekt som krävs för att läsa in från Data Lake Storage Gen1.
> * Ansluten till en Data Lake Storage Gen1 katalog.
> * Inlästa data till Azure SQL Data Warehouse.
> 

Läser in data är det första steget för att utveckla en lösning för informationslager med hjälp av SQL Data Warehouse. Kolla in våra Utvecklingsresurser.

> [!div class="nextstepaction"]
>[Lär dig hur du utvecklar tabeller i SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




