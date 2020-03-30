---
title: Skapa och distribuera en modell med Azure Synapse Analytics - Team Data Science Process
description: Skapa och distribuera en maskininlärningsmodell med Azure Synapse Analytics med en offentligt tillgänglig datauppsättning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 96d0a5b2fb59e4612107d8ccbf7285fff7576585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128394"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Team datavetenskapsprocessen i aktion: använda Azure Synapse Analytics
I den här självstudien går vi igenom att skapa och distribuera en maskininlärningsmodell med Azure Synapse Analytics för en offentligt tillgänglig datauppsättning – NYC Taxi Trips-datauppsättningen. [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) Den konstruerade binära klassificeringsmodellen förutsäger om ett tips betalas för en resa eller inte.  Modeller inkluderar multiklassklassificering (oavsett om det finns ett tips) och regression (fördelningen för de utbetalade tipsbeloppen).

Proceduren följer arbetsflödet [för teamdatavetenskapsprocessen (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Vi visar hur du konfigurerar en datavetenskapsmiljö, hur du läser in data i Azure Synapse Analytics och hur du använder antingen Azure Synapse Analytics eller en IPython Notebook för att utforska data- och teknikerfunktionerna som ska modelleras. Vi visar sedan hur du skapar och distribuerar en modell med Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Den NYC Taxi Trips dataset
Den NYC Taxi Trip data består av cirka 20 GB komprimerade CSV filer (~ 48 GB okomprimerad), inspelning mer än 173 miljoner enskilda resor och de priser som betalas för varje resa. Varje resepost innehåller hämtnings- och avlämningsplatser och tider, anonymiserat hackkortsnummer och medaljongnumret (taxins unika ID)-nummer. Uppgifterna omfattar alla resor år 2013 och finns i följande två datamängder för varje månad:

1. Filen **trip_data.csv** innehåller reseinformation, till exempel antal passagerare, pickup- och avlämningsplatser, resans varaktighet och resans längd. Här är några exempelposter:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Filen **trip_fare.csv** innehåller information om det pris som betalats för varje resa, till exempel betalningstyp, biljettprisbelopp, tilläggsavgift och skatter, tips och vägtullar samt det totala belopp som betalats. Här är några exempelposter:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den **unika nyckeln** som\_används\_för att gå med i resedata och resebiljett består av följande tre fält:

* Medaljong
* hacka\_licens och
* upphämtningsdatumtid.\_

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Ta itu med tre typer av förutsägelseuppgifter
Vi formulerar tre förutsägelseproblem baserat på *tipsbeloppet\_* för att illustrera tre typer av modelleringsuppgifter:

1. **Binär klassificering:** Att förutsäga om ett tips betalades för en resa, det vill ha ett *\_tips belopp* som är större än $0 är ett positivt exempel, medan ett *tips\_belopp* på $0 är ett negativt exempel.
2. **Multiclass klassificering**: Att förutsäga intervallet av spets betalas för resan. Vi delar *\_upp tipsbeloppet* i fem fack eller klasser:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regressionsuppgift**: För att förutsäga hur mycket tips som betalats för en resa.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurera Azure-datavetenskapsmiljön för avancerad analys
Så här konfigurerar du din Azure Data Science-miljö.

**Skapa ditt eget Azure blob storage-konto**

* När du etablerar din egen Azure blob-lagring väljer du en geografisk plats för din Azure-bloblagring i eller så nära som möjligt till **Södra centrala USA**, vilket är där NYC Taxi-data lagras. Data kopieras med AzCopy från den offentliga blob-lagringsbehållaren till en behållare i ditt eget lagringskonto. Ju närmare azure-bloblagringen är till Södra Centrala USA, desto snabbare slutförs den här uppgiften (steg 4).
* Om du vill skapa ett eget Azure Storage-konto följer du stegen som beskrivs på [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Var noga med att göra anteckningar om värdena för att följa autentiseringsuppgifter för lagringskonto, eftersom de kommer att behövas senare i den här genomgången.

  * **Lagringskontonamn**
  * **Nyckeln för lagringskonto**
  * **Behållarnamn** (som du vill att data ska lagras i Azure-bloblagringen)

**Etablera din Azure Synapse Analytics-instans.**
Följ dokumentationen på [Skapa och fråga ett Azure SQL Data Warehouse i Azure-portalen](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) för att etablera en Azure Synapse Analytics-instans. Se till att du gör notationer på följande Azure Synapse Analytics-autentiseringsuppgifter som ska användas i senare steg.

* **Servernamn** \<: servernamn>.database.windows.net
* **SQLDW(databas) namn**
* **Användarnamn**
* **Lösenord**

**Installera Visual Studio- och SQL Server-dataverktyg.** Instruktioner finns i [Komma igång med Visual Studio 2019 för SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Anslut till din Azure Synapse Analytics med Visual Studio.** Instruktioner finns i steg 1 & 2 i [Anslut till Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Kör följande SQL-fråga i databasen som du skapade i Din Azure Synapse Analytics (i stället för frågan i steg 3 i anslutningsavsnittet)) för att **skapa en huvudnyckel**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Skapa en Azure Machine Learning-arbetsyta under din Azure-prenumeration.** Instruktioner finns i [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Läsa in data i Azure Synapse Analytics
Öppna en Kommandokonsol för Windows PowerShell. Kör följande PowerShell-kommandon för att hämta exemplet SQL-skriptfiler som vi delar med dig på GitHub till en lokal katalog som du anger med parametern *-DestDir*. Du kan ändra värdet för parametern *-DestDir* till valfri lokal katalog. Om *-DestDir* inte finns skapas det av PowerShell-skriptet.

> [!NOTE]
> Du kan behöva **köra som administratör** när du kör följande PowerShell-skript om din *DestDir-katalog* behöver administratörsbehörighet för att skapa eller skriva till det.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

När du har lyckats med körningen ändras den aktuella arbetskatalogen till *-DestDir*. Du bör kunna se skärmen som nedan:

![Aktuella ändringar i arbetskatalogen][19]

Kör följande PowerShell-skript i *administratörsläge i -DestDir:*

    ./SQLDW_Data_Import.ps1

När PowerShell-skriptet körs för första gången blir du ombedd att mata in informationen från din Azure Synapse Analytics och ditt Azure blob storage-konto. När det här PowerShell-skriptet har körts för första gången har autentiseringsuppgifterna du matar in till en konfigurationsfil SQLDW.conf i den aktuella arbetskatalogen. Den framtida körningen av den här PowerShell-skriptfilen har möjlighet att läsa alla nödvändiga parametrar från den här konfigurationsfilen. Om du behöver ändra vissa parametrar kan du välja att mata in parametrarna på skärmen vid prompten genom att ta bort den här konfigurationsfilen och mata in parametervärdena enligt uppmaning eller ändra parametervärdena genom att redigera filen SQLDW.conf i katalogen *-DestDir.*

> [!NOTE]
> För att undvika schemanamnkonflikter med dem som redan finns i din Azure Azure Synapse Analytics, när du läser parametrar direkt från filen SQLDW.conf, läggs ett tresiffrigt slumptal till i schemanamnet från FILEN SQLDW.conf som standardschema schema namn för varje körning. PowerShell-skriptet kan fråga dig om ett schemanamn: namnet kan anges efter användarens gottfinnande.
>
>

Den här **PowerShell-skriptfilen** slutför följande uppgifter:

* **Hämtar och installerar AzCopy**, om AzCopy inte redan är installerat

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
* **Kopierar data till ditt privata blob storage-konto** från den offentliga blob med AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Läser in data med Polybase (genom att köra LoadDataToSQLDW.sql) till din Azure Synapse Analytics** från ditt privata blob storage-konto med följande kommandon.

  * Skapa ett schema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Skapa en databasomfattad autentiseringsuppgifter

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Skapa en extern datakälla för en Azure Storage-blob

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
  * Skapa ett externt filformat för en csv-fil. Data är okomprimerade och fält separeras med pipe-tecknet.

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
  * Skapa externa pris- och resetabeller för NYC-taxidatauppsättning i Azure blob storage.

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

    - Läsa in data från externa tabeller i Azure blob storage till Azure Synapse Analytics

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

    - Skapa en exempeldatatabell (NYCTaxi_Sample) och infoga data till den från att välja SQL-frågor på rese- och pristabellerna. (Vissa steg i den här genomgången måste använda den här exempeltabellen.)

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

Den geografiska platsen för dina lagringskonton påverkar belastningstiderna.

> [!NOTE]
> Beroende på den geografiska platsen för ditt privata blob storage-konto kan processen att kopiera data från en offentlig blob till ditt privata lagringskonto ta cirka 15 minuter, eller ännu längre, och processen med att läsa in data från ditt lagringskonto till ditt Azure Azure Synapse Analytics kan ta 20 minuter eller längre.
>
>

Du måste bestämma vad som ska om du har dubbla käll- och målfiler.

> [!NOTE]
> Om CSV-filerna som ska kopieras från den offentliga blob-lagringen till ditt privata blob-lagringskonto redan finns i ditt privata blob-lagringskonto, frågar AzCopy dig om du vill skriva över dem. Om du inte vill skriva över dem matar du in **n** när du uppmanas att göra det. Om du vill skriva över dem **alla,** mata in **en** när du uppmanas. Du kan också mata in **y** för att skriva över CSV-filer individuellt.
>
>

![Utdata från AzCopy][21]

Du kan använda dina egna data. Om dina data finns i din lokala dator i ditt verkliga program kan du fortfarande använda AzCopy för att ladda upp lokala data till din privata Azure-bloblagring. Du behöver bara ändra `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` **källplatsen,** i kommandot AzCopy i PowerShell-skriptfilen till den lokala katalogen som innehåller dina data.

> [!TIP]
> Om dina data redan finns i din privata Azure-bloblagring i ditt verkliga program kan du hoppa över AzCopy-steget i PowerShell-skriptet och direkt överföra data till Azure Azure Synapse Analytics. Detta kräver ytterligare ändringar av skriptet för att anpassa det till formatet på dina data.
>
>

Det här PowerShell-skriptet ansluter också azure Synapse Analytics-informationen till exempelfilerna för datautforskning SQLDW_Explorations.sql, SQLDW_Explorations.ipynb och SQLDW_Explorations_Scripts.py så att dessa tre filer är redo att provas direkt efter att PowerShell-skriptet har slutförts.

Efter en lyckad körning ser du skärmen som nedan:

![Utdata från en lyckad skriptkörning][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Datautforskning och funktionsteknik i Azure Synapse Analytics
I det här avsnittet utför vi datautforskning och funktionsgenerering genom att köra SQL-frågor mot Azure Synapse Analytics direkt med **Visual Studio Data Tools**. Alla SQL-frågor som används i det här avsnittet finns i exempelskriptet som heter *SQLDW_Explorations.sql*. Den här filen har redan hämtats till din lokala katalog av PowerShell-skriptet. Du kan också hämta den från [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Men filen i GitHub har inte Azure Synapse Analytics-informationen inkopplad.

Anslut till din Azure Synapse Analytics med Visual Studio med inloggningsnamnet och lösenordet för Azure Synapse Analytics och öppna **SQL Object Explorer** för att bekräfta att databasen och tabellerna har importerats. Hämta *filen SQLDW_Explorations.sql.*

> [!NOTE]
> Om du vill öppna en PDW-frågeredigerare (Parallel Data Warehouse) använder du kommandot **Ny fråga** medan PDW är markerat i **SQL Object Explorer**. Standard-SQL-frågeredigeraren stöds inte av PDW.
>
>

Här är de typer av datautforskning och funktionsgenereringsuppgifter som utförs i det här avsnittet:

* Utforska datadistributioner av några fält i olika tidsfönster.
* Undersök datakvaliteten för fälten longitud och latitud.
* Generera binära klassificeringsetiketter och klassificeringsetiketter med flera klasser baserat på **tipsmängden\_**.
* Generera funktioner och beräkna/jämföra trippavstånd.
* Gå med i de två tabellerna och extrahera ett slumpmässigt urval som kommer att användas för att bygga modeller.

### <a name="data-import-verification"></a>Verifiering av dataimport
Dessa frågor ger en snabb verifiering av antalet rader och kolumner i tabellerna som befolkats tidigare med Polybase parallella massimport,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Utgång:** Du bör få 173.179.759 rader och 14 kolumner.

### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Trip distribution av medaljong
Den här exempelfrågan identifierar de medaljonger (taxinummer) som slutförde mer än 100 resor inom en angiven tidsperiod. Frågan skulle dra nytta av den partitionerade tabellen åtkomst eftersom det är betingat av partitionen systemet **för\_pickup datetime**. Genom att fråga hela datauppsättningen används också den partitionerade tabellen och/eller indexgenomsökningen.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Utgång:** Frågan ska returnera en tabell med rader som anger 13 369 medaljonger (taxibilar) och antalet resor som slutfördes 2013. Den sista kolumnen innehåller antalet slutförda resor.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Utforskning: Resefördelning med medaljong och hack_license
I det här exemplet identifieras de medaljonger (taxinummer) och hack_license nummer (förare) som slutförde mer än 100 resor inom en angiven tidsperiod.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Utgång:** Frågan bör returnera en tabell med 13 369 rader som anger de 13 369 bil-/förar-ID:erna som har slutfört mer än 100 resor under 2013. Den sista kolumnen innehåller antalet slutförda resor.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Bedömning av datakvalitet: Verifiera poster med felaktig longitud och/eller latitud
I det här exemplet undersöks om något av longitud- och/eller latitudfälten antingen innehåller ett ogiltigt värde (radianexamina bör vara mellan -90 och 90) eller ha (0, 0) koordinater.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Utgång:** Frågan returnerar 837 467 resor som har ogiltiga longitud- och/eller latitudfält.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Prospektering: Tippade vs inte tippade resor distribution
I det här exemplet hittas antalet resor som tippades jämfört med det nummer som inte tippades under en angiven tidsperiod (eller i den fullständiga datauppsättningen om det täcker hela året som det ställs in här). Den här fördelningen återspeglar den binära etikettfördelning som senare ska användas för binär klassificeringsmodellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Utgång:** Frågan bör returnera följande tipsfrekvenser för år 2013: 90 447 622 tippade och 82 264 709 tippas inte.

### <a name="exploration-tip-classrange-distribution"></a>Utforskning: Tips klass / intervall distribution
I det här exemplet beräknas fördelningen av tipsintervall under en viss tidsperiod (eller i hela datauppsättningen om den täcker hela året). Den här fördelningen av etikettklasser kommer att användas senare för klassificeringsmodellering av flera klasser.

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

**Resultat:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Utforskning: Beräkna och jämför resa avstånd
I det här exemplet konverteras hämtning och avlämnings longitud och latitud till SQL-geografipunkter, beräknar trippavståndet med SQL-geografipoängsskillnaden och returnerar ett slumpmässigt urval av resultaten för jämförelse. Exemplet begränsar resultaten till giltiga koordinater endast med hjälp av den datakvalitetsbedömningsfråga som tidigare behandlats.

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

### <a name="feature-engineering-using-sql-functions"></a>Funktionsteknik med SQL-funktioner
Ibland sql-funktioner kan vara ett effektivt alternativ för funktionsteknik. I den här genomgången definierade vi en SQL-funktion för att beräkna det direkta avståndet mellan upphämtnings- och avlämningsplatserna. Du kan köra följande SQL-skript i **Visual Studio Data Tools**.

Här är SQL-skriptet som definierar avståndsfunktionen.

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

Här är ett exempel att anropa den här funktionen för att generera funktioner i din SQL-fråga:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Utgång:** Den här frågan genererar en tabell (med 2 803 538 rader) med start- och avlämningsgrader och longituder och motsvarande direkta avstånd i miles. Här är resultaten för de tre första raderna:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance (direktövervakning) |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Förbereda data för modellbyggnad
Följande fråga ansluter **\_nyctaxi resa** och **nyctaxi\_biljettpris** tabeller, genererar en binär klassificering etikett **tippade**, en multi-klass klassificering etikett spets **\_klass**, och extraherar ett prov från den fullständiga kopplade datauppsättningen. Provtagningen görs genom att hämta en delmängd av resorna baserat på upphämtningstid.  Den här frågan kan kopieras och sedan klistras in direkt i [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) Import [Data][import-datamodul] för direkt datainmatning från SQL Database-instansen i Azure. Frågan utesluter poster med felaktiga (0, 0) koordinater.

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

1. Spara den slutliga SQL-frågan för att extrahera och ta prov på data och kopiera klistra in frågan direkt i en[importdata-import-datamodul] i Azure Machine Learning, eller
2. Bevara de samplade och konstruerade data som du planerar att använda för modellbygge i en ny Azure Synapse[Analytics-tabell] och använd den nya tabellen i [importdatamodulen för importdata]i Azure Machine Learning. PowerShell-skriptet i tidigare steg har gjort den här uppgiften åt dig. Du kan läsa direkt från den här tabellen i modulen Importera data.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Datautforskning och funktionsteknik i IPython-anteckningsbok
I det här avsnittet kommer vi att utföra datautforskning och funktionsgenerering med både Python och SQL-frågor mot Azure Synapse Analytics som skapats tidigare. Ett exempel på IPython-anteckningsbok med namnet **SQLDW_Explorations.ipynb** och en Python-skriptfil **SQLDW_Explorations_Scripts.py** har hämtats till din lokala katalog. De finns också på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Dessa två filer är identiska i Python-skript. Python-skriptfilen tillhandahålls till dig om du inte har en IPython Notebook-server. Dessa två exempel Python filer är utformade under **Python 2.7**.

Den nödvändiga Azure Synapse Analytics-informationen i exempelt IPython Notebook och Python-skriptfilen som hämtats till din lokala dator har tidigare anslutits av PowerShell-skriptet. De är körbara utan några ändringar.

Om du redan har konfigurerat en Azure Machine Learning-arbetsyta kan du direkt ladda upp exempel på IPython Notebook till AzureML IPython Notebook-tjänsten och börja köra den. Här är stegen för att ladda upp till AzureML IPython Notebook-tjänsten:

1. Logga in på arbetsytan Azure Machine Learning, klicka på **Studio högst** upp och klicka på **ANTECKNINGSBÖCKER** till vänster på webbsidan.

    ![Klicka på Studio och sedan anteckningsböcker][22]
2. Klicka på **NY** i det vänstra nedre hörnet av webbsidan och välj **Python 2**. Ange sedan ett namn till anteckningsboken och klicka på bocken för att skapa den nya tomma IPython-anteckningsboken.

    ![Klicka på NY och välj Python 2][23]
3. Klicka på **Jupyter-symbolen** längst upp i det nya IPython-anteckningsboken.

    ![Klicka på Jupyter-symbol][24]
4. Dra och släpp exemplet IPython Notebook till **trädsidan** för din AzureML IPython Notebook-tjänst och klicka på **Ladda upp**. Sedan överförs exempelet IPython Notebook till AzureML IPython Notebook-tjänsten.

    ![Klicka på Ladda upp][25]

För att kunna köra exemplet IPython Notebook eller Python-skriptfilen behövs följande Python-paket. Om du använder azureml IPython Notebook-tjänsten har dessa paket förinstallerats.

- Pandor
- Numpy
- matplotlib
- pyodbc
- Pytables

När du skapar avancerade analyslösningar på Azure Machine Learning med stora data är här den rekommenderade sekvensen:

* Läs i ett litet urval av data i en dataram i minnet.
* Utför vissa visualiseringar och utforskningar med hjälp av de utvalda data.
* Experimentera med funktionsteknik med hjälp av de utvalda data.
* För större datautforskning, datamanipulering och funktionsteknik använder du Python för att utfärda SQL-frågor direkt mot Azure Synapse Analytics.
* Bestäm vilken urvalsstorlek som ska vara lämplig för Azure Machine Learning-modellbyggnad.

Följande är några datautforskning, datavisualisering och funktionsteknikexempel. Fler datautforskningar finns i exemplet IPython Notebook och exempelet Python-skriptfil.

### <a name="initialize-database-credentials"></a>Initiera databasautentiseringsuppgifter
Initiera inställningarna för databasanslutningen i följande variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Skapa databasanslutning
Här är anslutningssträngen som skapar anslutningen till databasen.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapportera antal rader och kolumner i tabell <nyctaxi_trip>
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Rapportera antal rader och kolumner i tabell <nyctaxi_fare>
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

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Läsa in ett litet dataexempel från Azure Synapse Analytics Database
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

Tid att läsa exempeltabellen är 14,096495 sekunder.
Antal rader och kolumner som hämtats = (1000, 21).

### <a name="descriptive-statistics"></a>Beskrivande statistik
Nu är du redo att utforska de utvalda data. Vi börjar med att titta på några beskrivande statistik för **resan\_avstånd** (eller andra fält som du väljer att ange).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisering: Exempel på låddiagram
Därefter tittar vi på lådan tomten för resan avstånd för att visualisera quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Utdata för rutar tomt][1]

### <a name="visualization-distribution-plot-example"></a>Visualisering: Exempel på distributionsdiagram
Tomter som visualiserar fördelningen och ett histogram för de samplade trippavstånden.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Utdata för distributionstomt][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisering: Stapel- och linjediagram
I det här exemplet lagerplatserar vi resans avstånd till fem lagerplatser och visualiserar binningsresultaten.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan rita ovanstående bin distribution i en bar eller linje tomt med:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Utdata för stapeldiagram][3]

och

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Utdata för raddiagram][4]

### <a name="visualization-scatterplot-examples"></a>Visualisering: Exempel på spridning
Vi visar scatter plot mellan **restid\_\_i\_sek** och resa **\_avstånd** för att se om det finns något samband

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Spridningsutdata för relation mellan tid och avstånd][6]

På samma sätt kan vi kontrollera förhållandet mellan **hastighetskod\_** och **resans\_avstånd.**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Spridningsutdata för relation mellan kod och avstånd][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Datautforskning av exempeldata med SQL-frågor i IPython-anteckningsbok
I det här avsnittet utforskar vi datadistributioner med hjälp av de exempeldata som sparas i den nya tabellen som vi skapade ovan. Liknande utforskningar kan utföras med hjälp av de ursprungliga tabellerna.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Utforskning: Rapportera antal rader och kolumner i den samplade tabellen
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Utforskning: Tippad/ej utlöst distribution
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Utforskning: Distribution av tipsklass
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Prospektering: Rita tipsfördelningen efter klass
    tip_class_dist['tip_freq'].plot(kind='bar')

![Tomt #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Prospektering: Daglig distribution av resor
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Utforskning: Resefördelning per medaljong
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploration: Trip distribution av medaljong och hacka licens
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Utforskning: Fördelning av restid
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Utforskning: Fördelning av reseavstånd
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Utforskning: Distribution av betalningstyp
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifiera den slutliga formen av det medarbest bord
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
Vi är nu redo att gå vidare till modellbygge och modelldistribution i [Azure Machine Learning](https://studio.azureml.net). Uppgifterna är redo att användas i något av de förutsägelseproblem som identifierats tidigare, nämligen:

1. **Binär klassificering**: Att förutsäga om ett tips betalades för en resa.
2. **Multiklassklassificering**: För att förutsäga intervallet för betald spets, enligt de tidigare definierade klasserna.
3. **Regressionsuppgift**: För att förutsäga hur mycket tips som betalats för en resa.

För att påbörja modelleringsövningen loggar du in på din **Azure Machine Learning (klassiska)** arbetsyta. Om du ännu inte har skapat en arbetsyta för maskininlärning läser du [Skapa en arbetsyta för Azure Machine Learning Studio (klassiskt).](../studio/create-workspace.md)

1. Information om hur du kommer igång med Azure Machine Learning finns i [Vad är Azure Machine Learning Studio (klassiskt)?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio (klassiskt)](https://studio.azureml.net).
3. Machine Learning Studio (klassisk) Hemsida ger en mängd information, videor, handledning, länkar till moduler referens och andra resurser. Mer information om Azure Machine Learning finns i [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Ett typiskt utbildningsexperiment består av följande steg:

1. Skapa ett **+NYTT** experiment.
2. Hämta data till Azure Machine Learning Studio (klassisk).
3. Förbehandla, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela upp data i utbildnings-/validerings-/testdatauppsättningar(eller har separata datauppsättningar för varje).
6. Välj en eller flera maskininlärningsalgoritmer beroende på vilket inlärningsproblem som ska lösas. Binär klassificering, multiklassklassificering, regression.
7. Träna en eller flera modeller med hjälp av träningsdatauppsättningen.
8. Poängsätta valideringsdatauppsättningen med hjälp av de tränade erna.
9. Utvärdera modellen/erna för att beräkna relevanta mått för inlärningsproblemet.
10. Ställ in modellen/erna och välj den bästa modellen att distribuera.

I den här övningen har vi redan utforskat och konstruerat data i Azure Synapse Analytics och beslutat om exempelstorleken för att inta i Azure Machine Learning Studio (klassisk). Här är proceduren för att bygga en eller flera av förutsägelsemodellerna:

1. Hämta data till Azure Machine Learning Studio [Import Data](klassisk) med hjälp av[importdatamodulen importdata,] som är tillgänglig i avsnittet **Datainmatning och utdata.** Mer information finns på referenssidan för importdata för[importdata.] [Import Data]

    ![Importdata för Azure ML][17]
2. Välj **Azure SQL Database** som **datakälla** på **egenskapspanelen.**
3. Ange databasens DNS-namn i **fältet Databasservernamn.** Format:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange **databasnamnet** i motsvarande fält.
5. Ange *SQL-användarnamnet* i **serveranvändarkontots namn**och *lösenordet* i **lösenordet för serveranvändarkontot**.
7. I textområdet Redigera text för **databasfråga** klistrar du in frågan som extraherar nödvändiga databasfält (inklusive alla beräknade fält som etiketterna) och nedexempel data till önskad exempelstorlek.

Ett exempel på ett binärt klassificeringsexperiment som läser data direkt från Azure Synapse Analytics-databasen finns i figuren nedan (kom ihåg att ersätta tabellnamnen nyctaxi_trip och nyctaxi_fare med schemanamnet och tabellnamnen som du använde i genomgången). Liknande experiment kan konstrueras för multiklassklassificerings- och regressionsproblem.

![Azure ML-tåg][10]

> [!IMPORTANT]
> I exempel på modelleringsdataextrahering och samplingsfråga i föregående avsnitt **inkluderas alla etiketter för de tre modelleringsövningarna i frågan**. Ett viktigt (obligatoriskt) steg i var och en av modelleringsövningarna är att **utesluta** onödiga etiketter för de andra två problemen och alla andra **målläckor**. När du till exempel använder binär klassificering använder du etiketten **tippad** och utesluter **\_fälttipsklassen,** **\_tipsmängden**och **det\_totala beloppet**. De senare är målläckor eftersom de antyder att tipset betalas.
>
> Om du vill utesluta onödiga kolumner eller målläckor kan du använda modulen [Välj kolumner i datauppsättning][select-columns] eller [redigera metadata][edit-metadata]. Mer information finns [i Välj kolumner i referenssidor för datauppsättning][select-columns] och Redigera [metadata.][edit-metadata]
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När din modell är klar kan du enkelt distribuera den som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure ML-webbtjänster finns i [Distribuera en azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst måste du:

1. Skapa ett bedömningsexperiment.
2. Distribuera webbtjänsten.

Om du vill skapa ett bedömningsexperiment från ett **färdigt** träningsexperiment klickar du på **SKAPA POÄNGSÄTTNINGSEXPERIment** i det nedre åtgärdsfältet.

![Azure-poängsättning][18]

Azure Machine Learning försöker skapa ett bedömningsexperiment baserat på komponenterna i utbildningsexperimentet. I synnerhet kommer den att

1. Spara den tränade modellen och ta bort modellutbildningsmodulerna.
2. Identifiera en logisk **indataport** som representerar det förväntade indataschemat.
3. Identifiera en logisk **utdataport** som representerar det förväntade webbtjänstutdataschemat.

När bedömningsexperimentet skapas granskar du resultaten och justerar efter behov. En typisk justering är att ersätta indatauppsättningen eller frågan med ett som utesluter etikettfält, eftersom dessa etikettfält inte mappas till schemat när tjänsten anropas. Det är också en bra idé att minska storleken på indatauppsättningen och/eller frågan till några få poster, tillräckligt för att ange indataschemat. För utdataporten är det vanligt att utesluta alla indatafält och endast inkludera **poängsatta etiketter** och **poängsatta sannolikheter** i utdata med modulen [Välj kolumner i datauppsättning.][select-columns]

Ett provbedömningsexperiment finns i figuren nedan. När du är klar att distribuera klickar du på knappen **PUBLICERA WEBBTJÄNST** i det nedre åtgärdsfältet.

![Azure ML Publicera][11]

## <a name="summary"></a>Sammanfattning
För att sammanfatta vad vi har gjort i den här genomgången har du skapat en Azure-datavetenskapsmiljö, arbetat med en stor offentlig datauppsättning, tagit den via Team Data Science Process, hela vägen från datainsamling till modellutbildning och sedan till distribution av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformation
Det här exemplet genomgång och dess medföljande skript och IPython-anteckningsböcker delas av Microsoft under MIT-licensen. Mer information finns i filen LICENSE.txt i katalogen för exempelkoden på GitHub.

## <a name="references"></a>Referenser
- [Andrés Monroy NYC Taxi Trips Ladda ner sida](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC: s Taxi Trip Data av Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
