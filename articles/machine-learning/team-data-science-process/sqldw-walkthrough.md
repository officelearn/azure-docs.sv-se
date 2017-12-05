---
title: "Team vetenskap av data i praktiken: använda SQL Data Warehouse | Microsoft Docs"
description: "Processen för avancerade analyser och teknik i åtgärd"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 88ba8e28-0bd7-49fe-8320-5dfa83b65724
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev;weig
ms.openlocfilehash: 9c858427b01f7b94aae87136a46e1d9ae5e09a1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Team vetenskap av data i praktiken: använda SQL Data Warehouse
I den här självstudiekursen vi beskriver hur du skapar och distribuerar en maskininlärningsmodell med hjälp av SQL Data Warehouse (SQL DW) för en offentligt tillgängliga dataset--den [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset. Binär klassificering modellen konstrueras beräknar huruvida ett tips betalat för en resa och modeller för multiklass-baserad klassificering och regression beskrivs också som förutsäga fördelningen för tips belopp betald.

Förfarandet som följer den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) arbetsflöde. Vi visar hur du ställer in en datavetenskap miljö, så Läs in data till SQL DW och hur använder SQL DW eller bärbar dator med en IPython för att utforska data och tekniker funktioner till modellen. Därefter visar vi hur du skapar och distribuerar en modell med Azure Machine Learning.

## <a name="dataset"></a>NYC Taxi resor datauppsättningen
NYC Taxi resa data består av cirka 20GB komprimerat CSV-filer (~ 48GB okomprimerade), registrera mer än 173 miljoner enskilda resor och priser betalat för varje resa. Hämtning och Samlingsbibliotek platser och gånger anonymiserade hackare (drivrutin) licensnummer och antalet medallion (taxi's unikt id) innehåller varje resa-post. Data omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

1. Den **trip_data.csv** filen innehåller resa information, till exempel antal passagerare, hämtning och dropoff, resa varaktighet och resa längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Den **trip_fare.csv** filen innehåller information om avgiften betalat för varje förflyttning, till exempel betalningssätt, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala betald. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den **Unik nyckel** användas för att ansluta resa\_data och resa\_avgiften består av följande tre fält:

* medallion,
* hacka\_licens och
* hämtning\_datetime.

## <a name="mltasks"></a>Åtgärda tre typer av förutsägelse uppgifter
Vi formulera tre förutsägelse problem utifrån den *tips\_belopp* att illustrera tre typer av modeling uppgifter:

1. **Binär klassificering**: för att förutsäga om huruvida ett tips har betalat för en resa, d.v.s. en *tips\_belopp* som är större än 0 är ett positivt exempel, när en *tips\_belopp* $0 är ett exempel på negativt.
2. **Multiklass-baserad klassificering**: att förutsäga intervallet för betald för resan-tips. Vi dela den *tips\_belopp* i fem lagerplatser eller klasser:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regression uppgiften**: att förutsäga mängden tips för en resa.  

## <a name="setup"></a>Konfigurera Azure datavetenskap miljö för avancerade analyser
Följ dessa steg om du vill konfigurera datavetenskap för Azure-miljö.

**Skapa din egen Azure blob storage-konto**

* När du konfigurerar din egen Azure-blobblagring Välj geoplats för din Azure-blobblagring i eller så nära som möjligt **södra centrala USA**, vilket är där NYC Taxi data lagras. Data kopieras med hjälp av AzCopy från den offentliga blob storage-behållaren till en behållare i ditt eget lagringskonto. Närmare Azure blob-lagring är att södra centrala USA, desto snabbare kommer att slutföra uppgiften (steg 4).
* Skapa din egen Azure storage-konto genom att följa anvisningarna på [om Azure storage-konton](../../storage/common/storage-create-storage-account.md). Se till att göra anteckningar på värden för följande lagringskontouppgifter eftersom de behövs längre fram i den här genomgången.
  
  * **Lagringskontonamnet**
  * **Lagringskontonyckel**
  * **Behållarens namn** (som du vill att data lagras i Azure blob storage)

**Etablera din Azure SQL DW-instans.**
Följ dokumentationen på [skapa ett SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) att etablera en instans av SQL Data Warehouse. Kontrollera att du gör här ska på följande SQL Data Warehouse-autentiseringsuppgifter som ska användas i senare steg.

* **Servernamnet**: <server Name>. database.windows.net
* **Namn på SQLDW (Database)**
* **Användarnamn**
* **Lösenord**

**Installera Visual Studio och SQL Server Data Tools.** Instruktioner finns i [installera Visual Studio 2015 och SSDT (SQL Server Data Tools) för SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ansluta till din Azure SQL DW med Visual Studio.** Instruktioner finns i steg 1 och 2 i [Anslut till Azure SQL Data Warehouse med Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Kör följande SQL-fråga på den databas du skapade i ditt SQL Data Warehouse (i stället för frågan som anges i steg 3 i avsnittet connect) till **skapa en huvudnyckel**.
> 
> 

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Skapa en Azure Machine Learning-arbetsytan under din Azure-prenumeration.** Instruktioner finns i [skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Läsa in data till SQL Data Warehouse
Öppna en Windows PowerShell-Kommandotolken. Kör följande PowerShell-kommandon för att ladda ned exempel SQL skriptfiler som vi dela med dig på GitHub till en lokal katalog som du anger parametern *- DestDir*. Du kan ändra värdet för parametern *- DestDir* till en lokal katalog. Om *- DestDir* finns inte, kommer att skapas av PowerShell-skriptet.

> [!NOTE]
> Du kan behöva **kör som administratör** när du kör följande PowerShell-skript om din *DestDir* directory behöver administratörsbehörighet för att skapa eller skriva till den.
> 
> 

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Efter att utföra den aktuella arbetskatalogen ändras till *- DestDir*. Du ska kunna se skärmen som nedan:

![][19]

I din *- DestDir*, kör följande PowerShell-skript i administratörsläge:

    ./SQLDW_Data_Import.ps1

När PowerShell-skriptet körs för första gången, blir du ombedd att ange informationen från Azure SQL DW och Azure blob storage-konto. När det här PowerShell-skriptet har slutförts kommer körs för första gången autentiseringsuppgifterna du indata har skrivits till en konfigurationsfil SQLDW.conf i den aktuella arbetskatalogen. Den framtida körningen av den här PowerShell-skriptfil har möjlighet att läsa alla behövs parametrar från konfigurationsfilen. Om du behöver ändra vissa parametrar kan du välja att indataparametrar på skärmen vid Kommandotolken genom att ta bort den här konfigurationsfilen och mata in värden för parametrar som efterfrågas eller för att ändra parametervärden genom att redigera filen SQLDW.conf i din *- DestDir* directory.

> [!NOTE]
> För att undvika schemat namnet står i konflikt med de som redan finns i din Azure SQL DW vid läsning av parametrar direkt från filen SQLDW.conf läggs ett 3-siffriga slumptal till schemanamnet från filen SQLDW.conf som standard schemanamn för varje körning. PowerShell-skriptet kan efterfrågas ett schemanamn: Ange namnet användaren gottfinnande.
> 
> 

Detta **PowerShell-skript** filen utför följande aktiviteter:

* **Hämtar och installerar AzCopy**om AzCopy inte redan är installerad
  
        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Kopierar data till ditt privata blob storage-konto** från den offentliga blobben med AzCopy
  
        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Läser in data med Polybase (genom att köra LoadDataToSQLDW.sql) Azure SQL DW** från privata blob storage-konto med följande kommandon.
  
  * Skapa ett schema
    
          EXEC (''CREATE SCHEMA {schemaname};'');
  * Skapa en databasbegränsade autentiseringsuppgift
    
          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Skapa en extern datakälla för ett Azure storage-blob
    
          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
    
          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Skapa ett externt filformat för en csv-fil. Data är okomprimerade och fälten avgränsas med pipe-tecken.
    
          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (   
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS  
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Skapa extern avgiften och resa tabeller för NYC taxi dataset i Azure blob storage.
    
          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12     
          )  

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Läs in data från externa tabeller i Azure blob storage till SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Skapa data exempeltabell (NYCTaxi_Sample) och infoga data till den från att välja SQL-frågor för kommunikation och avgiften tabeller. (Vissa steg i den här genomgången behöver använda den här exempeltabell.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Den geografiska platsen för dina lagringskonton påverkar inläsningstiden.

> [!NOTE]
> Beroende på den geografiska platsen för ditt privata blob storage-konto processen för att kopiera data från en offentlig blob till ditt privata storage-konto kan ta ungefär 15 minuter eller mer, och processen för inläsning av data från storage-konto till din Azure SQL DW ta 20 minuter eller längre.  
> 
> 

Du måste bestämma vilka gör om du har dubbla käll- och filer.

> [!NOTE]
> Om CSV-filer som ska kopieras från den offentliga blob storage till privata blob storage-konto finns redan i ditt privata blob storage-konto, AzCopy tillfrågas du om du vill skriva över. Om du inte vill skriva över inkommande  **n**  när du tillfrågas. Om du vill skriva över **alla** av dem, ange **en** när du tillfrågas. Du kan också ange **y** att skriva över CSV-filer separat.
> 
> 

![Rita #21][21]

Du kan använda dina egna data. Om dina data i din lokala dator i verkligheten programmet använda du fortfarande AzCopy för att överföra lokala data till din privata Azure blob storage. Du behöver ändra den **källa** plats, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, i AzCopy-kommandot i PowerShell-skriptfil till den lokala katalogen som innehåller dina data.

> [!TIP]
> Om data finns redan i din privata Azure blob storage i verkligheten programmet kan du hoppa över steg AzCopy i PowerShell-skript och direkt överföra data till Azure SQL DW. Detta kräver ytterligare redigeringar av skriptet för att skräddarsy att formatet för dina data.
> 
> 

Detta Powershell-skript också ansluts i Azure SQL DW-information till datafiler utforskning exempel SQLDW_Explorations.sql, SQLDW_Explorations.ipynb och SQLDW_Explorations_Scripts.py så att dessa tre filer är redo att försökas omedelbart efter PowerShell-skriptet har slutförts.

När du har körts, visas skärmen som nedan:

![][20]

## <a name="dbexplore"></a>Datagranskning och funktionen teknikerna i Azure SQL Data Warehouse
I det här avsnittet vi utföra data från kartläggning av naturresurser och funktionen generation genom att köra SQL-frågor mot Azure SQL DW direkt med **Visual Studio Data Tools**. Alla SQL-frågor som används i det här avsnittet hittar du i exempelskript som heter *SQLDW_Explorations.sql*. Den här filen har redan hämtats till din lokala katalog av PowerShell-skriptet. Du kan också hämta det från [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Men filen i GitHub har inte Azure SQL DW-information inkopplad.

Ansluta till din Azure SQL DW Visual Studio med SQL DW-inloggningsnamn och lösenord och öppna den **SQL Object Explorer** bekräfta databasen och tabeller har importerats. Hämta den *SQLDW_Explorations.sql* fil.

> [!NOTE]
> Öppna frågeredigeraren en Parallel Data Warehouse (PDW) med den **ny fråga** kommandot när din PDW har valts i den **SQL Object Explorer**. Standardredigeraren för SQL-fråga stöds inte av PDW.
> 
> 

Här är typ av data från kartläggning av naturresurser och funktionen generation uppgifter som utförs i det här avsnittet:

* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök data quality longitud och latitud fält.
* Generera binära och multiklass-baserad klassificeringsetiketter baserat på de **tips\_belopp**.
* Generera funktioner och beräkning eller jämförelse resa avstånd.
* Koppla två tabeller och extrahera ett slumpmässigt prov som ska användas för att bygga modeller.

### <a name="data-import-verification"></a>Importera dataverifieringen
De här frågorna ger en snabb kontroll av antalet rader och kolumner i tabellerna fyllts tidigare med Polybases parallella bulk importera

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Utdata:** du bör få 173,179,759 rader och 14 kolumner.

### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: Resa distribution av medallion
Den här exempelfråga identifierar medallions (taxi numbers) som slutförda mer än 100 resor inom en angiven tidsperiod. Frågan skulle dra nytta av partitionerad tabellåtkomst eftersom den är villkorad av partitionsschemat för **hämtning\_datetime**. Frågar den fullständiga datauppsättningen blir också användning av partitionerad tabell och/eller index-sökning.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Utdata:** frågan ska returnera en tabell med rader som anger 13,369 medallions (taxibilar) och antalet resa slutförts av dem i 2013. Den sista kolumnen innehåller antalet turer som slutförts.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Undersökning: Resa distribution av medallion och hack_license
Det här exemplet identifierar medallions (taxi numbers) och hack_license siffror (drivrutiner) som slutförda mer än 100 resor inom en angiven tidsperiod.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Utdata:** frågan ska returnera en tabell med 13,369 rader att ange 13,369 car och drivrutin-ID som har slutfört mer att 100 resor i 2013. Den sista kolumnen innehåller antalet turer som slutförts.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Data utvärdering: Verifiera poster med felaktigt longituden eller latituden
Det här exemplet undersöker om något av fälten longituden eller latituden antingen innehåller ett ogiltigt värde (radian grader ska vara mellan-90 och 90), eller ha (0, 0) koordinater.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Utdata:** frågan returnerar 837,467 resor som har ogiltiga longituden eller latituden fält.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Undersökning: Lutad kontra inte lutad resor distribution
Det här exemplet hittar antalet turer som har lutad kontra numret som inte har lutad i en angiven tidsperiod (eller i den fullständiga datauppsättningen om täcker hela året som den har angetts här). Den här distributionen visar binära etikett distributionen senare användas för binär klassificering modellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Utdata:** frågan ska returnera följande tips frekvenser för år 2013: 90,447,622 lutad och 82,264,709 inte lutad.

### <a name="exploration-tip-classrange-distribution"></a>Undersökning: Tips klass-intervallet distribution
Det här exemplet beräknar fördelningen av tips intervall i en viss tidsperiod (eller den fullständiga datauppsättningen om täcker hela året). Detta är distributionen av klasserna etiketten som ska användas senare för multiklass-baserad klassificering modellering.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Utdata:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Undersökning: Beräkna och jämföra resa avstånd
Det här exemplet konverterar hämtning och Samlingsbibliotek longitud och latitud till SQL geografi pekar beräknar resa avståndet med SQL geografi punkter skillnaden och returnerar ett slumpvist urval av resultaten för jämförelse. I exempel begränsar resultat till giltiga koordinater endast med data quality assessment frågan omfattas tidigare.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Funktionen tekniker med hjälp av SQL-funktioner
SQL-funktioner kan ibland vara ett effektivt alternativ för funktionen tekniker. Vi har definierat en SQL-funktion för att beräkna direkt avståndet mellan platser för hämtning och dropoff i den här genomgången. Du kan köra följande SQL-skript i **Visual Studio Data Tools**.

Här är SQL-skript som definierar funktionen avstånd.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Här är ett exempel för att anropa den här funktionen om du vill generera funktioner i SQL-frågan:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Utdata:** den här frågan genererar en tabell (med 2,803,538 rader) med hämtning och dropoff latituderna och longitudes och motsvarande direkt avstånd i mil. Här är resultatet för första 3 rader:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Förbered data för modellskapandet
Följande fråga kopplingar i **nyctaxi\_resa** och **nyctaxi\_avgiften** tabeller, genererar en binär klassificering etikett **lutad**, flera klassen klassificering etikett **tips\_klassen**, och extraherar ett sampel från den kopplade fullständiga datauppsättningen. Beräkningarna görs genom att hämta en delmängd av resor baserat på pickup tid.  Den här frågan kan kopieras och klistras in direkt i den [Azure Machine Learning Studio](https://studio.azureml.net) [importera Data] [ import-data] -modulen för direkt datapåfyllning från SQL-databasinstans i Azure. Frågan utesluter poster med fel (0, 0) koordinater.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

När du är redo att fortsätta till Azure Machine Learning, kan du antingen:  

1. Spara slutliga SQL-frågan om du vill extrahera den exempeldata och kopiera / klistra in frågan direkt i en [importera Data] [ import-data] modul i Azure Machine Learning, eller
2. Spara provade och bakåtkompilerade data som du planerar att använda för modellen bygga i en ny SQL DW-tabell och använda den nya tabellen i den [importera Data] [ import-data] modul i Azure Machine Learning. PowerShell-skriptet i tidigare steg har gjort det åt dig. Du kan läsa direkt från den här tabellen i modulen importera Data.

## <a name="ipnb"></a>Datagranskning och funktionen tekniker i IPython anteckningsbok
Vi kommer att utföra datagranskning och funktionen generation använder båda Python och SQL-frågor mot SQL DW skapade tidigare i det här avsnittet. En exempel IPython bärbar dator med namnet **SQLDW_Explorations.ipynb** och en Python-skriptfil **SQLDW_Explorations_Scripts.py** har hämtats till din lokala katalog. De är också tillgängliga på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Dessa två filer är identiska i Python-skript. Skriptfilen Python tillhandahålls för dig om du inte har en bärbar dator IPython-server. Dessa två exempel Python filer är utformade under **Python 2.7**.

Den Azure SQL DW information som krävs i exemplet IPython anteckningsboken och Python-skriptfilen laddas ned till den lokala datorn har anslutits av PowerShell-skriptet tidigare. De är körbara utan några ändringar.

Om du redan har installerat en AzureML-arbetsyta, du direkt överföra exempel IPython anteckningsboken till tjänsten för AzureML IPython anteckningsboken och börja köra den. Här följer stegen för att överföra till tjänsten för AzureML IPython anteckningsboken:

1. Logga in på ditt AzureML arbetsområde på ”Studio” längst upp och klicka på ”bärbara datorer” till vänster på sidan.
   
    ![Rita #22][22]
2. Klicka på ”ny” i det nedre vänstra hörnet på sidan och välj ”Python 2”. Sedan ange ett namn på den bärbara datorn och klicka på kryssmarkeringen för att skapa den nya tomma IPython bärbara datorn.
   
    ![Rita #23][23]
3. Klicka på symbolen ”Jupyter” i det övre vänstra hörnet på den nya IPython bärbara.
   
    ![Rita #24][24]
4. Dra och släpp exempel IPython anteckningsboken till den **träd** i din AzureML IPython anteckningsboken tjänsten och klickar på **överför**. Sedan exemplet IPython anteckningsboken kommer att överföras till tjänsten för AzureML IPython bärbar dator.
   
    ![Rita #25][25]

För att kunna köra exemplet IPython bärbar dator eller i Python skriptfil, följande Python paket krävs. Om du använder tjänsten AzureML IPython anteckningsboken har paketen förinstallerat.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Den rekommenderade sekvensen när du skapar avancerade analytiska lösningar på AzureML med stora mängder data är följande:

* Läs i ett litet antal data till en ram i minnet data.
* Utföra vissa visualiseringar och explorations med hjälp av samplade data.
* Experimentera med funktionen tekniker med hjälp av samplade data.
* Använd Python för att skicka SQL-frågor direkt mot SQL DW för större datagranskning, databehandling och funktionen tekniker.
* Bestäm provtagning vara lämpliga för Azure Machine Learning modellskapandet.

Följande är några datagranskning, datavisualisering och funktionen engineering exempel. Mer data explorations finns i exemplet IPython anteckningsboken och Python-skriptet exempelfilen.

### <a name="initialize-database-credentials"></a>Initiera Databasautentiseringsuppgifter
Initiera anslutningsinställningarna databasen i följande variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Skapa databasanslutning
Här är den anslutningssträng som skapas en anslutning till databasen.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Rapporten antalet rader och kolumner i tabellen < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totalt antal rader = 173179759  
* Totalt antal kolumner = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Rapporten antalet rader och kolumner i tabellen < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totalt antal rader = 173179759  
* Totalt antal kolumner = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Läs i ett litet datasampel från SQL Data Warehouse-databas
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tid för att läsa exempeltabell är 14.096495 sekunder.  
Antal rader och kolumner hämtas = (1000, 21).

### <a name="descriptive-statistics"></a>Beskrivande statistik
Du är nu redo att utforska samplade data. Vi börjar med att titta på vissa beskrivande statistik för den **resa\_avstånd** (eller andra fält som du väljer att ange).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualiseringen: Exempel på ritytans
Vi titta på Låddiagram för resa avståndet visualisera quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Rita #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualiseringen: Distribution ritytans exempel
Områden som visualiserar distributionen och ett histogram för provade resa avstånd.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Rita #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualiseringen: Menyraden och rad områden
I det här exemplet vi bin resa avståndet i fem lagerplatser och visualisera binning resultat.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan ritas ovan bin-distribution i ett fält eller rad ritytans med:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Rita #3][3]

och

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Rita #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualiseringen: Scatterplot exempel
Visar vi punktdiagram ritytans mellan **resa\_tid\_i\_sek** och **resa\_avstånd** om det finns några korrelation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Rita #6][6]

På liknande sätt kan vi kontrollerar relationen mellan **hastighet\_kod** och **resa\_avstånd**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Rita #8][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Datagranskning på samplade data med hjälp av SQL-frågor i IPython anteckningsbok
I det här avsnittet förklarar vi data distributioner med hjälp av samplade data som sparas i den nya tabellen som vi skapade ovan. Observera att liknande explorations kan utföras med hjälp av de ursprungliga tabellerna.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Undersökning: Rapporterar antalet rader och kolumner i tabellen provade
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Undersökning: Lutad ej utlöses Distribution
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Undersökning: Tips klassen distribution
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Undersökning: Rita tips fördelningen av klassen
    tip_class_dist['tip_freq'].plot(kind='bar')

![Rita #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Undersökning: Dagliga distribution av resor
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Undersökning: Resa distribution per medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Undersökning: Resa distribution av medallion och hackare licens
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Undersökning: Fördelning av resa
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Undersökning: Resa avståndet distribution
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Undersökning: Betalning typen distribution
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Kontrollera den slutliga utformning av tabellen featurized
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Bygga modeller i Azure Machine Learning
Vi är nu redo att fortsätta att modellskapandet och distribution av modellen i [Azure Machine Learning](https://studio.azureml.net). Data är redo att användas i något av de förutsägelse problem som konstaterats tidigare, nämligen:

1. **Binär klassificering**: för att förutsäga om huruvida ett tips har betalat för en resa.
2. **Multiklass-baserad klassificering**: att förutsäga intervallet för tips betald enligt de tidigare definierade klasserna.
3. **Regression uppgiften**: att förutsäga mängden tips för en resa.  

Om du vill börja modellering logga in på ditt **Azure Machine Learning** arbetsytan. Om du inte har skapat machine learning-arbetsytan finns [skapa en arbetsyta för Azure ML](../studio/create-workspace.md).

1. Om du vill komma igång med Azure Machine Learning finns [vad är Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. Sidan Studio innehåller en mängd information, videor, självstudier, länkar till moduler referens och andra resurser. Mer information om Azure Machine Learning i [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

En typisk träningsexperiment består av följande steg:

1. Skapa en **+ ny** experiment.
2. Hämta data till Azure ML.
3. Förbearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela data i utbildning / / verifieringstesterna datauppsättningar (eller ha separata datauppsättningar för varje).
6. Välj en eller flera maskininlärningsalgoritmer beroende på learning problemet att lösa. T.ex. binär klassificering, multiklass-baserad klassificering, regression.
7. Träna en eller flera modeller med hjälp av utbildning dataset.
8. Poängsätta validering datauppsättningen med den tränade modeller.
9. Utvärdera modeller för att beräkna relevanta mätvärden för learning problemet.
10. Bra finjustera modeller och välj den bästa modellen för distribution.

I den här övningen har vi redan utforskade och utformad data i SQL Data Warehouse och valt exempelstorleken att mata in i Azure ML. Här är proceduren för att skapa en eller flera modeller för förutsägelse:

1. Hämta data till Azure ML med hjälp av den [importera Data] [ import-data] modulen är tillgängliga i den **Data ingående och utgående** avsnitt. Mer information finns i [importera Data] [ import-data] modulsida referens.
   
    ![Azure ML-importera Data][17]
2. Välj **Azure SQL Database** som den **datakällan** i den **egenskaper** panelen.
3. Ange DNS-namnet för databasen i den **Databasservernamnet** fältet. Format:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange den **databasnamnet** i motsvarande fält.
5. Ange den *användarnamn för SQL* i den **Server användarkontonamnet**, och *lösenord* i den **serverlösenord**.
7. I den **databasfrågan** redigera texten, klistrar in frågan som extraherar nödvändiga databasfält (inklusive eventuella beräknade fält, till exempel etiketter) och ned exempel data till den önskade provtagning.

Ett exempel på en binär klassificering experiment läsning av data direkt från SQL Data Warehouse-databasen är i bilden nedan (Kom ihåg att ersätta tabellen namn nyctaxi_trip och nyctaxi_fare med schemanamnet och tabellnamn som du använde i den här genomgången). Liknande experiment kan konstrueras för multiklass-baserad klassificering och regressionsproblem.

![Azure ML Train][10]

> [!IMPORTANT]
> För modellering data extrahering och samlar frågan exempel som i föregående avsnitt **alla etiketter för de tre modellering övningarna ingår i frågan**. Ett viktigt steg i (obligatoriskt) i varje modellering övningarna är att **undanta** onödiga etiketterna för de andra två problem och andra **mål minnesläckor**. Till exempel när du använder binär klassificering kan använda etiketten **lutad** och utelämna fälten **tips\_klassen**, **tips\_belopp**, och **totala\_belopp**. Dessa är målet minnesläckor eftersom de innebär tips betald.
> 
> Om du vill undanta alla onödiga kolumner eller mål minnesläckor, kan du använda den [Välj kolumner i datauppsättning] [ select-columns] modul eller [redigera Metadata][edit-metadata]. Mer information finns i [Välj kolumner i datauppsättning] [ select-columns] och [redigera Metadata] [ edit-metadata] referera sidor.
> 
> 

## <a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När modellen är klar, kan du enkelt distribuera det som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure ML web services finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst, måste du:

1. Skapa ett bedömningsprofil experiment.
2. Distribuera webbtjänsten.

Så här skapar du ett bedömningsprofil experiment från en **avslutad** utbildning experiment, klickar du på **skapa BEDÖMNINGEN EXPERIMENTERA** i Åtgärdsfältet lägre.

![Azure bedömningen][18]

Azure Machine Learning försöker skapa ett bedömningsprofil experiment som bygger på komponenterna för utbildning experimentet. I synnerhet att:

1. Spara den tränade modellen och ta bort modellen utbildningsmoduler.
2. Identifiera en logisk **inkommande port** som representerar det förväntade indata-schemat.
3. Identifiera en logisk **utgående port** att representera utdataschema förväntade web service.

När bedömningsprofil experimentet har skapats kan du granska den och göra justera efter behov. En typisk justering är att ersätta inkommande dataset och/eller fråga med en vilket utesluter etikett fält, eftersom dessa inte är tillgänglig när tjänsten har anropats. Det är också en bra idé att minska storleken på inkommande dataset och/eller fråga till några poster bara tillräckligt för att ange det inkommande schemat. För den utgående porten, är det vanligt att undanta alla inmatningsfält och bara ta med den **poängsatta etiketter** och **bedömas sannolikhet** i utdata med den [Välj kolumner i datauppsättning] [ select-columns] modul.

Ett exempel bedömningen experiment finns i bilden nedan. När du är klar att distribuera klickar du på den **publicera WEBBTJÄNSTEN** knappen i det nedre Åtgärdsfältet.

![Azure ML publicera][11]

## <a name="summary"></a>Sammanfattning
Om du vill Sammanfattningsvis vad vi har gjort i den här genomgången självstudiekursen, du har skapat ett Azure datavetenskap miljö, arbetat med en stor offentliga datauppsättning tar genom Team vetenskap av data, allt från datainsamling till modellen utbildning och sedan till den distribution av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformationen
Den här genomgången exempel och dess tillhörande skript och IPython notebook(s) delas av Microsoft under MIT-licensen. Kontrollera filen LICENSE.txt i katalogen exempelkoden på GitHub för mer information.

## <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi resor hämtningssidan](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxitransport resa Data av Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
