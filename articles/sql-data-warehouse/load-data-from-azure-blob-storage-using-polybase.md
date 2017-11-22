---
title: Ladda Polybase data - Azure Storage Blob till Azure SQL Data Warehouse | Microsoft Docs
description: "En självstudiekurs som använder Azure portal och SQL Server Management Studio för att läsa in New York tag taxi data från Azure blobblagring till Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: 
author: ckarst
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.custom: mvc,develop data warehouses
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 11/17/2017
ms.author: cakarst
ms.reviewer: barbkess
ms.openlocfilehash: fe3ea6c22fafad0d0dcf611ceb365a2ebca80011
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2017
---
# <a name="use-polybase-to-load-data-from-azure-blob-storage-to-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa data från Azure blobblagring till Azure SQL Data Warehouse

PolyBase är standard inläsning av teknik för att hämta data till SQL Data Warehouse. I den här kursen använder du PolyBase för att läsa New York tag taxi data från Azure blobblagring till Azure SQL Data Warehouse. I självstudiekursen används den [Azure-portalen](https://portal.azure.com) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) till: 

> [!div class="checklist"]
> * Skapa ett data warehouse i Azure-portalen
> * Konfigurera en brandväggsregel på servernivå i Azure-portalen
> * Anslut till data warehouse med SSMS
> * Skapa en användare som utsetts för inläsning av data
> * Skapa externa tabeller för data i Azure blob storage
> * Använd CTAS T-SQL-instruktion för att läsa in data till datalagret
> * Visa förloppet för data som har hämtats
> * Skapa statistik på nyinlästa data

Om du inte har en Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar den här självstudiekursen, hämta och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-sql-data-warehouse"></a>Skapa en tom SQL data warehouse

Ett Azure SQL data warehouse har skapats med en definierad uppsättning [beräkningsresurser](performance-tiers.md). Databasen har skapats i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL-server](../sql-database/sql-database-features.md). 

Följ dessa steg om du vill skapa en tom SQL data warehouse. 

1. Klicka på den **ny** knappen i det övre vänstra hörnet i Azure-portalen.

2. Välj **databaser** från den **ny** och väljer **SQL Data Warehouse** under **aktuell** på den **ny**sidan.

    ![Skapa ett data warehouse](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Fyll i formuläret SQL Data Warehouse med följande information:   

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Databasnamn** | mySampleDataWarehouse | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Välj källa** | Tom databas | Anger om du vill skapa en tom databas. Observera att ett informationslager är en typ av databas.|

    ![Skapa ett data warehouse](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Klicka på **Server** för att skapa och konfigurera en ny server för den nya databasen. Fyll i den **nytt serverformulär** med följande information: 

    | Inställning | Föreslaget värde | Beskrivning | 
    | ------- | --------------- | ----------- |
    | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
    | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och specialtecken. |
    | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![Skapa database-server](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klicka på **Välj**.

6. Klicka på **prestandanivån** att ange om datalagret har optimerats för elasticitet eller beräkning och antalet data warehouse enheter. 

7. Den här självstudiekursen, Välj den **optimerade för elasticitet** tjänstnivån. Skjutreglaget, som standard är inställt på **DW400**.  Försök att flytta uppåt och nedåt för att se hur det fungerar. 

    ![Konfigurera prestanda](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klicka på **Använd**.
9. I SQL Data Warehouse-sidan väljer du en **sorteringen** för tom databas. Använd standardvärdet för den här kursen. Mer information om sorteringar finns [sorteringar](/sql/t-sql/statements/collations.md)

11. Nu när du har fyllt i SQL Database-formuläret klickar du på **Skapa** så att databasen etableras. Etableringen tar några minuter. 

    ![Klicka på Skapa](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

12. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.
    
     ![avisering](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Tjänsten SQL Data Warehouse skapar en brandvägg på servernivå som förhindrar externa program och verktyg från att ansluta till servern eller en databas på servern. Du kan lägga till brandväggsregler som tillåter anslutning för specifika IP-adresser för att tillåta anslutningar.  Följ dessa steg för att skapa en [brandväggsregel på servernivå](../sql-database/sql-database-firewall-configure.md) för din klients IP-adress. 

> [!NOTE]
> SQL Data Warehouse kommunicerar via port 1433. Om du försöker ansluta från ett företagsnätverk kanske utgående trafik via port 1433 inte av ditt nätverks brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.
>

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och klickar sedan på **mySampleDatabase** på sidan **SQL-databaser**. På översiktssidan för din databas öppnas och visar fullständigt kvalificerade servernamnet (exempelvis **mynewserver 20171113.database.windows.net**) och innehåller alternativ för ytterligare konfiguration. 

2. Kopiera det här fullständiga servernamnet för anslutning till servern och databaserna i efterföljande snabbstarter. Klicka på servernamnet för att öppna serverinställningarna för.

    ![hitta servernamn](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Klicka på servernamnet för att öppna serverinställningarna för.

    ![serverinställningar](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Klicka på **visa brandväggsinställningar**. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

    ![brandväggsregler för server](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Nu kan du ansluta till SQLServer och dess datalager med den här IP-adress. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.  

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Klicka på **OFF** på den här sidan och klicka sedan på **spara** att inaktivera brandväggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade servernamnet för SQLServer i Azure-portalen. Senare används det fullständigt kvalificerade namnet när du ansluter till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet mynewserver 20171113.database.windows.net. 

    ![anslutningsinformation](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Anslut till servern som serveradministratör

Det här avsnittet använder [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) för att upprätta en anslutning till Azure SQL-servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

    | Inställning      | Föreslaget värde | Beskrivning | 
    | ------------ | --------------- | ----------- | 
    | Servertyp | Databasmotor | Det här värdet krävs |
    | servernamn | Fullständigt kvalificerat servernamn | Namnet ska vara ungefär så här: **mynewserver 20171113.database.windows.net**. |
    | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här kursen. |
    | Inloggning | Serveradministratörskontot | Detta är det konto som du angav när du skapade servern. |
    | Lösenord | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

    ![Anslut till server](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

5. I Object Explorer, expandera **databaser**. Expandera **systemdatabaser** och **master** att visa objekt i master-databasen.  Expandera **mySampleDatabase** visa objekten i den nya databasen.

    ![objekt i databasen](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Skapa en användare för att läsa in data

Administratörskonto server är avsedd att utföra hanteringsåtgärder och är inte lämpligt för att köra frågor på användardata. Data läses in vanligtvis kräver mycket minne. [Minne maxkapacitet](performance-tiers.md#memory-maximums) definieras enligt [prestandanivån](performance-tiers.md), och [resursklassen](resource-classes-for-workload-management.md). 

Det är bäst att skapa en inloggning och användaren som är dedikerad för inläsning av data. Lägg till användaren läser in en [resursklassen](resource-classes-for-workload-management.md) som gör att en lämplig maximala minnesallokering.

Eftersom du är ansluten som serveradministratören, kan du skapa inloggningar och användare. Följ dessa steg för att skapa en inloggning och användaren som kallas **LoaderRC20**. Tilldela användare till den **staticrc20** resursklassen. 

1.  Högerklicka i SSMS, **master** att visa en nedrullningsbar meny, och välj **ny fråga**. Ett nytt frågefönster öppnas.

    ![Ny fråga i master](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Ange dessa T-SQL-kommandon för att skapa en inloggning och som heter LoaderRC20, ersätter du ditt eget lösenord för a123STRONGpassword! i frågefönstret. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Klicka på **Kör**.

4. Högerklicka på **mySampleDataWarehouse**, och välj **ny fråga**. En ny fråga öppnas.  

    ![Ny fråga på exempel datalagret](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)
 
5. Ange följande T-SQL-kommandon för att skapa en databasanvändare som heter LoaderRC20 för LoaderRC20 inloggningen. Den andra raden ger den nya användaren behörighet på nytt datalager.  Dessa behörigheter liknar gör användaren ägare till databasen. Den tredje raden lägger till den nya användaren som en medlem i staticrc20 [resursklassen](resource-classes-for-workload-management.md).

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Klicka på **Kör**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Ansluta till servern som läser in användare

Första steget mot att data läses in är att logga in som LoaderRC20.  

1. I Object Explorer, klickar du på den **Anslut** nedrullningsbara menyn, Välj **databasmotorn**. Den **Anslut till Server** dialogrutan visas.

    ![Ansluta med ny inloggning](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Ange det fullständiga servernamnet, men nu ange **LoaderRC20** som inloggningen.  Ange lösenordet för LoaderRC20.

3. Klicka på **Anslut**.

4. När anslutningen är klar, visas två anslutningar i Object Explorer. En anslutning som ServerAdmin och en anslutning som MedRCLogin.

    ![Anslutningen är klar](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Skapa externa tabeller för exempeldata

Du är redo att börja läsa in data i ditt nya data warehouse. Den här kursen visar hur du använder [Polybase](/sql/relational-databases/polybase/polybase-guide.md) att läsa in New York City taxi cab data från en Azure storage blob. Om du vill lära dig hur du får dina data till Azure Blob Storage eller hur du läser in dem direkt från källan till SQL Data Warehouse för framtida bruk går du till [översikten över inläsning](sql-data-warehouse-overview-load.md).

Kör följande SQL anger skript du information om data du vill läsa in. Informationen omfattar var informationen finns, formatet för innehållet i data och tabelldefinitionen för data. 

1. I det föregående avsnittet inloggad du på ditt data warehouse som LoaderRC20. Högerklicka på anslutningen LoaderRC20 i SSMS, och välj **ny fråga**.  Ett nytt frågefönster visas. 

    ![Nya inläsning frågefönstret](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Jämför din frågefönstret till föregående bild.  Kontrollera din nytt frågefönster körs som LoaderRC20 och utför frågor på MySampleDataWarehouse databasen. Använd den här frågefönstret för att utföra alla steg som lästes in.

3. Skapa en huvudnyckel för databasen MySampleDataWarehouse. Du behöver bara skapa en huvudnyckel en gång per databas. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Kör följande [Skapa extern DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql.md) instruktionen för att definiera platsen för Azure-blobben. Det här är platsen för externa taxi cab-data.  Markera de kommandon som du vill köra och klicka på om du vill köra ett kommando som du har bifogats till frågefönstret **kör**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Kör följande [skapa externt FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql.md) T-SQL-instruktionen för att ange formatering egenskaper och alternativ för filen externa data. Den här instruktionen anger externa data lagras som text och avgränsas med pipe ('| ') tecken. Den externa filen komprimeras med Gzip. 

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

6.  Kör följande [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql.md) instruktionen för att skapa ett schema för din externa filformat. Schemat innehåller ett sätt att ordna externa tabeller som du ska skapa.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Skapa de externa tabellerna. Tabellen lagras definitionerna i SQL Data Warehouse, men tabellerna referera till data som lagras i Azure blob storage. Kör följande T-SQL-kommandon för att skapa flera externa tabeller som alla pekar mot den Azure-blob vi definierade tidigare i vår externa datakälla.

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

8. Expandera mySampleDataWarehouse om du vill se en lista över externa tabeller som du just har skapat i Object Explorer.

    ![Visa externa tabeller](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Läs in data till datalagret

Det här avsnittet använder externa tabeller som du precis har definierats för att läsa in exempeldata från Azure Storage Blob till SQL Data Warehouse.  

Skriptet använder den [Skapa tabell AS Välj (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL-instruktion för att läsa in data från Azure Storage Blob till nya tabeller i datalagret. CTAS skapar en ny tabell baserat på resultatet av en select-instruktion. Den nya tabellen har samma kolumner och datatyper som resultatet av select-instruktionen. När select-instruktionen väljer från en extern tabell, importerar SQL Data Warehouse data i en relationsdatabas tabell i datalagret. 

1. Kör följande skript för att läsa in data till nya tabeller i datalagret.

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

2. Visa data som laddas. Du läser in flera GB data och komprimerar dem till högpresterande klustrade Columnstore-index. Kör följande fråga som använder en dynamisk hanteringsvy (DMV) för att visa status för belastningen. När du har startat frågan kan du hämta lite kaffe och något att äta medan SQL Data Warehouse sköter grovjobbet.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024 as gb_processed
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

4. Få se informationen snyggt läses in i ditt data warehouse.

    ![Visa tabellerna](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="create-statistics-on-newly-loaded-data"></a>Skapa statistik på nyinlästa data

SQL Data Warehouse skapar och uppdaterar inte statistik automatiskt. För att få en hög frågeprestanda är det därför viktigt att skapa statistik för varje kolumn av varje tabell efter den första inläsningen. Det är också viktigt att uppdatera statistiken efter att det har skett betydande förändringar.

1. Köra dessa kommandon för att skapa statistik på kolumner som sannolikt kommer att användas i kopplingar.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Att debiteras du för beräkningsresurser och data som du har lästs in i ditt data warehouse. Dessa faktureras separat. 

- Om du vill skydda data i lagring kan du pausa beräkning när du inte använder datalagret. Genom att pausa beräkning kommer du bara att kostnad för lagring av data och du kan återuppta beräkningen när du är redo att arbeta med data.
- Om du vill ta bort framtida avgifter kan du ta bort datalagret. 

Följ dessa steg för att rensa resurser som du önskar.

1. Logga in på den [Azure-portalen](https://portal.azure.com), klicka på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkning, klickar du på den **pausa** knappen. När datalagret har pausats, visas en **starta** knappen.  Om du vill återuppta databearbetning, klickar du på **starta**.

3. Ta bort datalagret så att du inte kommer att debiteras för beräkning och lagring genom att klicka på **ta bort**.

4. Ta bort SQL-server som du har skapat, klicka på **mynewserver 20171113.database.windows.net** i föregående bild och klicka sedan på **ta bort**.  Var försiktig med detta som tar bort servern tas bort alla databaser som har tilldelats servern.

5. Ta bort resursgruppen, klicka på **myResourceGroup**, och klicka sedan på **ta bort resursgruppen**.

## <a name="next-steps"></a>Nästa steg 
I kursen får du har lärt dig hur du skapar ett data warehouse och skapa en användare för att läsa in data. Du har skapat externa tabeller för att definiera strukturen för data som lagras i Azure Storage Blob och sedan används PolyBase CREATE TABLE AS SELECT-instruktion för att läsa in data till datalagret. 

Du gjorde detta:
> [!div class="checklist"]
> * Skapa ett data warehouse i Azure-portalen
> * Konfigurera en brandväggsregel på servernivå i Azure-portalen
> * Ansluten till data warehouse med SSMS
> * Skapa en användare som utsetts för inläsning av data
> * Skapa externa tabeller för data i Azure Storage Blob
> * Används CTAS T-SQL-instruktion för att läsa in data till datalagret
> * Visa förloppet för data som har hämtats
> * Skapade statistik på nyinlästa data

Gå till översikt över migrering att lära dig hur du migrerar en befintlig databas till SQL Data Warehouse.

> [!div class="nextstepaction"]
>[Lär dig hur du migrerar en befintlig databas till SQL Data Warehouse](sql-data-warehouse-overview-migrate.md)
