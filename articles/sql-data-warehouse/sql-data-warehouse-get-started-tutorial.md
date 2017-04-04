---
title: "Självstudiekurs: Komma igång med Azure SQL Data Warehouse | Microsoft Docs"
description: "I den här kursen får du lära dig hur du etablerar och läser in data i Azure SQL Data Warehouse. Du får också lära dig grunderna för skala, pausa och justera."
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
ms.custom: quickstart
ms.date: 01/26/2017
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9d3029817cae6570ff8871fbcb068250544595d7
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-sql-data-warehouse"></a>Komma igång med SQL Data Warehouse

Den här kursen visar hur du etablerar och läser in data i Azure SQL Data Warehouse. Du får också lära dig grunderna för skala, pausa och justera. När du är klar är du redo att fråga efter och utforska datalagret.

**Återstående tid:** Det här är en självstudie för slutpunkt till slutpunkt med exempelkod som tar cirka 30 minuter att slutföra när du har uppfyllt kraven. 

## <a name="prerequisites"></a>Krav

Självstudien förutsätter att du är bekant med grundläggande begrepp för SQL Data Warehouse. En introduktion finns i [Vad är SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md) 

### <a name="sign-up-for-microsoft-azure"></a>Registrera dig för Microsoft Azure
Om du inte redan har ett Microsoft Azure-konto måste du registrera dig för ett konto för att kunna använda den här tjänsten. Om du redan har ett konto kan du hoppa över det här steget. 

1. Gå till kontosidorna på [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Skapa ett kostnadsfritt Azure-konto eller köp ett konto.
3. Följ anvisningarna

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>Installera rätt verktyg och drivrutin för SQL-klienten

De flesta SQL-klientverktygen kan ansluta till SQL Data Warehouse med JDBC, ODBC eller ADO.NET. På grund av det stora antalet T-SQL-funktioner som SQL Data Warehouse stöder kanske inte alla klientprogram är helt kompatibla med SQL Data Warehouse.

Om du kör ett Windows-operativsystem rekommenderar vi att du använder antingen [Visual Studio] eller [SQL Server Management Studio].

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>Skapa ett SQL Data Warehouse

En SQL Data Warehouse är en särskild typ av databas som är avsedd för MPP (massively parallel processing). Databasen fördelas på flera noder och bearbetar frågor parallellt. SQL Data Warehouse har en kontrollnod som samordnar aktiviteter för alla noder. Noderna använder SQL Database för att hantera dina data.  

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Skapa ett datalager

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt** > **Databaser** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Fyll i distributionsinformationen

    **Databasnamn**: Välj önskat namn. Om du har flera instanser av datalager rekommenderar vi att dina namn innehåller information om instansens region, miljö osv., t.ex. *mydw-westus-1-test*.

    **Prenumeration**: Din Azure-prenumeration

    **Resursgrupp**: Skapa en resursgrupp eller använd en befintlig resursgrupp.
    > [!NOTE]
    > Resursgrupper är användbara för resursadministration, till exempel för att styra åtkomstkontroll och mallbaserad distribution. Läs mer om Azure-resursgrupper och bästa praxis [här](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)

    **Källa**: Tom databas

    **Server**: Välj den server som du skapade i [Krav].

    **Sortering**: Lämna standardsorteringen SQL_Latin1_General_CP1_CI_AS.

    **Select performance** (Välj prestanda): Vi rekommenderar att du börjar med standardinställningen 400DWU.

4. Välj **Fäst på instrumentpanelen**
    ![Fäst på instrumentpanelen](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Luta dig tillbaka medan datalagret distribueras! Processen kan ta flera minuter. Portalen meddelar dig när datalagret är klart att användas. 

## <a name="connect-to-sql-data-warehouse"></a>Anslut till SQL Data Warehouse

I den här självstudiekursen använder vi SQL Server Management Studio (SSMS) för att ansluta till datalagret. Du kan ansluta till SQL Data Warehouse med hjälp av våra anslutningsappar som stöds: ADO.NET, JDBC, ODBC och PHP. Tänk på att funktionaliteten kan vara begränsad med verktyg som inte stöds av Microsoft.


### <a name="get-connection-information"></a>Hämta anslutningsinformation

Om du vill ansluta till ditt informationslager måste du ansluta via den logiska SQL-server som du skapade i [Krav].

1. Välj ditt datalager på instrumentpanelen eller sök efter det i dina resurser.

    ![Instrumentpanel för SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Hitta den logiska SQL-serverns fullständiga namn.

    ![Välj servernamn](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Öppna SSMS och använd objektutforskaren för att ansluta till den här servern med de serveradmin-autentiseringsuppgifter som du skapade i [Krav]

    ![Anslut med SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Om allt går som det ska bör du nu vara ansluten till din logiska SQL Server-instans. Eftersom du har loggat in som serveradministratör kan du ansluta till alla databaser som hanteras av servern, inklusive huvuddatabasen. 

Det finns endast ett serveradministratörskonto och det har de flesta behörigheter för alla användare. Var noga med att inte låta för många personer i organisationen få reda på adminlösenordet. 

Du kan också ha ett administratörskonto för Azure Active Directory. Vi tillhandahåller inte informationen här. Om du vill veta mer om hur du använder Azure Active Directory-autentisering går du till [Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

Därefter skapar vi ytterligare inloggningar och användare.


## <a name="create-a-database-user"></a>Skapa en databasanvändare

I det här steget kan skapa du ett användarkonto för att få åtkomst till datalagret. Vi visar också hur du ger användaren möjlighet att köra frågor med en stor mängd minne och CPU-resurser.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Information om resursklasser för att tilldela resurser till frågor

- Om du vill skydda dina data ska du inte använda serveradministratören för att köra frågor i produktionsdatabaserna. Här finns de flesta privilegier för alla användare, och om du använder den för att utföra åtgärder på användardata kan dina skadas. Dessutom, eftersom serveradministratören är avsedd att utföra hanteringsåtgärder, kör den åtgärder med en liten allokering av minne och CPU-resurser. 

- SQL Data Warehouse använder fördefinierade databasroller som kallas resursklasser för att allokera olika mängder minne, CPU-resurser och samtidighetsfack till användare. Varje användare kan höra till en liten, mellanstor eller extra stor resursklass. Användarens resursklass bestämmer de resurser som användaren måste köra frågor och läsa in åtgärder för.

- För att optimera datakomprimering kan användaren behöva läsa in med stora eller extra stora resursallokeringar. Läs mer om resursklasser [här](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="create-an-account-that-can-control-a-database"></a>Skapa ett konto som kan styra en databas

Eftersom du för närvarande är inloggad som serveradministratör har du behörighet att skapa inloggningar och användare.

1. Med SSMS eller någon annan frågeklient öppnar du en ny fråga för **huvuddatabasen**.

    ![Ny fråga mot huvuddatabas](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Ny fråga mot huvuddatabas1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. I frågefönstret kör du det här T-SQL-kommandot för att skapa en inloggning med namnet MedRCLogin och en användare med namnet LoadingUser. Den här inloggningen kan inte ansluta till den logiska SQL-servern.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. Nu frågar du *SQL Data Warehouse-databasen*. Skapa en databasanvändare baserat på inloggningen du skapade för att få åtkomst till och utföra åtgärder i databasen.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Ge databasen användarkontrollbehörigheter till databasen NYT. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Om ditt databasnamn innehåller bindestreck måste du skriva det inom parentes! 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Ge användaren medelstora resursallokeringar

1. Kör det här T-SQL-kommandot för att göra den medlem i den medelstora resursklassen som kallas mediumrc. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Klicka [här](sql-data-warehouse-develop-concurrency.md#resource-classes) och läs mer om samtidighet och resursklasser! 
    >

2. Ansluta till den logiska servern med nya autentiseringsuppgifter

    ![Logga in med ny inloggning](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Läsa in data från Azure Blob Storage

Du är nu redo att läsa in data till datalagret. Det här steget visar hur du hämtar New York City-taxidata från en offentlig Azure Storage-blob. 

- Ett vanligt sätt att läsa in data till SQL Data Warehouse är att först flytta data till Azure Blob Storage och sedan läsa in den i datalagret. Om du vill göra det enklare att förstå hur du ska läsa in har vi redan New York-taxidata i en offentlig Azure Storage-blob. 

- Om du vill lära dig hur du får dina data till Azure Blob Storage eller hur du läser in dem direkt från källan till SQL Data Warehouse för framtida bruk går du till [översikten över inläsning](sql-data-warehouse-overview-load.md).


### <a name="define-external-data"></a>Definiera externa data

1. Skapa en huvudnyckel. Du behöver bara skapa en huvudnyckel en gång per databas. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Ange platsen för Azure-bloben som innehåller taxi-data.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Ange de externa filformaten

    Kommandot ```CREATE EXTERNAL FILE FORMAT``` används för att ange formatet för filerna som innehåller de externa data. De innehåller text som avgränsas med ett eller flera tecken som kallas avgränsare. För demonstration lagras taxidata både som komprimerade data och gzip-komprimerade data.

    Kör dessa T-SQL-kommandon för att definiera två olika format: okomprimerat och komprimerat.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Skapa ett schema för det externa filformatet. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Skapa de externa tabellerna. Dessa tabeller refererar till data som lagras i Azure Blob Storage. Kör följande T-SQL-kommandon för att skapa flera externa tabeller som alla pekar mot den Azure-blob vi definierade tidigare i vår externa datakälla.

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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
```

### <a name="import-the-data-from-azure-blob-storage"></a>Importera data från Azure Blob Storage.

SQL Data Warehouse har stöd för en nyckelinstruktion som kallas CREATE TABLE AS SELECT (CTAS). Den här instruktionen skapar en ny tabell baserat på resultatet av en select-instruktion. Den nya tabellen har samma kolumner och datatyper som resultatet av select-instruktionen.  Detta är ett smidigt sätt att importera data från Azure Blob Storage till SQL Data Warehouse.

1. Kör det här skriptet för att importera dina data.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Visa data som laddas.

   Du läser in flera GB data och komprimerar dem till högpresterande klustrade Columnstore-index. Kör följande fråga som använder en dynamisk hanteringsvy (DMV) för att visa status för belastningen. När du har startat frågan kan du hämta lite kaffe och något att äta medan SQL Data Warehouse sköter grovjobbet.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
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
        nbr_files desc, 
        gb_processed desc;
    ```

3. Visa alla systemfrågor.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Se hur dina data läses in i Azure SQL Data Warehouse.

    ![Visa inlästa data](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## <a name="improve-query-performance"></a>Förbättra prestanda för frågor

Det finns flera sätt att förbättra frågeprestanda och få den snabba prestanda som SQL Data Warehouse har utformats för att ge.  

### <a name="see-the-effect-of-scaling-on-query-performance"></a>Se effekten av skalning på frågeprestanda 

Ett sätt att förbättra frågeprestanda är att skala resurser genom att ändra DWU-tjänstenivån för informationslagret. Varje tjänstenivå kostar mer, men du kan minska eller pausa resurser när som helst. 

I det här steget jämför du prestanda på två olika DWU-inställningar.

Vi ska först skala ned till 100 DWU:er för att få en uppfattning av hur en beräkningsnod kan prestera på egen hand.

1. Gå till portalen och välj SQL Data Warehouse.

2. Välj skala på bladet SQL Data Warehouse. 

    ![Skala DW från portalen](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Skala ned prestandafältet till 100 DWU och klicka på Spara.

    ![Skala och spara](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Vänta tills skalningen har slutförts.

    > [!NOTE]
    > Frågor kan inte köras medan du ändrar skalan. Skalning **stoppar** dina frågor som körs för närvarande. Du kan starta om dem när åtgärden är klar.
    >
    
5. Kör en genomsökningsåtgärd mot reseinformationen och välj den översta miljonen poster för alla kolumner. Om du är otålig och vill gå vidare snabbt kan du välja färre rader. Notera hur lång tid det tar att utföra den här åtgärden.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Skala tillbaka informationslagret till 400 DWU. Kom ihåg att varje 100 DWU lägger till en till beräkningsnod till ditt Azure SQL Data Warehouse.

7. Kör frågan igen! Du bör märka en stor skillnad. 

> [!NOTE]
> Eftersom SQL Data Warehouse använder massiv parallell bearbetning (MPP). Frågor som skannar eller utför analysfunktioner på miljoner rader utnyttjar den verkliga kraften i Azure SQL Data Warehouse.
>

### <a name="see-the-effect-of-statistics-on-query-performance"></a>Se effekten av statistik på frågeprestanda

1. Kör en fråga som kopplar datumtabellen med resetabellen

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    Den här frågan tar en stund eftersom SQL Data Warehouse måste blanda data innan den kan utföra kopplingen. Kopplingar behöver inte blanda data om de har utformats för att koppla data på samma sätt som de distribueras. Det är ett djupare ämne. 

2. Statistik gör skillnad. 
3. Kör den här instruktionen för att skapa statistik på de kopplade kolumnerna.

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

[Concurrency and workload management][] (Hantering av samtidiga körningar och arbetsbelastningar)

[Metodtips för Azure SQL Data Warehouse][]

[Query Monitoring][] (Frågeövervakning)

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse][] (Tio rekommendationer för att skapa ett storskaligt relationsinformationslager)

[Migrating Data to Azure SQL Data Warehouse][] (Migrera data till Azure SQL Data Warehouse)

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

