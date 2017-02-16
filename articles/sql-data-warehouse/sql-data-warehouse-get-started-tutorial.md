---
title: "Självstudiekurs: Komma igång med Azure SQL Data Warehouse | Microsoft Docs"
description: "Självstudiekurs: Komma igång med Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 5bb75bf36892c737568b8129b30bb30b02d01bf2
ms.openlocfilehash: 1227903652a944d9d144917922fe36a4f149f820


---
# <a name="get-started-with-sql-data-warehouse"></a>Komma igång med SQL Data Warehouse

Självstudiekurs: Komma igång med Azure SQL Data Warehouse. I den här introduktionskursen lär du dig hur du etablerar och läser in data till ett SQL Data Warehouse, samt hur du skalar, pausar och justerar. 

**Uppskattad tidsåtgång:** 75 minuter

## <a name="prerequisites"></a>Krav


### <a name="sign-up-for-microsoft-azure"></a>Registrera dig för Microsoft Azure
Om du inte redan har ett Microsoft Azure-konto måste du registrera dig för ett konto för att kunna använda den här tjänsten. Om du redan har ett konto kan du hoppa över det här steget. 

1. Gå till kontosidorna på [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Skapa ett kostnadsfritt Azure-konto eller köp ett konto.
3. Följ anvisningarna

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Installera rätt verktyg och drivrutin för SQL-klienten

De flesta SQL-klientverktygen kan ansluta till Azure SQL Data Warehouse med JDBC, ODBC eller ADO.net. På grund av produktens komplexitet och det stora antalet T-SQL-funktioner som SQL Data Warehouse stöder kanske inte alla klientprogram är helt kompatibla med SQL Data Warehouse.

Om du kör ett Windows-operativsystem rekommenderar vi att du använder antingen [Visual Studio] eller [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Skapa ett Azure SQL Data Warehouse

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Skapa ett SQL Data Warehouse
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Nytt** > **Databaser** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Fyll i distributionsinformationen

    **Databasnamn**: Välj önskat namn. Om du har flera instanser av SQL DW rekommenderar vi att namnet innehåller information om instansens region, miljö osv., t.ex. *mydw-westus-1-test*

    **Prenumeration**: Din Azure-prenumeration

    **Resursgrupp**: Skapa en ny (eller använd en befintlig om du planerar att använda ditt Azure SQL Data Warehouse med andra tjänster)
    > [!NOTE]
    > Tjänster i en resursgrupp bör ha samma livscykel. Resursgrupper är användbara för resursadministration, till exempel för att styra åtkomstkontroll och mallbaserad distribution. Läs mer om Azure-resursgrupper och bästa praxis [här](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
    >

    **Källa**: Tom databas

    **Server**: Välj den server som du skapade i [Krav].

    **Sortering**: Lämna standardsorteringen SQL_Latin1_General_CP1_CI_AS

    **Select performance** (Välj prestanda): Vi rekommenderar att du använder standardinställningen 400DWU

4. Välj **Fäst på instrumentpanelen**
    ![Fäst på instrumentpanelen](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Luta dig tillbaka medan Azure SQL Data Warehouse distribueras! Processen kan ta flera minuter. Ett meddelande visas på portalen när din instans har distribuerats. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Ansluta till Azure SQL Data Warehouse via SQL Server (logisk server)

I den här självstudiekursen använder vi SQL Server Management Studio för att ansluta till SQL Data Warehouse. Du kan använda andra verktyg för att ansluta till SQL Data Warehouse med hjälp av våra anslutningsappar som stöds: ADO.NET, JDBC, ODBC och PHP. Tänk på att funktionaliteten kan vara begränsad med verktyg som inte stöds av Microsoft.


### <a name="get-connection-information"></a>Hämta anslutningsinformation

För att ansluta till SQL Data Warehouse måste du ansluta via den SQL Server (logisk server) som du skapade i [Krav].

1. Välj ditt SQL Data Warehouse på instrumentpanelen eller sök efter det i dina resurser.

    ![Instrumentpanel för SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Hitta den logiska serverns fullständiga namn.

    ![Välj servernamn](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Öppna SSMS och använd objektutforskaren för att ansluta till den här servern med de autentiseringsuppgifter som du skapade i [Krav]

    ![Anslut med SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Om allt går som det ska bör du nu vara ansluten till din SQL Server-instans (logisk server). Du kan använda autentiseringsuppgifter för att autentisera till valfri databas på servern som databasens ägare. Som bästa praxis bör du dock skapa separata inloggningar och användare för varje databas. Vi ska se hur du skapar användare i avsnittet [Skapa en användare för SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Skapa en användare för SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>Varför bör du skapa en separat användare?

Vi använder anslutningen till SQL Server (logisk server), med serverautentiseringsuppgifterna från föregående steg, för att skapa en ny användare för vårt SQL Data Warehouse. Det finns två huvudsakliga skäl till varför du kanske vill skapa en separat användare/inloggning för ditt SQL DW.

1.  Din organisations användare ska använda ett annat konto för att autentisera. På så sätt kan du begränsa behörigheterna som programmet beviljas och minska riskerna för skadlig aktivitet.

2. Den serveradministratörsinloggning som du för närvarande är ansluten med använder som standard en mindre resursklass. Resursklasser hjälper dig att kontrollera minnesallokeringen och processorcyklerna som en fråga tilldelas. Användare i **smallrc** tilldelas en mindre mängd minne och kan dra nytta av fler samtidiga körningar. Användare som har tilldelats till **xlargerc** får däremot stora mängder minne, vilket innebär att färre av deras frågor kan köras samtidigt. För att inläsningen av dessa data ska göras på ett sätt som optimerar komprimeringen bör de vara en del av en större resursklass. Läs mer om resursklasser [här](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="creating-a-user-of-a-larger-resource-class"></a>Skapa en användare i en större resursklass

1. Skapa en ny fråga mot serverns **huvuddatabas**

    ![Ny fråga mot huvuddatabas](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Ny fråga mot huvuddatabas1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Skapa en serverinloggning och användare

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Skapa en ny databasanvändare baserat på serverinloggningen
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Bevilja databaskontroll till användare
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Om ditt databasnamn innehåller bindestreck måste du skriva det inom parentes! 
    >

5. Lägg till databasanvändaren till resursklassrollen **xlargerc**
    ```sql
    EXEC sp_addrolememeber 'xlargerc', 'LoadingUser';
    ```

6. Logga in i databasen med dina nya autentiseringsuppgifter

    ![Logga in med ny inloggning](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Läsa in data

### <a name="defining-external-data"></a>Definiera externa data
1. Definiera en extern datakälla

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Ange de externa filformaten

    Kommandot ```CREATE EXTERNAL FILE FORMAT``` används för att ange formatet för de externa data som du läser in från. För New Yorks offentliga taxiinformation har vi använt de två formaten för att lagra data i Azure Blob Storage

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Skapa ett schema för det externa filformatet

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Skapa de externa tabellerna. Dessa tabeller referera till data som lagras i HDFS-lagring eller Azure Blob Storage. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>CTAS (Create Table As Select)

5. Läs in data från externa tabeller till din instans av SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Du läser in flera GB data och komprimerar dem till högpresterande Cluster Columnstore-index. Kör den efterföljande DMV-frågan och ta sedan en kaffe eller fika medan Azure SQL Data Warehouse gör jobbet.
    >

6. Skapa en ny fråga och se hur dina data hämtas till den här dynamiska hanteringsvyn (DMV)

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Visa alla systemfrågor

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Se hur dina data läses in i Azure SQL Data Warehouse

    ![Visa inlästa data](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Köra frågor mot data 

### <a name="scan-query-with-scaling"></a>Skanna fråga med skalning

Nu ska vi se hur skalning påverkar hur snabbt dina frågor körs.

Innan vi börjar ska vi skala ned till 100 DWU:er för att få en uppfattning av hur en beräkningsnod kan prestera på egen hand.

1. Gå till portalen och välj din instans av SQL Data Warehouse

2. Välj skala på bladet SQL Data Warehouse. 

    ![Skala DW från portalen](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Skala ned prestandafältet till 100 DWU och klicka på Spara.

    ![Skala och spara](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Vänta tills skalningen har slutförts.

    > [!NOTE]
    > Observera att skalningsåtgärder **avbryter** frågor som körs och förhindrar att nya frågor körs.
    >
    
5. Kör en genomsökningsåtgärd mot reseinformationen och välj den översta miljonen poster för alla kolumner. Om du är otålig och vill gå vidare snabbt kan du välja färre rader.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Notera hur lång tid det tog att utföra den här åtgärden.

6. Skala upp din instans till 400 DWU. Kom ihåg att varje 100 DWU lägger till en till beräkningsnod till ditt Azure SQL Data Warehouse.

7. Kör frågan igen! Du bör märka en stor skillnad. 

> [!NOTE]
> Azure SQL Data Warehouse är en MPP-plattform (Massively Parallel Processing). Frågor och åtgärder som kan parallellisera arbete mellan olika noder drar nytta av den verkliga kraften i Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Koppla fråga med statistik

1. Kör en fråga som kopplar datumtabellen med resetabellen

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Som förväntat tar frågan mycket längre tid när du blandar data mellan noderna, särskilt i ett kopplingsscenario som det här.

2. Nu ska vi se skillnaden när vi skapar statistik baserat på kolumnen som vi kopplar genom att köra följande:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW hanterar inte statistik automatiskt åt dig. Statistik är viktigt för frågeprestanda och vi rekommenderar att du skapar och uppdaterar statistik.
    > 
    > **Du får ut mest genom att använda statistik med kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**
    >

3. Kör frågan från Krav igen och notera eventuella skillnader i prestanda. Även om skillnaderna i frågeprestanda inte är lika drastiska som när du skalar upp, bör du märka att det går snabbare. 

## <a name="next-steps"></a>Nästa steg

Nu är du redo att köra frågor och utforska. Se våra bästa praxis och tips.

Om du är klar för dagen ser du till att pausa din instans. I en produktionsmiljö kan du uppnå enorma besparingar genom att pausa och skala utifrån dina affärsbehov.

![Pausa](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Bra information

[Concurrency and workload management] (Hantering av samtidiga körningar och arbetsbelastningar)

[Metodtips för Azure SQL Data Warehouse]

[Query Monitoring] (Frågeövervakning)

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse] (Tio rekommendationer för att skapa ett storskaligt relationsinformationslager)

[Migrating Data to Azure SQL Data Warehouse] (Migrera data till Azure SQL Data Warehouse)


[Concurrency and workload management]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example (Hantering av samtidiga körningar och arbetsbelastningar)
[Metodtips för Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Query Monitoring]: sql-data-warehouse-manage-monitor.md (Frågeövervakning)
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/ (Tio rekommendationer för att skapa ett storskaligt relationsinformationslager)
[Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/ (Migrera data till Azure SQL Data Warehouse)



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Krav]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO2-->


