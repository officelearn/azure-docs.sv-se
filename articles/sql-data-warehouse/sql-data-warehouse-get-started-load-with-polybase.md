---
title: "Självstudier för PolyBase i SQL Data Warehouse | Microsoft Docs"
description: "Läs mer om vad PolyBase är och hur man använder det för informationslagerscenarier."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 0a0103b4-ddd6-4d1e-87be-4965d6e99f3f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/01/2017
ms.author: cakarst
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 1e3cd48aebf6f00802863c1a1b20fcb625e03c25
ms.lasthandoff: 03/03/2017



---
# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Läs in data med PolyBase i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

De här självstudierna visar hur du läser in data i SQL Data Warehouse med hjälp av AzCopy och PolyBase. När du är klar, kommer du att veta hur man:

* Använder AzCopy för att kopiera data till Azure-blobblagring
* Skapar databasobjekt för att definiera data
* Kör en T-SQL-fråga för att läsa in data

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-with-PolyBase-in-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Krav
För att gå igenom de här självstudierna behöver du

* En SQL Data Warehouse-databas.
* Ett Azure-lagringskonto av typen standard lokalt redundant lagring (Standard-LRS), standard geo-redundant lagring (Standard-GRS) eller standard geo-redundant lagring med läsbehörighet (Standard-RAGRS).
* Kommandoradsverktyget AzCopy. Hämta och installera den [senaste versionen av AzCopy][latest version of AzCopy] som installeras med Microsoft Azure Storage-verktygen.
  
    ![Azure Storage-verktyg](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)

## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Steg 1: Lägg till exempeldata i Azure blobblagret
För att kunna läsa in data, behöver vi lägga exempeldata i ett Azure-blobblager. I det här steget fyller vi en Azure Storage-blob med exempeldata. Senare kommer vi att använda PolyBase för att läsa in exempeldatan till din SQL Data Warehouse-databas.

### <a name="a-prepare-a-sample-text-file"></a>A. Förbered en exempeltextfil
Förbered en exempeltextfil:

1. Öppna anteckningar och kopiera följande datarader i en ny fil. Spara den på din lokala temp-katalog som %temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Hitta blobbtjänstens slutpunkt
Så här hittar du blobbtjänstens slutpunkt:

1. I Azure Portal väljer du **Bläddra** > **Lagringskonton**.
2. Klicka på det lagringskonto som du vill använda.
3. I bladet Storage-konton klickar du på Blobbar
   
    ![Klicka på Blobbar](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)
4. Spara URL:en för blobbtjänstens slutpunkt för senare.
   
    ![Blob-tjänstens slutpunkt](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Hitta din Azure-lagringsnyckel
Hitta din Azure-lagringsnyckel:

1. I Azure Portal väljer du **Bläddra** > **Lagringskonton**.
2. Klicka på det lagringskonto som du vill använda.
3. Välj **Alla inställningar** > **Åtkomstnycklar**.
4. Klicka på kopiera-rutan för att kopiera en av dina åtkomstnycklar till urklipp.
   
    ![Kopiera Azure-lagringsnyckel](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Kopiera exempelfilen till Azure-blobblagring
Kopiera dina data till Azure-blobblagring:

1. Öppna en kommandotolk och ändra katalog till installationskatalogen för AzCopy. Det här kommandot ändrar till standard-installationskatalogen på en 64-bitars Windows-klient.
   
    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```
2. Kör följande kommando för att ladda upp filen. Ange URL:en för blobbtjänstens slutpunkt för <blob service endpoint URL> och nyckeln för Azure-lagringskontot för <azure_storage_account_key>.
   
    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Mer information finns i [Kom igång med kommandoradsverktyget AzCopy][Getting Started with the AzCopy Command-Line Utility].

### <a name="e-explore-your-blob-storage-container"></a>E. Utforska din blobblagringsbehållare
Om du vill se filen du laddade upp till blobblagring:

1. Gå tillbaka till bladet Blob-tjänst.
2. Under Behållare dubbelklickar du på **datacontainer**.
3. Om du vill följa sökvägen till dina data, klickar du på mappen **datedimension** för att se filen **DimDate2.txt** som du laddat upp.
4. Om du vill visa egenskaper, klickar du på **DimDate2.txt**.
5. Observera att bladet för Blob-egenskaper låter dig hämta eller ta bort filen.
   
    ![Visa Azure-lagringsblobb](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)

## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Steg 2: Skapa en extern tabell för exempeldata
I det här avsnittet ska vi skapa en extern tabell som definierar exempeldata.

PolyBase använder sig av externa tabeller för att komma åt data i Azure-blobblagring. Eftersom data inte lagras inom SQL Data Warehouse, hanterar PolyBase autentisering till externa data med hjälp av en databas-omfattande autentisering.

Exemplet i det här steget använder de här Transact-SQL-uttrycken för att skapa en extern tabell.

* [Skapa huvudnyckel (Transact-SQL)][Create Master Key (Transact-SQL)] för att kryptera hemligheten för din databasövergripande autentisering.
* [Skapa databasomfattande autentisering (Transact-SQL)][Create Database Scoped Credential (Transact-SQL)] och ange autentiseringsinformation för ditt Azure-lagringskonto.
* [Skapa extern datakälla (Transact-SQL)][Create External Data Source (Transact-SQL)] och ange platsen för din Azure-bloblagring.
* [Skapa externt filformat (Transact-SQL)][Create External File Format (Transact-SQL)] och ange formatet för dina data.
* [Skapa extern tabell (Transact-SQL)][Create External Table (Transact-SQL)] och ange tabelldefinitionen och platsen för dina data.

Kör den här frågan mot din SQL Data Warehouse-databas. Det skapar en extern tabell som heter DimDate2External i dbo-schemat, som pekar på exempeldatan DimDate2.txt i Azure-blobblagret.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


I SQL Server Object Explorer i Visual Studio, kan du se det externa filformatet, den externa datakällan och DimDate2External-tabellen.

![Visa extern tabell](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Steg 3: Läs in data till SQL Data Warehouse
När den externa tabellen har skapats kan du antingen läsa in dina data till en ny tabell eller infoga dem i en befintlig tabell.

* Om du vill läsa in data till en ny tabell kör du uttrycket [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Den nya tabellen kommer att ha kolumnerna som namnges i frågan. Datatyperna för kolumnerna kommer att matcha datatyperna i den externa tabelldefinitionen.
* Om du vill läsa in data i en befintlig tabell använder du uttrycket [INSERT...SELECT (Transact-SQL)][INSERT...SELECT (Transact-SQL)].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Steg 4: Skapa statistik på dina nyinlästa data
SQL Data Warehouse skapar och uppdaterar inte statistik automatiskt. För att få en hög frågeprestanda är det därför viktigt att skapa statistik för varje kolumn av varje tabell efter den första inläsningen. Det är också viktigt att uppdatera statistiken efter att det har skett betydande förändringar.

Det här exemplet skapar enkolumns-statistik för den nya DimDate2-tabellen.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Mer information finns i [Statistik][Statistics].  

## <a name="next-steps"></a>Nästa steg
Se [PolyBase-guiden][PolyBase guide] för ytterligare information som du bör känna till när du utvecklar en PolyBase-baserad lösning.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[PolyBase guide]: ./sql-data-warehouse-load-polybase-guide.md
[Getting Started with the AzCopy Command-Line Utility]: ../storage/storage-use-azcopy.md
[latest version of AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx

