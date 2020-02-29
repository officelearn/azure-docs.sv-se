---
title: Självstudier läsa in data från Azure Data Lake Storage
description: Använd PolyBase-externa tabeller för att läsa in data från Azure Data Lake Storage för SQL Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9a567a8f62f8f12de725f6d9420576680a3005fe
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194588"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Läs in data från Azure Data Lake Storage för SQL Analytics
Den här guiden beskriver hur du använder PolyBase-externa tabeller för att läsa in data från Azure Data Lake Storage. Även om du kan köra adhoc-frågor på data som lagras i Data Lake Storage rekommenderar vi att du importerar data för bästa prestanda. 

> [!NOTE]  
> Ett alternativ till att läsa in är [kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) för närvarande i offentlig för hands version.  KOPIERINGs instruktionen ger störst flexibilitet. Om du vill ge feedback om KOPIERINGs instruktionen skickar du ett e-postmeddelande till följande distributions lista: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Skapa databas objekt som krävs för att läsa in från Data Lake Storage.
> * Anslut till en Data Lake Storage katalog.
> * Läs in data i data lagret.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar
Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

För att kunna köra den här självstudien behöver du:

* En SQL-pool. Se [skapa en SQL-pool och fråga efter data](create-data-warehouse-portal.md).
* Ett Data Lake Storage konto. Se [Kom igång med Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). För det här lagrings kontot måste du konfigurera eller ange någon av följande autentiseringsuppgifter som ska läsas in: en lagrings konto nyckel, en Azure Directory-programanvändare eller en AAD-användare som har lämplig RBAC-roll för lagrings kontot. 

##  <a name="create-a-credential"></a>Skapa en autentiseringsuppgift
Du kan hoppa över det här avsnittet och fortsätta till "skapa den externa data källan" när du autentiserar med hjälp av AAD-vidarekoppling. En databas som omfattas av databasen behöver inte skapas eller anges när du använder AAD-vidarekoppling, men se till att din AAD-användare har lämplig RBAC-roll (Storage BLOB data Reader, deltagare eller ägar roll) till lagrings kontot. Mer information beskrivs [här](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

För att få åtkomst till ditt Data Lake Storage-konto måste du skapa en huvud nyckel för databasen för att kryptera din hemliga autentiseringsuppgifter. Du kan sedan skapa en databas med begränsade autentiseringsuppgifter för att lagra din hemliga information. När du autentiserar med hjälp av tjänstens huvud namn (Azure Directory Application User) lagrar databasens begränsade autentiseringsuppgifter de autentiseringsuppgifter för tjänstens huvud namn som angetts i AAD. Du kan också använda databasens begränsade autentiseringsuppgifter för att lagra lagrings konto nyckeln för Gen2.

Om du vill ansluta till Data Lake Storage med hjälp av tjänstens huvud namn måste du **först** skapa ett Azure Active Directory program, skapa en åtkomst nyckel och ge programmet åtkomst till data Lake Storage-kontot. Instruktioner finns i [autentisera till Azure Data Lake Storage med hjälp av Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Skapa den externa data källan
Använd det här kommandot [skapa extern data källa](/sql/t-sql/statements/create-external-data-source-transact-sql) för att lagra data platsen. Om du autentiserar med AAD-vidarekoppling krävs inte parametern CREDENTIAL. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfigurera data format
Om du vill importera data från Data Lake Storage måste du ange det externa fil formatet. Det här objektet definierar hur filerna skrivs i Data Lake Storage.
I den fullständiga listan tittar du på vår T-SQL-dokumentation [Skapa externt fil format](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Nu när du har angett data källan och fil formatet är du redo att skapa de externa tabellerna. Externa tabeller är hur du interagerar med externa data. Parametern location kan ange en fil eller en katalog. Om den anger en katalog kommer alla filer i katalogen att läsas in.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
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
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Överväganden för externa tabeller
Det är enkelt att skapa en extern tabell, men det finns vissa olika delarna som behöver diskuteras.

Externa tabeller är starkt skrivna. Det innebär att varje rad data som matas in måste uppfylla tabell schema definitionen.
Om en rad inte matchar schema definitionen avvisas raden från belastningen.

Med alternativen REJECT_TYPE och REJECT_VALUE kan du definiera hur många rader eller vilken procent andel av data som måste finnas i den slutliga tabellen. Vid inläsningen Miss lyckas inläsningen. Den vanligaste orsaken till avvisade rader är en felaktig schema definition. Om en kolumn till exempel felaktigt har fått schemat för int när data i filen är en sträng, kommer alla rader inte att läsas in.

Data Lake Storage Gen1 använder rollbaserad Access Control (RBAC) för att kontrol lera åtkomsten till data. Det innebär att tjänstens huvud namn måste ha Läs behörighet till de kataloger som definierats i plats parametern och till underordnade till den slutgiltiga katalogen och filerna. Detta gör att PolyBase kan autentisera och läsa in dessa data. 

## <a name="load-the-data"></a>Läs in data
Om du vill läsa in data från Data Lake Storage använder du uttrycket [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS skapar en ny tabell och fyller den med resultatet av en SELECT-instruktion. CTAS definierar den nya tabellen för att ha samma kolumner och data typer som resultatet av SELECT-instruktionen. Om du väljer alla kolumner från en extern tabell är den nya tabellen en replik av kolumnerna och data typerna i den externa tabellen.

I det här exemplet skapar vi en hash-distribuerad tabell med namnet DimProduct från vår externa tabell DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering
Som standard definieras tabeller som ett grupperat columnstore-index. När en belastning är klar kanske vissa av data raderna inte komprimeras till columnstore.  Det finns en mängd olika orsaker till varför detta kan inträffa. Mer information finns i [Hantera columnstore-index](sql-data-warehouse-tables-index.md).

Om du vill optimera prestanda och columnstore-komprimering efter en belastning måste du återskapa tabellen för att tvinga columnstore-indexet att komprimera alla rader.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik
Det är bäst att skapa statistik med en kolumn direkt efter en belastning. Det finns några val för statistik. Om du till exempel skapar en statistik med en kolumn på varje kolumn kan det ta lång tid att bygga om all statistik. Om du vet att vissa kolumner inte kommer att finnas i frågesyntaxen kan du hoppa över att skapa statistik för dessa kolumner.

Om du bestämmer dig för att skapa en statistik med en kolumn på varje kolumn i varje tabell kan du använda exempel koden för den lagrade proceduren `prc_sqldw_create_stats` i [statistik](sql-data-warehouse-tables-statistics.md) artikeln.

I följande exempel visas en start punkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i tabellen dimension och i varje kopplings kolumn i fakta tabellerna. Du kan alltid lägga till en eller flera kolumn statistik i andra fakta tabell kolumner senare.

## <a name="achievement-unlocked"></a>Utmärkelse upplåst!
Du har läst in data i informations lagret. Bra jobbat!

## <a name="next-steps"></a>Nästa steg 
I den här självstudien har du skapat externa tabeller för att definiera strukturen för data som lagras i Data Lake Storage Gen1 och sedan använda PolyBase-CREATE TABLE som SELECT-uttryck för att läsa in data till data lagret. 

Du gjorde detta:
> [!div class="checklist"]
>
> * Skapade databas objekt som krävs för att läsa in från Data Lake Storage.
> * Ansluten till en Data Lake Storage katalog.
> * Inlästa data till data lagret.
>

Inläsning av data är det första steget för att utveckla en informations lager lösning med Azure Synapse Analytics. Kolla våra utvecklings resurser.

> [!div class="nextstepaction"]
> [Lär dig hur du utvecklar tabeller för data lager hantering](sql-data-warehouse-tables-overview.md)
