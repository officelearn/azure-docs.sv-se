---
title: Skapa och distribuera en modell med SQL Data Warehouse - Team Data Science Process
description: Skapa och distribuera en maskininlärningsmodell med SQL Data Warehouse med en datauppsättning som är allmänt tillgängliga.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d9e59a11957e8d108ae86ebba157a06129efa58
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111559"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Team Data Science Process i praktiken: använda SQL Data Warehouse
I de här självstudierna vi vägleder genom att skapa och distribuera en maskininlärningsmodell med SQL Data Warehouse (SQL DW) för en datauppsättning som allmänt tillgängliga – den [NYC Taxi kommunikation](http://www.andresmh.com/nyctaxitrips/) datauppsättning. Binära klassificeringsmodellen konstrueras förutsäger huruvida ett tips är betalas för en resa och modeller för multiklass-baserad klassificering och regression beskrivs som förutsäga fördelningen för tips belopp som har betalats.

Förfarandet som följer den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) arbetsflöde. Vi visar hur du ställer in en datavetenskapsmiljö in data i SQL DW och använda hur SQL DW eller en IPython Notebook för att utforska data och -tekniker funktioner i modellen. Sedan visar vi hur du skapar och distribuerar en modell med Azure Machine Learning.

## <a name="dataset"></a>NYC Taxi och RETUR-datauppsättning
NYC Taxi resedata består av cirka 20GB komprimerat CSV-filer (~ 48GB okomprimerad), registrera mer än 173 miljoner enskilda kommunikation och priser betalda för varje resa. Varje resa post innehåller hämtning och dropoff platser och gånger, maskerade hack (drivrutin) licensnummer och antalet medallion (taxi's unikt id). Informationen som täcker alla kommunikation i år 2013 och anges i följande två datauppsättningar för varje månad:

1. Den **trip_data.csv** filen innehåller resans information, till exempel antalet passagerare, hämtning och dropoff, resans varaktighet och resans längd. Här följer några Exempelposter:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Den **trip_fare.csv** filen innehåller information om avgiften betalat för varje förflyttning, till exempel betalningstypen, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala beloppet som betalas. Här följer några Exempelposter:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den **Unik nyckel** används för att ansluta till resans\_data och resans\_avgiften består av följande tre fält:

* medallion,
* hack\_licens och
* upphämtning\_datetime.

## <a name="mltasks"></a>Åtgärda tre typer av uppgifter för förutsägelse
Vi formulera tre förutsägelse problem baserat på den *tips\_belopp* att illustrera tre typer av modellering uppgifter:

1. **Binär klassificering**: För att förutsäga om ett tips har betalat för en resa t.ex. en *tips\_belopp* som är större än $0 är ett positivt exempel, medan en *tips\_belopp* $0 är ett exempel på negativt.
2. **Multiklass-baserad klassificering**: Att förutsäga vilka tips som har betalat för resan. Vi dela upp den *tips\_belopp* i fem lagerplatser eller klasser:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regression uppgift**: Att förutsäga mängden tips som har betalat för en resa.

## <a name="setup"></a>Konfigurera Azure data science-miljö för avancerad analys
Följ dessa steg om du vill konfigurera din Azure Data Science-miljö.

**Skapa din egen Azure blob storage-konto**

* När du etablerar ditt eget Azure blob storage, Välj geoplatsbaserad för Azure blob storage i eller så nära som möjligt till **södra centrala USA**, vilket är NYC Taxi-data ska lagras. Data kommer att kopieras med hjälp av AzCopy från den offentliga blob storage-behållaren till en behållare i ditt eget lagringskonto. Ju närmare ditt Azure blob storage är att södra centrala USA, desto snabbare kommer att slutföra uppgiften (steg 4).
* För att skapa din egen Azure storage-konto, följer du stegen som beskrivs i [om Azure storage-konton](../../storage/common/storage-create-storage-account.md). Glöm inte att göra anteckningar på värden för följande autentiseringsuppgifterna för lagringskontot eftersom de behövs längre fram i den här genomgången.

  * **Lagringskontonamn**
  * **Lagringskontonyckel**
  * **Behållarnamn** (som du vill att data ska lagras i Azure blob storage)

**Etablera din Azure SQL DW-instans.**
Dokumentationen på [skapa ett SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) att etablera en instans av SQL Data Warehouse. Kontrollera att du har ändrat beteckningar på följande SQL Data Warehouse-autentiseringsuppgifter som ska användas i senare steg.

* **Servernamn**: <server Name>. database.windows.net
* **Namn på SQLDW (databas)**
* **Användarnamn**
* **Lösenord**

**Installera Visual Studio och SQL Server Data Tools.** Anvisningar finns i [installera Visual Studio 2015 och/eller SSDT (SQL Server Data Tools) för SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Anslut till din Azure SQL DW med Visual Studio.** Instruktioner finns i steg 1 och 2 i [Anslut till Azure SQL Data Warehouse med Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Kör följande SQL-fråga på den databas du skapade i SQL Data Warehouse (i stället för den angivna i steg 3 i avsnittet connect frågan) till **skapa en huvudnyckel**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Skapa en Azure Machine Learning-arbetsyta för din Azure-prenumeration.** Anvisningar finns i [skapar en Azure Machine Learning-arbetsyta](../studio/create-workspace.md).

## <a name="getdata"></a>Läsa in data till SQL Data Warehouse
Öppna en Windows PowerShell kommando-konsol. Kör följande PowerShell-kommandon för att ladda ned exempel SQL skriptfiler som vi delar med dig på GitHub till en lokal katalog som du anger med parametern *- DestDir*. Du kan ändra värdet för parametern *- DestDir* till en lokal katalog. Om *- DestDir* finns inte, kommer att skapas av PowerShell-skriptet.

> [!NOTE]
> Du kan behöva **kör som administratör** när du kör följande PowerShell-skript om din *DestDir* directory behöver administratörsbehörighet att skapa eller skriva till den.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

När åtgärden har körts, din aktuella arbetskatalog ändras till *- DestDir*. Du bör kunna skärm som den här:

![Aktuella fungerande katalogändringar][19]

I din *- DestDir*, kör följande PowerShell-skript i administratörsläge:

    ./SQLDW_Data_Import.ps1

När PowerShell-skriptet körs för första gången blir du ombedd att ange informationen från din Azure SQL DW och Azure blob storage-kontot. När det här PowerShell-skriptet har slutförts ska körs för första gången autentiseringsuppgifterna du indata ha skrivits till en konfigurationsfil SQLDW.conf i den aktuella arbetskatalogen. Den framtida körningen av den här PowerShell-skriptfil har möjlighet att läsa alla nödvändiga parametrar från konfigurationsfilen. Om du vill ändra vissa parametrar kan du välja att ange parametrarna på skärmen vid Kommandotolken genom att radera den här konfigurationsfilen och mata in värden för parametrar som efterfrågas eller ändra parametrarnas värden genom att redigera filen SQLDW.conf i din *- DestDir* directory.

> [!NOTE]
> För att undvika schemat namnet står i konflikt med de som redan finns i din Azure SQL DW vid läsning av parametrar direkt från filen SQLDW.conf har ett 3-siffriga slumptal lagts till schemanamnet från filen SQLDW.conf som standard schemanamn för varje körning. PowerShell-skriptet kan efterfrågas ett schemanamn: namnet kan anges efter användaren gottfinnande.
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
* **Kopierar data till privat blob storage-kontot** från den offentliga blobben med AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Läser in data med Polybase (genom att köra LoadDataToSQLDW.sql) till din Azure SQL DW** från ditt privata blob storage-konto med följande kommandon.

  * Skapa ett schema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Skapa en databasbegränsade autentiseringsuppgifter

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Skapa en extern datakälla för en Azure storage blob

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
  * Skapa ett externt filformat för en csv-fil. Data är okomprimerade och fälten avgränsas med pipe-tecknet.

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
  * Skapa extern avgiften och resans tabeller för NYC taxi datauppsättningen i Azure blob storage.

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

    - Läsa in data från externa tabeller i Azure blob storage till SQL Data Warehouse

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

    - Skapa en data exempeltabell (NYCTaxi_Sample) och infoga data till den från att välja SQL-frågor på tabellerna resa och avgiften. (Vissa steg i den här genomgången behöver använda den här exempeltabell.)

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

Den geografiska platsen i dina lagringskonton påverkar snabbare.

> [!NOTE]
> Beroende på den geografiska platsen för ditt privata blob storage-konto, hur du kopierar data från en offentlig blob till ditt privata lagringskonto kan ta cirka 15 minuter eller ännu längre tid, och hur du läser in data från ditt lagringskonto till din Azure SQL DW kan ta 20 minuter eller längre.
>
>

Du måste bestämma vilka gör om du har dubblettfiler för källa och mål.

> [!NOTE]
> Om CSV-filer som ska kopieras från den offentliga blob storage till privat blob storage-kontot finns redan i ditt privata blob storage-konto, AzCopy tillfrågas du om du vill skriva över. Om du inte vill skriva över indata **n** när du tillfrågas. Om du vill skriva över **alla** , ange **en** när du tillfrågas. Du kan också ange **y** att skriva över CSV-filer separat.
>
>

![Utdata från AzCopy][21]

Du kan använda dina egna data. Om dina data är i den lokala datorn i ditt program i verkligheten kan använda du fortfarande AzCopy för att ladda upp data på plats till ditt privata Azure blob storage. Du behöver bara ändra den **källa** plats, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, i AzCopy-kommandot i PowerShell-skriptfil till den lokala katalogen som innehåller dina data.

> [!TIP]
> Om dina data finns redan i ditt privata Azure blob storage i ditt program i verkligheten, kan du hoppa över AzCopy-steget i PowerShell-skript och direkt överföra data till Azure SQL DW. Detta kräver ytterligare redigeringar av skript för att skräddarsy den efter formatet för dina data.
>
>

Den här PowerShell.skript också ansluts i Azure SQL DW-information till datafiler utforskning exempel SQLDW_Explorations.sql och SQLDW_Explorations.ipynb SQLDW_Explorations_Scripts.py så att dessa tre filer är redo att prova omedelbart efter PowerShell-skriptet har körts.

Efter en lyckad körning visas skärmen som den här:

![Utdata för en lyckad skriptkörningen][20]

## <a name="dbexplore"></a>Datagranskning och de funktioner i Azure SQL Data Warehouse
I det här avsnittet ska vi utföra genereringen av data utforskning och funktionen genom att köra SQL-frågor mot Azure SQL DW direkt med hjälp av **Dataverktyg för Visual Studio**. Alla SQL-frågor som används i det här avsnittet finns i exempelskriptet med namnet *SQLDW_Explorations.sql*. Den här filen har redan hämtats till din lokala katalog med PowerShell-skriptet. Du kan också hämta den från [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Men filen i GitHub har inte Azure SQL DW informationen inkopplad.

Ansluta till din Azure SQL DW med Visual Studio med SQL DW-inloggningsnamn och lösenord och öppna den **SQL Object Explorer** att bekräfta den databasen och tabeller har importerats. Hämta den *SQLDW_Explorations.sql* fil.

> [!NOTE]
> Öppna en Parallel Data Warehouse (PDW) frågeredigeraren genom att använda den **ny fråga** kommandot medan din PDW väljs i den **SQL Object Explorer**. Standard SQL-frågeredigeraren stöds inte av PDW.
>
>

Här är typ av data utforskning och funktionen generation uppgifter utförs i det här avsnittet:

* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök datakvaliteten fält för longitud och latitud.
* Generera binära och inom klassificeringsetiketter baserat på den **tips\_belopp**.
* Skapa funktioner och beräkning/jämför resans avstånd.
* Koppla två tabeller och extrahera ett slumpmässigt urval som ska användas för att skapa modeller.

### <a name="data-import-verification"></a>Importera dataverifieringen
De här frågorna ge en snabb kontroll av antalet rader och kolumner i tabellerna som fyllts i tidigare med hjälp av Polybases parallella bulk importera

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Utdata:** Du bör få 173,179,759 rader och 14 kolumner.

### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Resans distribution enligt medallion
Den här exempelfråga identifierar medallions (taxi-nummer) som slutförts fler än 100 kommunikation inom en angiven tidsperiod. Frågan skulle ha nytta av partitionerade tabellåtkomst eftersom det villkor som partitionsschemat för **upphämtning\_datetime**. Fråga hela datauppsättningen innebär även att använda partitionerade tabellen och/eller index-genomsökning.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Utdata:** Frågan bör returnera en tabell med rader som anger 13,369 medallions (taxibilar) och antalet resans slutförts av dem i 2013. Den sista kolumnen innehåller antal kommunikation har slutförts.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Utforskning: Resans distribution enligt medallion och hack_license
Det här exemplet identifierar medallions (taxi-nummer) och hack_license siffror (drivrutiner) som har slutfört mer än 100 kommunikation inom en angiven tidsperiod.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Utdata:** Frågan bör returnera en tabell med 13,369 rader att ange 13,369 bil/drivrutinen ID: N som har slutfört de 100 processorn i 2013. Den sista kolumnen innehåller antal kommunikation har slutförts.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Data utvärdering: Kontrollera poster med felaktig longitud och/eller latitud
Det här exemplet undersöker om något av fälten för longitud och/eller latitud antingen innehåller ett ogiltigt värde (radian grader bör vara mellan-90 och 90), eller så har (0, 0) koordinater.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Utdata:** Frågan returnerar 837,467 kommunikation som har ogiltiga fält för longitud och/eller latitud.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Utforskning: Lutad kontra inte lutad och RETUR-distribution
Det här exemplet hittar antalet turer som har lutad jämfört med antalet som inte har lutad i en angiven tidsperiod (eller i hela datauppsättningen om som täcker hela året som den har ställts in här). Den här distributionen återspeglar distribution av binära etiketter ska användas senare för binär klassificering modellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Utdata:** Frågan bör returnera följande tips frekvenser för året 2013: 90,447,622 lutad och 82,264,709 spets inte.

### <a name="exploration-tip-classrange-distribution"></a>Utforskning: Tips klass/intervall distribution
Det här exemplet beräknar fördelningen av tip-intervall i en viss tidsperiod (eller i hela datauppsättningen om som täcker hela året). Det här är distributionen av klasserna etiketten som ska användas senare för multiklass-baserad klassificering modellering.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Utforskning: Beräkna och jämföra resans avstånd
Det här exemplet konverterar hämtning och dropoff longitud och latitud till SQL geografiskt område återställningspunkter, beräknar avståndet resa med hjälp av SQL geografi punkter skillnaden och returnerar ett slumpmässigt urval av resultaten för jämförelse. I exempel begränsar resultaten till giltiga koordinater bara med data quality utvärdering fråga beskrivs tidigare.

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

### <a name="feature-engineering-using-sql-functions"></a>Funktionsframställning med hjälp av SQL-funktioner
SQL-funktioner kan ibland vara en effektiv alternativ för funktioner. Vi har definierat en SQL-funktion för att beräkna direkt avståndet mellan platser för hämtning och dropoff i den här genomgången. Du kan köra följande SQL-skript i **Dataverktyg för Visual Studio**.

Här är SQL-skriptet som definierar funktionen avstånd.

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

Här är ett exempel för att anropa den här funktionen för att generera funktioner i SQL-frågan:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Utdata:** Den här frågan genererar ett register (med 2,803,538 rader) med hämtning och dropoff Latitude och longitudes och motsvarande direkt avstånd i miles. Här följer resultaten i först 3 rader:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Förbereda data för att skapa modellen
Följande fråga kopplingar i **nyctaxi\_resans** och **nyctaxi\_avgiften** tabeller, genererar en binär klassificeringsetikett **lutad**, ett flera Klassificeringsetiketten **tips\_klass**, och extraherar ett exempel från fullständig domänansluten datauppsättningen. Är samplingen görs genom att hämta en delmängd av kommunikation baserat på upphämtning tid.  Den här frågan kan kopieras och klistras in direkt i den [Azure Machine Learning Studio](https://studio.azureml.net) [importdata] [ import-data] -modulen för direkta datainmatning från SQL database-instans i Azure. Frågan utesluter poster med fel (0, 0) koordinater.

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

När du är redo att gå vidare till Azure Machine Learning kan du antingen:

1. Spara den slutliga SQL-frågan för att extrahera och hämtar exempel från data och kopiera och klistra in frågan direkt i en [importdata] [ import-data] modul i Azure Machine Learning, eller
2. Spara provade och bakåtkompilerade data som du planerar att använda för att skapa med en ny tabell i SQL DW-modellen och Använd den nya tabellen i den [importdata] [ import-data] modul i Azure Machine Learning. PowerShell-skriptet i tidigare steg har gjort det åt dig. Du kan läsa direkt från den här tabellen i modulen importera Data.

## <a name="ipnb"></a>Datagranskning och de funktioner i IPython notebook
I det här avsnittet ska vi köra datagranskning och funktionen generation med hjälp av både Python och SQL-frågor mot SQL DW skapade tidigare. Ett exempel IPython notebook med namnet **SQLDW_Explorations.ipynb** och en Python-skriptfil **SQLDW_Explorations_Scripts.py** har hämtats till din lokala katalog. De är också tillgängliga på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Dessa två filer är identiska i Python-skript. Python-skriptfil får du om du inte har en IPython Notebook-server. Dessa två exempel på Python-filerna är avsedda under **Python 2.7**.

Den Azure SQL DW information som krävs i det här exemplet IPython Notebook och Python-skriptfilen laddas ned till den lokala datorn har anslutits av PowerShell-skriptet tidigare. De är körbara utan några ändringar.

Om du redan har konfigurerat en AzureML-arbetsyta kan du direkt ladda upp exempel IPython Notebook till AzureML IPython Notebook-tjänsten och börja köra den. Här följer stegen för att överföra till AzureML IPython Notebook-tjänsten:

1. Logga in på din AzureML-arbetsyta, klickar du på ”Studio” högst upp och klicka på ”bärbara datorer” till vänster på sidan.

    ![Klicka på Studio och sedan ANTECKNINGSBÖCKER][22]
2. Klicka på ”nytt” i det nedre vänstra hörnet i webbsidan och välja ”Python 2”. Sedan anger du ett namn för anteckningsboken och klicka på kryssmarkeringen för att skapa en ny tom IPython Notebook.

    ![Klicka på nytt och välj sedan Python 2][23]
3. Klicka på symbolen ”Jupyter” i det övre vänstra hörnet av nya IPython Notebook.

    ![Klicka på symbolen för Jupyter][24]
4. Dra och släpp exemplet IPython Notebook till den **trädet** i din AzureML IPython Notebook-tjänst och klickar på **överför**. Sedan exemplet IPython Notebook ska överföras till tjänsten AzureML IPython Notebook.

    ![Klicka på ladda upp][25]

För att köra exemplet IPython Notebook- eller Python-skriptfil, följande paket krävs för Python. Om du använder tjänsten AzureML IPython Notebook har paketen förinstallerats.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Rekommenderade sekvensen när du bygger avancerade Analyslösningar i AzureML med stora mängder data är följande:

* Läs i ett litet antal data till en ram för data i minnet.
* Utför vissa visualiseringar och explorations med exempeldata.
* Experimentera med funktionsframställning med exempeldata.
* För större datagranskning, datamanipulering av och funktioner du Använd Python för att skicka SQL-frågor direkt mot SQL DW.
* Bestäm urvalsstorlek vara lämplig för att skapa Azure Machine Learning-modell.

I följande avsnitt finns några datagranskning, visualisering av data och funktionen tekniska exempel. Mer data explorations finns i exemplet IPython Notebook och Python-skriptet exempelfilen.

### <a name="initialize-database-credentials"></a>Initiera Databasautentiseringsuppgifter
Initiera dina anslutningsinställningar i följande variabler:

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
Nu är det dags att utforska exempeldata. Vi börjar med att titta på några beskrivande statistik för den **resans\_avståndet** (eller andra fält som du vill ange).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisering: Exempel på diagram
Därefter tittar vi på Låddiagram för resans avståndet till visualisera quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Box ritytans utdata][1]

### <a name="visualization-distribution-plot-example"></a>Visualisering: Exempel för distribution-diagram
Områden som visualisera distributionen och ett histogram för provade resans avstånd.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Distribution ritytans utdata][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisering: Stapel- och linjediagram områden
I det här exemplet vi bin resans avståndet till fem lagerplatser och visualisera datagrupperingen resultat.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan rita ovan bin-distribution i ett fält eller rad diagram med:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Stapel ritytans utdata][3]

och

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Rad diagram utdata][4]

### <a name="visualization-scatterplot-examples"></a>Visualisering: Spridningsdiagrammet exempel
Vi visar spridningsdiagrammet mellan **resans\_tid\_i\_sekunder** och **resans\_avståndet** att se om det finns några korrelation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Spridningsdiagrammet utdata av relation mellan tid och avståndet][6]

På samma sätt kan vi Kontrollera relationen mellan **rate\_kod** och **resans\_avståndet**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Spridningsdiagrammet utdata från relationen mellan kod och avståndet][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Datagranskning på samplade data med SQL-frågor i IPython notebook
I det här avsnittet ska utforska vi data-distributioner som använder exempeldata som bevaras i den nya tabellen som vi skapade ovan. Observera att liknande explorations kan utföras med hjälp av de ursprungliga tabellerna.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Utforskning: Rapporten antalet rader och kolumner i tabellen provade
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Utforskning: Lutad/inte utlöst Distribution
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Utforskning: Tips klass distribution
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Utforskning: Rita tips fördelningen av-klassen
    tip_class_dist['tip_freq'].plot(kind='bar')

![Rita #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Utforskning: Dagliga distribution av kommunikation
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Utforskning: Resans distribution per medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Utforskning: Resans distribution enligt medallion och hack licens
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Utforskning: Resans gång distribution
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Utforskning: Resans avståndet distribution
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Utforskning: Betalning typ av distribution
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Kontrollera den slutliga utformning av tabellen trädmodell
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
Vi är nu redo att gå vidare till modellskapandet och distribution av modeller i [Azure Machine Learning](https://studio.azureml.net). Data är redo att användas i någon av de förutsägelse problem som konstaterats tidigare, nämligen:

1. **Binär klassificering**: För att förutsäga om ett tips har betalat för en resa.
2. **Multiklass-baserad klassificering**: Att förutsäga vilka tips betalt enligt de tidigare definierade klasserna.
3. **Regression uppgift**: Att förutsäga mängden tips som har betalat för en resa.

Börja modellering, logga in på din **Azure Machine Learning** arbetsyta. Om du inte har skapat en machine learning-arbetsytan finns i [skapar en Azure Machine Learning studio-arbetsyta](../studio/create-workspace.md).

1. Kom igång med Azure Machine Learning, se [vad är Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. Sidan Studio tillhandahåller en mängd information, videor, självstudier, länkar till moduler-referenser och andra resurser. Mer information om Azure Machine Learning finns det [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

En typisk träningsexperiment består av följande steg:

1. Skapa en **+ ny** experimentera.
2. Hämta data till Azure Machine Learning studio.
3. Förbearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela upp data i utbildning / / valideringstestning datauppsättningar (eller har separata datauppsättningar för var och en).
6. Välj en eller flera maskininlärningsalgoritmer beroende på träningsproblemet att lösa. T.ex. binär klassificering, multiklass-baserad klassificering, regression.
7. Skapa en eller flera modeller med hjälp av datauppsättning för träning.
8. Bedöma verifiering datauppsättningen med hjälp av den tränade modeller.
9. Utvärdera modeller för att beräkna relevanta mått för träningsproblemet.
10. Bra finjustera modell(er) och välja modellen med bäst att distribuera.

I den här övningen har vi redan utforskas och utformat data i SQL Data Warehouse och valt urvalsstorlek att mata in i Azure Machine Learning studio. Här följer stegen för att skapa en eller flera av förutsägelsemodeller:

1. Hämta data till Azure Machine Learning studio med hjälp av den [importdata] [ import-data] modulen, som är tillgängliga i den **Data indata och utdata** avsnittet. Mer information finns i den [importdata] [ import-data] referenssida för modulen.

    ![Azure ML-importera Data][17]
2. Välj **Azure SQL Database** som den **datakälla** i den **egenskaper** panelen.
3. Ange namnet på databasen DNS i den **Databasservernamnet** fält. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange den **databasnamn** i motsvarande fält.
5. Ange den *SQL-användarnamnet* i den **Server användarkontonamn**, och *lösenord* i den **Server lösenord**.
7. I den **databasfråga** redigera textområde, klistrar in frågan som extraherar de nödvändiga databasfält (inklusive eventuella beräknade fält, till exempel etiketter) och ned samplar data till önskad urvalsstorlek.

Ett exempel på en binär klassificering experiment som läser data direkt från SQL Data Warehouse-databasen är i bilden nedan (Kom ihåg att ersätta tabell namnen nyctaxi_trip och nyctaxi_fare med schemanamnet och tabellnamn som du använde i din genomgången). Liknande experiment kan konstrueras för multiklass-baserad klassificering och regressionsproblem.

![Azure ML Train][10]

> [!IMPORTANT]
> För modellering av finansdata extrahering och samlar fråga exempel som i föregående avsnitt **alla etiketter för tre modellering övningar som ingår i frågan**. Ett viktigt steg i var och en av modellering övningarna för (obligatoriskt) är att **undanta** onödiga etiketter för de andra två problemen och andra **rikta läckage av**. Till exempel när du använder binär klassificering, använda etiketten **lutad** och utelämna fälten **tips\_klass**, **tips\_belopp**, och **totala\_belopp**. Dessa är målet läckage eftersom de innebär tipset betald.
>
> Om du vill exkludera alla onödiga kolumner eller rikta läckage av, kan du använda den [Välj kolumner i datauppsättning] [ select-columns] modul eller [redigera Metadata][edit-metadata]. Mer information finns i [Välj kolumner i datauppsättning] [ select-columns] och [redigera Metadata] [ edit-metadata] referera till sidorna.
>
>

## <a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När din modell är klar kan distribuera du enkelt den som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure ML-webbtjänsterna finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst, måste du:

1. Skapa en arbetsflödesbaserad experiment.
2. Distribuera webbtjänsten.

Skapa en arbetsflödesbaserad experiment från en **slutfört** utbildning experiment, klickar du på **skapa bedömning EXPERIMENTERA** i lägre Åtgärdsfältet.

![Azure-bedömning][18]

Azure Machine Learning försöker skapa en arbetsflödesbaserad experiment som bygger på komponenterna för träningsexperimentet. I synnerhet att:

1. Spara den tränade modellen och ta bort modellen utbildningsmoduler.
2. Identifiera en logisk **indataporten** som motsvarar det förväntade indataschema.
3. Identifiera en logisk **utgående port** som motsvarar det förväntade web service utdata-schemat.

När bedömnings experimentet har skapats kan du granska den och göra justera efter behov. En typisk justering är att ersätta datauppsättningen för indata och/eller fråga med en vilket utesluter Etikettfälten som dessa inte är tillgänglig när tjänsten anropas. Det är också en bra idé att minska storleken på datauppsättningen för indata och/eller fråga till några poster, bara tillräckligt för att indikera inmatningsschemat. För den utgående porten, är det vanligt att exkludera alla inmatningsfält och bara innehålla den **poängsatta etiketter** och **poängsätts sannolikhet** i utdata med den [Välj kolumner i datauppsättning] [ select-columns] modulen.

Ett exempel bedömning experiment finns i bilden nedan. När du är klar att distribuera klickar du på den **publicera WEBBTJÄNSTEN** knappen i lägre Åtgärdsfältet.

![Publicera Azure ML][11]

## <a name="summary"></a>Sammanfattning
Om du vill tar och sammanfattar vad vi har gjort i den här genomgången självstudien, du har skapat en Azure data science-miljö har samarbetat med en stor offentlig datauppsättning, tar det via Team Data Science Process, allt från datainsamling till modellen och sedan till den distribution av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformation
Det här exemplet genomgång och dess tillhörande skript och IPython notebook(s) som delas av Microsoft under MIT-licensen. Kontrollera filen LICENSE.txt i katalogen på exempelkoden på GitHub för mer information.

## <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi kommunikation hämtningssidan](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC Taxitransport Resedata av Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC Taxi och Limousine kommissionen forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
