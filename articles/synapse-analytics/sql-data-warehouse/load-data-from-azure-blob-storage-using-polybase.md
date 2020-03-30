---
title: 'Handledning: Ladda New York Taxicab data'
description: Självstudien använder Azure Portal och SQL Server Management Studio för att läsa in New York Taxicab-data från en global Azure-blob för SQL Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f1614538f6ab735720d090f66fee0e017e96cf72
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346733"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Handledning: Ladda New York Taxicab dataset

Den här självstudien använder PolyBase för att läsa in New York Taxicab-data från ett globalt Azure blob storage-konto. I självstudierna används [Azure-portalen](https://portal.azure.com) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att: 

> [!div class="checklist"]
> * Skapa en SQL-pool i Azure-portalen
> * Skapade en brandväggsregel på servernivå på Azure-portalen
> * Ansluta till informationslagret med SSMS
> * Skapa en användare som utsetts för att läsa in data
> * Skapa externa tabeller för data i Azure blobblagring
> * Använda CTAS T-SQL-instruktionen för att läsa in data till informationslagret
> * Visa dataförloppet vid hämtning
> * Skapa statistik på nyligen inlästa data

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Skapa en tom databas

En SQL-pool skapas med en definierad uppsättning [beräkningsresurser](memory-concurrency-limits.md). Databasen skapas inom en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) och i en [logisk Azure SQL-server](../../sql-database/sql-database-features.md). 

Följ dessa steg för att skapa en tom databas. 

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.

2. Välj **Databaser** på den **nya** sidan och välj **Azure Synapse Analytics** under **Dagens** på den **nya** sidan.

    ![skapa ett informationslager](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Fyll i formuläret med följande information: 

   | Inställning            | Föreslaget värde       | Beskrivning                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Namn**            | mySampleDataWarehouse | Giltiga databasnamn finns i [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
   | **Prenumeration**   | Din prenumeration     | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup       | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
   | **Välj källa**  | Tom databas        | Anger att en tom databas ska skapas. Observera att ett informationslager är en typ av databas. |

    ![skapa ett informationslager](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Välj **Server** om du vill skapa och konfigurera en ny server för den nya databasen. Fyll i formuläret **Ny server** med följande information: 

    | Inställning                | Föreslaget värde          | Beskrivning                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Servernamn**        | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
    | **Logga in för serveradministratör** | Valfritt giltigt namn           | Giltiga inloggningsnamn finns i [Databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
    | **Lösenord**           | Valfritt giltigt lösenord       | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
    | **Location**           | Valfri giltig plats       | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![skapa databasserver](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Välj **Välj**.

6. Välj **Prestandanivå** om du vill ange om informationslagret är Gen1 eller Gen2 och antalet informationslagerenheter. 

7. För den här självstudien väljer du SQL pool **Gen2**. Skjutreglaget är inställt på **DW1000c** som standard.  Prova att flytta det uppåt och nedåt för att se hur det fungerar. 

    ![konfigurera prestanda](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Välj **Använd**.
9. I etableringsbladet väljer du en **sortering** för den tomma databasen. I de här självstudierna ska du välja standardvärdet. Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

11. Nu när du har fyllt i formuläret väljer du **Skapa** för att etablera databasen. Etableringen tar några minuter. 

12. Välj **Meddelanden för** att övervaka distributionsprocessen i verktygsfältet.
  
     ![avisering](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

En brandvägg på servernivå som förhindrar att externa program och verktyg ansluter till servern eller databaser på servern. Om du vill kan du lägga till brandväggsregler som tillåter anslutningar för specifika IP-adresser.  Följ dessa steg för att skapa en [brandväggsregel på servernivå](../../sql-database/sql-database-firewall-configure.md) för klientens IP-adress. 

> [!NOTE]
> SQL Database Warehouse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.

1. När distributionen är klar väljer du **SQL-databaser** från menyn till vänster och väljer sedan **mySampleDatabase** på **sql-databassidan.** Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade servernamnet (till exempel **mynewserver-20180430.database.windows.net)** och innehåller alternativ för ytterligare konfiguration. 

2. Kopiera det här fullständiga servernamnet för anslutning till servern och databaserna i efterföljande snabbstarter. Välj sedan servernamnet för att öppna serverinställningarna.

    ![hitta servernamn](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Välj det servernamn som du vill öppna serverinställningarna.

    ![serverinställningar](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Välj **Visa brandväggsinställningar**. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

    ![brandväggsregler för server](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Välj **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

6. Välj **OK** och stäng sedan sidan **Brandväggsinställningar**.

Nu kan du ansluta till SQL-servern och dess informationslager med den här IP-adress. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.  

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **AV** på den här sidan och välj sedan **Spara** för att inaktivera brandväggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade servernamnet för SQL-servern i Azure Portal. Du kommer att använda det fullständigt kvalificerade namnet senare när du ska ansluta till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Azure Synapse Analytics** på menyn till vänster och välj din databas på sidan Azure **Synapse Analytics.** 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet mynewserver-20180430.database.windows.net. 

    ![anslutningsinformation](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

I det här avsnittet används [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att upprätta en anslutning till Azure SQL-servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

    | Inställning        | Föreslaget värde                            | Beskrivning                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Servertyp    | Databasmotor                            | Det här värdet är obligatoriskt                                       |
    | servernamn    | Fullständigt kvalificerat servernamn            | Namnet ska vara ungefär så här: **mynewserver-20180430.database.windows.net**. |
    | Autentisering | SQL Server-autentisering                  | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här kursen. |
    | Inloggning          | Serveradministratörskontot                   | Detta är det konto som du angav när du skapade servern. |
    | lösenord       | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

    ![Anslut till server](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Välj **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

5. Expandera **Databaser** i Object Explorer. Expandera sedan **Systemdatabaser** och **Huvuddatabas** för att visa objekt i huvuddatabasen.  Expandera **mySampleDatabase** så visas objekten i den nya databasen.

    ![databasobjekt](./media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Skapa en användare för att läsa in data

Serveradministratörskontot är avsett för att utföra hanteringsåtgärder och är inte lämpligt för att köra frågor på användardata. Datainläsning är en minneskrävande åtgärd. Maximal minne definieras enligt [informationslagerenheter och](what-is-a-data-warehouse-unit-dwu-cdwu.md) [resursklass](resource-classes-for-workload-management.md) konfigurerad . 

Det är bäst att skapa en särskild inloggning och en särskild användare för inläsning av data. Lägg sedan till inläsningsanvändaren i en [resursklass](resource-classes-for-workload-management.md) som möjliggör en lämplig maximal minnesallokering.

Eftersom du för närvarande är ansluten som serveradministratör kan du skapa inloggningar och användare. Följ dessa steg för att skapa en inloggning och användare som kallas för **LoaderRC20**. Tilldela användaren resursklassen **staticrc20**. 

1.  I SSMS väljer du **mall för** att visa en rullgardinsmeny och väljer **Ny fråga**. Ett nytt frågefönster öppnas.

    ![Ny fråga i huvuddatabas](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. I frågefönstret anger du dessa T-SQL-kommandon för att skapa en inloggning och en användare som kallas för LoaderRC20 och ersätter 123STRONGpassword! med ditt eget lösenord. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Välj **Kör**.

4. Högerklicka på **mySampleDataWarehouse** och välj **Ny fråga**. Ett nytt frågefönster öppnas.  

    ![Ny fråga på exempelinformationslagret](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Ange följande T-SQL-kommandon för att skapa en databasanvändare som kallas för LoaderRC20 till inloggningen LoaderRC20. Den andra raden ger den nya användaren kontrollbehörighet på det nya informationslagret.  Dessa behörigheter påminner om att göra användaren till databasens ägare. Den tredje raden lägger till den nya användaren som medlem i [resursklassen](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Välj **Kör**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Ansluta till servern som inläsningsanvändare

Första steget mot att läsa in data är att logga in som LoaderRC20.  

1. I Objektutforskaren väljer du listrutan **Anslut** och väljer **Databasmotor**. Dialogrutan **Anslut till server** visas.

    ![Ansluta med ny inloggning](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Ange det fullständigt kvalificerade servernamnet och ange **LoaderRC20** som inloggning.  Ange lösenordet för LoaderRC20.

3. Välj **Anslut**.

4. När anslutningen är klar visas två serveranslutningar i Object Explorer. En anslutning som Serveradmin och en anslutning som MedRCLogin.

    ![Anslutningen lyckades](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Skapa externa tabeller för exempeldata

Du är redo att börja läsa in data till ditt nya informationslager. Den här självstudien visar hur du använder externa tabeller för att läsa in Taxi cab-data från En Azure Storage-blob. För framtida referens, om du vill lära dig hur du hämtar dina data till Azure blob storage eller läser in dem direkt från källan, läs [inläsningsöversikten](design-elt-data-loading.md).

Kör följande SQL-skript och ange information om de data som du vill läsa in. Informationen omfattar var informationen finns, formatet för innehållet i aktuella data och tabelldefinitionen för dessa data. 

1. I det föregående avsnittet loggade du in på ditt informationslager som LoaderRC20. Högerklicka på anslutningen LoaderRC20 i SSMS, och välj **Ny fråga**.  Ett nytt frågefönster visas. 

    ![Nytt fönster för inläsning av fråga](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Jämför ditt frågefönster med föregående bild.  Kontrollera att ditt nya frågefönster körs som LoaderRC20 och kör frågor på MySampleDataWarehouse-databasen. Använd det här frågefönstret för att utföra alla inläsningssteg.

3. Skapa en huvudnyckel för MySampleDataWarehouse-databasen. Du behöver bara skapa en huvudnyckel en gång per databas. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Kör instruktionen [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) för att definiera platsen för Azure-blobben. Det är här som dina externa taxi-data finns.  Om du vill köra ett kommando som du har lagt till i frågefönstret markerar du de kommandon som du vill köra och väljer **Kör**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Kör T-SQL-instruktionen [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) för att ange formateringsegenskaper och alternativ för filen med externa data. Den här instruktionen anger externa data och lagras som text, där värdena avgränsas med pipe-tecknet (”|”). Den externa filen komprimeras med Gzip. 

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

6.  Kör instruktionen [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql) för att skapa ett schema för ditt externa filformat. Schemat innehåller ett sätt att ordna de externa tabeller som du ska skapa.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Skapa de externa tabellerna. Tabelldefinitionerna lagras i informationslagret, men tabellerna refererar till data som lagras i Azure blob storage. Kör följande T-SQL-kommandon för att skapa flera externa tabeller som alla pekar mot den Azure-blob vi definierade tidigare i vår externa datakälla.

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
    );
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
    );
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
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. I Object Explorer ska du expandera mySampleDataWarehouse om du vill se en lista över externa tabeller som du just har skapat.

    ![Visa externa tabeller](./media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Läsa in data till informationslagret

I det här avsnittet används de externa tabeller som du just har definierat för att läsa in exempeldata från Azure Storage Blob.  

> [!NOTE]
> De här självstudierna läser in data direkt till den slutliga tabellen. I en produktionsmiljö använder du vanligtvis CREATE TABLE AS SELECT FÖR att läsa in till en mellanlagringstabell. Du kan utföra alla nödvändiga omvandlingar när data är i mellanlagringstabellen. Du kan använda instruktionen INSERT...SELECT om du vill lägga till data i mellanlagringstabellen i en produktionstabell. Mer information finns i [Infoga data i en produktionstabell](guidance-for-loading-data.md#inserting-data-into-a-production-table).

Skriptet använder T-SQL-instruktionen [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) för att läsa in data från Azure Storage Blob till nya tabeller i informationslagret. CTAS skapar en ny tabell baserat på resultatet av en SELECT-instruktion. Den nya tabellen har samma kolumner och datatyper som resultatet av select-instruktionen. När select-satsen väljer från en extern tabell importeras data till en relationstabell i informationslagret. 

1. Kör följande skript för att läsa in data till nya tabeller i informationslagret.

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

2. Visa data som laddas. Du läser in flera GB data och komprimerar dem till högpresterande klustrade columnstore-index. Kör följande fråga som använder en dynamisk hanteringsvy (DMV) för att visa status för belastningen. 

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
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

4. Se hur dina data läses in i ditt informationslager.

    ![Visa inlästa tabeller](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Autentisera med hanterade identiteter för att läsa in (valfritt)
Inläsning med PolyBase och autentisering via hanterade identiteter är den säkraste mekanismen och gör att du kan utnyttja slutpunkter för virtuella nätverkstjänster med Azure Storage. 

### <a name="prerequisites"></a>Krav
1.    Installera Azure PowerShell med den här [guiden](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.    Om du har ett allmänt v1- eller blob-lagringskonto måste du först uppgradera till allmänt v2 med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Du måste tillåta **betrodda Microsoft-tjänster för att komma åt det här lagringskontot** aktiverat under Azure Storage-kontobrand brandväggar och inställningar **för virtuella nätverk.** Se den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) för mer information.

#### <a name="steps"></a>Steg
1. I PowerShell **registrerar du SQL-servern** med Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
   
   1. Skapa ett **allmänt v2-lagringskonto** med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   >
   > - Om du har ett allmänt v1- eller blob-lagringskonto måste du **först uppgradera till v2** med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   
1. Under ditt lagringskonto navigerar du till **Åtkomstkontroll (IAM)** och väljer **Lägg till rolltilldelning**. Tilldela **RBAC-rollen för storage blob Data Contributor** till SQL Database-servern.

   > [!NOTE] 
   > Endast medlemmar med ägarbehörighet kan utföra det här steget. För olika inbyggda roller för Azure-resurser finns i den här [guiden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Polybase-anslutning till Azure Storage-kontot:**
  
   1. Skapa databasens begränsade autentiseringsuppgifter med **IDENTITY = "Managed Service Identity":**

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Det finns ingen anledning att ange SECRET med Azure Storage-åtkomstnyckel eftersom den här mekanismen använder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) under omfattar.
       > - IDENTITETSNAMNET ska vara **"Managed Service Identity"** för PolyBase-anslutning för att fungera med Azure Storage-konto.
   
   1. Skapa den externa datakällan som anger databasscopen med den hanterade tjänstidentiteten.
     
   1. Fråga som vanligt med hjälp av [externa tabeller](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

Se följande [dokumentation](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) om du vill konfigurera slutpunkter för virtuella nätverkstjänster för Azure Synapse Analytics. 

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för beräkningsresurser och data som du har läst in i ditt informationslager. Dessa faktureras separat. 

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkningen kommer du bara att debiteras för datalagringen och du kan återuppta beräkningen när du är redo att arbeta med dina data.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj ditt informationslager.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **Pausa.** När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort informationslagret så att du inte debiteras för beräkning eller lagring väljer du **Ta bort**.

4. Om du vill ta bort den SQL-server som du skapade markerar du **mynewserver-20180430.database.windows.net** i föregående bild och väljer sedan **Ta bort**.  Var försiktig: om du tar bort servern tas nämligen alla databaser som servern har tilldelats bort.

5. Om du vill ta bort resursgruppen markerar du **myResourceGroup**och väljer sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg 
I de här självstudierna lärde du dig att skapa ett informationslager och skapa en användare för att läsa in data. Du skapade externa tabeller för att definiera strukturen för data som lagras i Azure Storage Blob och använde sedan PolyBase-instruktionen CREATE TABLE AS SELECT för att läsa in data till informationslagret. 

Du gjorde detta:
> [!div class="checklist"]
> * Skapade ett informationslager på Azure-portalen
> * Skapade en brandväggsregel på servernivå på Azure-portalen
> * Anslöt till informationslagret med SSMS
> * Skapade en användare för inläsning av data
> * Skapade externa tabeller för data i Azure Storage Blob
> * Använde CTAS T-SQL-instruktionen för att läsa in data till informationslagret
> * Visade förloppet för data under inläsning
> * Skapade statistik på nyligen inlästa data

Gå vidare till utvecklingsöversikten om du vill lära dig hur du migrerar en befintlig databas till Azure Synapse Analytics.

> [!div class="nextstepaction"]
> [Utforma beslut om att migrera en befintlig databas till Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)
