---
title: Självstudieinläsningsdata från Azure Data Lake Storage
description: Använd externa PolyBase-tabeller för att läsa in data från Azure Data Lake Storage för Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7d599ce121b4c53662b91e5aab94130b0f3f4458
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583940"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>Läsa in data från Azure Data Lake Storage för Synapse SQL

Den här guiden beskriver hur du använder externa PolyBase-tabeller för att läsa in data från Azure Data Lake Storage. Även om du kan köra adhoc-frågor om data som lagras i DataSjölagring rekommenderar vi att du importerar data för bästa prestanda. 

> [!NOTE]  
> Ett alternativ till inläsning är [COPY-satsen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) som för närvarande är i offentlig förhandsversion.  COPY-satsen ger mest flexibilitet. Om du vill ge feedback på COPY-satsen sqldwcopypreview@service.microsoft.comskickar du ett e-postmeddelande till följande distributionslista: .
>
> [!div class="checklist"]

> * Skapa databasobjekt som krävs för att läsa in från Data Lake Storage.
> * Anslut till en datasjölagringskatalog.
> * Läs in data i informationslagret.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar
Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

För att köra den här självstudien behöver du:

* En SQL-pool. Se [Skapa en SQL-pool och frågedata](create-data-warehouse-portal.md).
* Ett datasjölagringskonto. Se [Komma igång med Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md). För det här lagringskontot måste du konfigurera eller ange någon av följande autentiseringsuppgifter för att läsa in: En lagringskontonyckel, en Azure Directory Application-användare eller en AAD-användare som har lämplig RBAC-roll till lagringskontot. 

##  <a name="create-a-credential"></a>Skapa en autentiseringstillstånd
Du kan hoppa över det här avsnittet och fortsätta till "Skapa den externa datakällan" när du autentiserar via AAD-genomströmning. En databasomfattad autentiseringsuppgifter behöver inte skapas eller anges när du använder AAD-vidare, men se till att din AAD-användare har rätt RBAC-roll (Storage Blob Data Reader, Contributor eller Owner Role) till lagringskontot. Mer information beskrivs [här](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

För att komma åt ditt Data Lake Storage-konto måste du skapa en databasnyckel för att kryptera din autentiseringsuppgifter. Du kan sedan skapa en databasscopen för att lagra din hemlighet. När du autentiserar med hjälp av tjänsthuvudnamn (Azure Directory Application-användare) lagrar databasen scoped-autentiseringsuppgifterna de autentiseringsuppgifter för tjänsten som konfigurerats i AAD. Du kan också använda databasscopen för att lagra lagringskontonyckeln för Gen2.

Om du vill ansluta till DataSjölagring med hjälp av tjänsthuvudnamn måste du **först** skapa ett Azure Active Directory-program, skapa en åtkomstnyckel och bevilja programmet åtkomst till DataSjölagringskontot. Instruktioner finns [i Autentisera till Azure Data Lake Storage med Active Directory](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md).

Logga in i din SQL-pool med en användare med behörighet på CTRL-nivå och kör följande SQL-uttryck mot databasen:

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

## <a name="create-the-external-data-source"></a>Skapa den externa datakällan
Använd det här kommandot [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql) för att lagra platsen för data. Om du autentiserar med AAD-genomströmning krävs inte parametern AUTENTISERINGSUPPGIFTER. 

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

## <a name="configure-data-format"></a>Konfigurera dataformat
Om du vill importera data från DataSjölagring måste du ange det externa filformatet. Det här objektet definierar hur filerna skrivs i Data Lake Storage.
För hela listan, titta på vår T-SQL dokumentation [SKAPA EXTERNT FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Nu när du har angett datakällan och filformatet är du redo att skapa de externa tabellerna. Externa tabeller är hur du interagerar med externa data. Platsparametern kan ange en fil eller en katalog. Om den anger en katalog läses alla filer i katalogen in.

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
Det är enkelt att skapa en extern tabell, men det finns vissa nyanser som måste diskuteras.

Externa tabeller skrivs starkt. Det innebär att varje rad med de data som förtärs måste uppfylla tabellschemadefinitionen.
Om en rad inte matchar schemadefinitionen avvisas raden från inläsningen.

Med alternativen REJECT_TYPE och REJECT_VALUE kan du definiera hur många rader eller hur stor procentandel av data som ska finnas i finaltabellen. Under inläsningen, om avvisningsvärdet uppnås, misslyckas belastningen. Den vanligaste orsaken till avvisade rader är en schemadefinition som inte stämmer överens. Om en kolumn till exempel felaktigt ges schemat för int när data i filen är en sträng, kommer varje rad inte att läsas in.

Data Lake Storage Gen1 använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till data. Det innebär att tjänsthuvudstaden måste ha läsbehörighet till de kataloger som definierats i platsparametern och till underordnade till den slutliga katalogen och filerna. Detta gör det möjligt för PolyBase att autentisera och läsa in dessa data. 

## <a name="load-the-data"></a>Läs in data
Om du vill läsa in data från Data Lake Storage använder du [CREATE TABLE AS SELECT -uttrycket (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 

CTAS skapar en ny tabell och fyller den med resultatet av en select-sats. CTAS definierar den nya tabellen så att samma kolumner och datatyper har samma kolumner och datatyper som resultatet av select-satsen. Om du markerar alla kolumner från en extern tabell är den nya tabellen en replik av kolumnerna och datatyperna i den externa tabellen.

I det här exemplet skapar vi en hash-distribuerad tabell som heter DimProduct från vår externa tabell DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering
Som standard definieras tabeller som ett grupperat columnstore-index. När en inläsning har slutförts kanske vissa datarader inte komprimeras till columnstore.  Det finns en mängd olika skäl till varför detta kan hända. Mer information finns i [Hantera columnstore-index](sql-data-warehouse-tables-index.md).

Om du vill optimera frågeprestanda och columnstore-komprimering efter en belastning återskapar du tabellen så att columnstore-indexet komprimerar alla rader.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik
Det är bäst att skapa statistik med en kolumn omedelbart efter en belastning. Det finns några alternativ för statistik. Om du till exempel skapar statistik med en kolumn i varje kolumn kan det ta lång tid att återskapa all statistik. Om du vet att vissa kolumner inte kommer att finnas i frågepredikater kan du hoppa över att skapa statistik för dessa kolumner.

Om du bestämmer dig för att skapa statistik med en kolumn i `prc_sqldw_create_stats` varje kolumn kan du använda det lagrade procedurkodexemplet i [statistikartikeln.](sql-data-warehouse-tables-statistics.md)

Följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i dimensionstabellen och på varje kopplingskolumn i faktatabellerna. Du kan alltid lägga till statistik med en eller flera kolumner i andra faktatabellkolumner senare.

## <a name="achievement-unlocked"></a>Prestation olåst!
Du har läst in data i ditt informationslager. Bra jobbat!

## <a name="next-steps"></a>Nästa steg 
I den här självstudien skapade du externa tabeller för att definiera strukturen för data som lagras i Data Lake Storage Gen1 och använde sedan PolyBase CREATE TABLE AS SELECT-satsen för att läsa in data i ditt informationslager. 

Du gjorde detta:
> [!div class="checklist"]
>
> * Skapade databasobjekt som krävs för att läsa in från Data Lake Storage.
> * Ansluten till en datasjölagringskatalog.
> * Inläst data i informationslagret.
>

Att läsa in data är det första steget för att utveckla en informationslagerlösning med Azure Synapse Analytics. Kolla in våra utvecklingsresurser.

> [!div class="nextstepaction"]
> [Lär dig hur du utvecklar tabeller för datalagring](sql-data-warehouse-tables-overview.md)
