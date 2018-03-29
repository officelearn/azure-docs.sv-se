---
title: 'Skalbar datavetenskap med Azure Data Lake: en slutpunkt till slutpunkt genomgången | Microsoft Docs'
description: Hur du använder Azure Data Lake till aktiviteter för data-undersökning och binär klassificering på en datamängd.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 6d0f889e1cc76eced172d66755a0a9275e6b7bdf
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalbar datavetenskap med Azure Data Lake: en genomgång för slutpunkt till slutpunkt
Den här genomgången visar hur du använder Azure Data Lake datagranskning och binär klassificering uppgifter på ett sampel från NYC taxi resan och färdavgiften dataset för att förutsäga huruvida ett tips betalar en avgiften. Den vägleder dig genom stegen för den [Team datavetenskap Process](http://aka.ms/datascienceprocess), slutpunkt-till-slutpunkt, från datainsamling modellera utbildning och sedan till distributionen av en webbtjänst som publicerar modellen.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Den [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) har alla funktioner krävs för att göra det enkelt för data forskare att lagra data i olika storlekar, form och hastighet och att genomföra databearbetning, avancerade analyser och maskininlärning modellering med hög skalbarhet på ett kostnadseffektivt sätt.   Du betalar per projekt-basis, bara när data bearbetas faktiskt. Azure Data Lake Analytics innehåller U-SQL, ett språk som blandar deklarativa kompetensen hos SQL med den expressiva kraften i C# för att tillhandahålla skalbara distribuerade frågan kapaciteten. Det gör det möjligt att bearbeta Ostrukturerade data genom att använda schemat på Läs, infoga egen kod och användardefinierade funktioner (UDF) och innehåller utökningsbarhet för att aktivera detaljerade kontroll över hur du utför i större skala. Läs mer om designfilosofin bakom U-SQL i [Visual Studio blogginlägget](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Datasjöanalys är också en viktig del av Cortana Analytics Suite och fungerar med Azure SQL Data Warehouse, Power BI och Data Factory. Detta ger dig en fullständigt moln stordata och avancerade analysplattform.

Den här genomgången börjar genom att beskriva hur du installerar krav och resurser som krävs för att slutföra datavetenskap processen uppgifter. Därefter beskriver databearbetning steg med hjälp av U-SQL och avslutar genom att visa hur du använder Python och Hive med Azure Machine Learning Studio för att skapa och distribuera förutsägelsemodeller. 

### <a name="u-sql-and-visual-studio"></a>U-SQL och Visual Studio
Den här genomgången rekommenderar att du använder Visual Studio redigera U-SQL-skript för att bearbeta dataset. U-SQL-skript som beskrivs här och anges i en separat fil. Processen omfattar vill föra in, utforska och provtagning data. Den visar även hur du kör ett jobb för U-SQL-skript från Azure-portalen. Hive-tabeller skapas för data i ett associerade HDInsight-kluster för att underlätta att skapa och distribuera en binär klassificering modell i Azure Machine Learning Studio.  

### <a name="python"></a>Python
Den här genomgången innehåller också ett avsnitt som visar hur du skapar och distribuerar en förutsägelsemodell med Azure Machine Learning Studio Python. Det ger en Jupyter-anteckningsbok med Python-skript för stegen i den här processen. Den bärbara datorn innehåller koden för vissa ytterligare funktionen engineering steg och modeller konstruktion som multiklass-baserad klassificering och regressionen modeling förutom binär klassificering modellen som beskrivs här. Regression-uppgift är att förutsäga mängden tips baserat på andra tips funktioner. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio används för att skapa och distribuera förutsägelsemodeller. Detta görs med hjälp av två sätt: först med Python-skript och sedan Hive-tabeller i ett kluster i HDInsight (Hadoop).

### <a name="scripts"></a>Skript
Bara de viktigaste stegen beskrivs i den här genomgången. Du kan hämta fullständigt **U-SQL-skript** och **Jupyter-anteckningsbok** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar dessa avsnitt, måste du ha följande:

* En Azure-prenumeration. Om du inte redan har en, se [hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* (Rekommenderas) Visual Studio 2013 eller senare. Om du inte redan har en av dessa versioner är installerad, kan du hämta en kostnadsfri Community-version från [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Du kan också använda Azure-portalen för att skicka frågor om Azure Data Lake i stället för Visual Studio. Anvisningar finns på hur du gör detta med Visual Studio och på portalen i avsnittet **bearbeta data med U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Förbered datavetenskap miljö för Azure Data Lake
För att förbereda datavetenskap miljö för den här genomgången, skapar du följande resurser:

* Azure Data Lake Store (ADLS) 
* Azure Data Lake Analytics (ADLA)
* Azure Blob storage-konto
* Azure Machine Learning Studio-konto
* Azure Data Lake-verktyg för Visual Studio (rekommenderas)

Det här avsnittet innehåller instruktioner om hur du skapar dessa resurser. Om du väljer att använda Hive-tabeller med Azure Machine Learning, i stället för Python, för att skapa en modell, måste du också att etablera ett kluster i HDInsight (Hadoop). Den här alternativa proceduren i beskrivs i avsnittet Alternativ 2.


> [!NOTE]
> Den **Azure Data Lake Store** skapas antingen separat eller när du skapar den **Azure Data Lake Analytics** som standardlagring. Refererar till instruktioner för att skapa dessa resurser separat, men behöver inte att skapa Data Lake-lagringskontot separat.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Skapa ett Azure Data Lake Store


Skapa ett ADLS från den [Azure-portalen](http://portal.azure.com). Mer information finns i [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Se till att konfigurera klustret AAD-identitet i den **DataSource** bladet för den **valfri konfiguration** bladet beskrivs det. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Skapa ett Azure Data Lake Analytics-konto
Skapa ett ADLA konto från den [Azure-portalen](http://portal.azure.com). Mer information finns i [Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto
Skapa ett Azure Blob storage-konto från den [Azure-portalen](http://portal.azure.com). Mer information i avsnittet Skapa en storage-konto i [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Konfigurera ett konto i Azure Machine Learning Studio
Logga in/till Azure Machine Learning Studio från den [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) sidan. Klicka på den **Kom igång nu** knappen och välj sedan en ”ledigt arbetsyta” eller ”Standard arbetsytan”. Nu är du redo för att skapa experiment i Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Installera Azure Data Lake-verktyg (rekommenderas)
Installera Azure Data Lake-verktyg för din version av Visual Studio från [Azure Data Lake-verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Öppna Visual Studio när installationen slutförs. Du bör se Data Lake fliken menyn längst upp. Azure-resurser ska visas i den vänstra panelen när du loggar in på ditt Azure-konto.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>NYC Taxi resor datauppsättningen
Den datamängd som används här är en offentligt tillgängliga dataset--den [NYC Taxi resor dataset](http://www.andresmh.com/nyctaxitrips/). NYC Taxi resa data består av cirka 20 GB komprimerat CSV-filer (~ 48 GB okomprimerade), registrera mer än 173 miljoner enskilda resor och priser betalat för varje resa. Varje resa post innehåller hämtning och Samlingsbibliotek platser och tider, licensnummer anonymiserade hackare (drivrutin) och antalet medallion (taxi's unikt ID). Data omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

Trip_data CSV innehåller resa information, till exempel antal passagerare, hämtning och dropoff punkter, resa varaktighet och resa längd. Här följer några Exempelposter:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Trip_fare CSV innehåller information om avgiften betalat för varje förflyttning, till exempel betalningssätt, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala betald. Här följer några Exempelposter:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unik nyckel för att ansluta till resa\_data och resa\_avgiften består av följande tre fält: medallion hackare\_licens och hämtning\_datetime. Rådata CSV-filer kan nås från en offentlig Azure storage blob. U-SQL-skriptet för den här kopplingen finns på den [koppla resa och avgiften tabeller](#join) avsnitt.

## <a name="process-data-with-u-sql"></a>Bearbeta data med U-SQL
Databearbetningsuppgifter som visas i det här avsnittet innehåller vill föra in, kontrollera kvalitet, utforska och provtagning data. Hur du kopplar resa och avgiften tabeller visas också. Den sista delen visas kör ett skript U-SQL-jobb från Azure-portalen. Här är länkar till varje avsnitt:

* [Datapåfyllning: läsa data från offentliga blob](#ingest)
* [Data quality kontroller](#quality)
* [Datagranskning](#explore)
* [Koppla resa och avgiften tabeller](#join)
* [Data provtagning](#sample)
* [Kör U-SQL-jobb](#run)

U-SQL-skript som beskrivs här och anges i en separat fil. Du kan hämta fullständigt **U-SQL-skript** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Om du vill köra U-SQL, öppna Visual Studio klickar du på **filen--> Ny--> projekt**, Välj **U-SQL-projekt**, namnge och spara den till en mapp.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Det är möjligt att använda Azure Portal för att köra U-SQL i stället för Visual Studio. Du kan gå till Azure Data Lake Analytics-resursen på portalen och skicka frågor direkt som i följande bild:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Datapåfyllning: Läsa data från offentliga blob
Platsen för data i Azure blob hänvisas till som **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** och extraheras med **Extractors.Csv()**. Ersätt behållarens namn och lagringskontonamn i följande skript för container_name@blob_storage_account_name i wasb-adress. Eftersom filnamnen finns i samma format, är det möjligt att använda **resa\_data_ {\*\}.csv** att läsa i alla 12 resa filer. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Eftersom det finns huvuden i den första raden, måste du ta bort sidhuvuden och ändra kolumnens datatyp till lämplig de. Du kan antingen spara den bearbetade data till Azure Data Lake Storage med hjälp av **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ eller till Azure Blob storage-kontot med  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

På liknande sätt kan du läsa i avgiften datauppsättningar. Högerklicka på Azure Data Lake Store kan du visa data i **Azure-portalen--> Data Explorer** eller **Utforskaren** i Visual Studio. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Data quality kontroller
Efter att resa och avgiften tabeller har lästs i kan data quality kontroller göras på följande sätt. De resulterande CSV-filerna kan sparas till Azure Blob storage eller Azure Data Lake Store. 

Hitta antalet medallions och antalet medallions unika:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

Hitta de medallions som har fler än 100 resor:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

Hitta de ogiltiga posterna vad gäller pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Hitta saknade värden för vissa variabler:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Datagranskning
Göra vissa datagranskning med följande skript för att få en bättre förståelse för data.

Hitta lutad och icke-lutad resor distributionen:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

Hitta fördelningen av tips storlek med avklippt värden: 0, 5, 10 och 20 dollar.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

Hitta grundläggande statistik resa avstånd:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Hitta percentiler resa avstånd:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Koppla resa och avgiften tabeller
Resa och avgiften tabeller kan anslutas med medallion, hack_license och pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Beräkna antalet poster, genomsnittlig tips belopp, varians av tips, procentandelen lutad resor för varje nivå av passagerare antalet.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Data provtagning
Först slumpmässigt Välj 0,1% av data från den kopplade tabellen:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Gör stratified sampling av binär variabeln tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Kör U-SQL-jobb
När du har redigerat U-SQL-skript, kan du skicka dem till servern med Azure Data Lake Analytics-kontot. Klicka på **Data Lake**, **skicka jobbet**, Välj din **Analytics-konto**, Välj **parallellitet**, och klicka på **skicka**  knappen.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

När jobbet uppfyller har visas status för jobbet i Visual Studio för övervakning. När jobbet är klar kan du även replay-körningen jobbprocessen och ta reda flaskhals stegen för att förbättra effektiviteten jobb. Du kan även gå till Azure-portalen för att kontrollera status för U-SQL-jobb.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nu kan du kontrollera utdatafilerna i Azure Blob storage eller Azure-portalen. Använd stratified exempeldata för våra modellering i nästa steg.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Skapa och distribuera modeller i Azure Machine Learning
Det finns två alternativ att hämta data till Azure Machine Learning att bygga och 

* I det första alternativet, använder du samplade data som har skrivits till en Azure-Blob (i det **Data provtagning** steget ovan) och använda Python för att skapa och distribuera modeller från Azure Machine Learning. 
* I det andra alternativet fråga du efter data i Azure Data Lake direkt med en Hive-fråga. Det här alternativet kräver att du skapar ett nytt kluster i HDInsight eller Använd ett befintligt HDInsight-kluster där Hive-tabeller pekar på NY Taxi data i Azure Data Lake Storage.  Båda dessa alternativ beskrivs i följande avsnitt. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Alternativ 1: Använd Python att skapa och distribuera datorn learning-modeller
Skapa en Jupyter-anteckningsbok för att skapa och distribuera machine learning-modeller använder Python, på den lokala datorn eller i Azure Machine Learning Studio. Jupyter-anteckningsbok tillhandahålls på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) innehåller den fullständiga koden om du vill utforska visualisera data, funktionen tekniker, modellering och distribution. I den här artikeln beskrivs bara modellering och distribution. 

### <a name="import-python-libraries"></a>Importera Python-bibliotek
För att kunna köra exemplet Jupyter-anteckningsbok eller Python skriptfil, följande Python paket krävs. Om du använder tjänsten för AzureML-anteckningsboken har paketen förinstallerat.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Lästa data från blob
* Anslutningssträng   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Läs i som text
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Lägg till kolumnnamn och avgränsa kolumner
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Ändra vissa kolumner till numeriska
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Skapa machine learning-modeller
Här kan du skapa en modell för binär klassificering för att förutsäga om en resa lutad eller inte. Du kan hitta andra två modeller i Jupyter-anteckningsbok: multiklass-baserad klassificering och regression modeller.

* Du måste först skapa dummy variabler som kan användas i scikit-Läs modeller
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Skapa data ram för modellering
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Träning och testning 60-40 delning
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistic Regression i träningsmängden
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Poängsätta tester datauppsättning
  
        Y_test_pred = logit_fit.predict(X_test)
* Beräkna utvärdering mått
  
        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
  
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
  
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
  
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)
  
       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Skapa-webbtjänstens API och använda den i Python
Vill du operationalisera maskininlärning modellen när den har skapats. Binär logistic modellen används här som ett exempel. Kontrollera att scikit-Läs mer om versionen i din lokala dator 0.15.1. Du behöver inte bry dig om detta om du använder Azure ML studio tjänst.

* Hitta din arbetsyta autentiseringsuppgifter från Azure ML studio inställningar. I Azure Machine Learning Studio klickar du på **inställningar** --> **namn** --> **auktorisering token**. 
  
    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Skapa webbtjänst
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Hämta autentiseringsuppgifter för web service
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Anropa-webbtjänstens API. Du måste vänta 5-10 sekunder efter föregående steg.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Alternativ 2: Skapa och distribuera modeller direkt i Azure Machine Learning
Azure Machine Learning Studio kan läsa data direkt från Azure Data Lake Store och sedan användas för att skapa och distribuera modeller. Den här metoden använder en Hive-tabell som pekar på Azure Data Lake Store. Detta kräver att etableras en separat Azure HDInsight-kluster, på Hive-tabellen har skapats. Följande avsnitt visar hur du gör detta. 

### <a name="create-an-hdinsight-linux-cluster"></a>Skapa ett HDInsight Linux-kluster
Skapa ett HDInsight-kluster (Linux) från den [Azure-portalen](http://portal.azure.com). Mer information finns i **skapa ett HDInsight-kluster med åtkomst till Azure Data Lake Store** i avsnittet [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Skapa Hive-tabellen i HDInsight
Nu kan du skapa Hive-tabeller som ska användas i Azure Machine Learning Studio i HDInsight-kluster med hjälp av data som lagras i Azure Data Lake Store i föregående steg. Gå till HDInsight-klustret skapas. Klicka på **inställningar** --> **egenskaper** --> **kluster-AAD-identitet** --> **ADLS-åtkomst**, Kontrollera att ditt Azure Data Lake Store-konto har lagts till i listan med läsa, skriva och köra rättigheter. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klicka på **instrumentpanelen** bredvid den **inställningar** knappen och ett fönster som öppnas. Klicka på **Hive-vy** i det övre högra hörnet på sidan och du bör se den **frågeredigeraren**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Klistra in följande Hive-skript för att skapa en tabell. Datakällan finns i Azure Data Lake Store referensen i det här sättet: **adl://data_lake_store_name.azuredatalakestore.net:443/mappnamn/filnamn**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


När frågan är klar visas resultaten så här:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Skapa och distribuera modeller i Azure Machine Learning Studio
Du är nu redo att skapa och distribuera en modell som beräknar huruvida ett tips är betald med Azure Machine Learning. Stratified exempeldata är redo att användas i denna binär klassificering (tips eller inte) problem. Förutsägelsemodeller med multiklass-baserad klassificering (tip_class) och regression (tip_amount) kan också skapats och distribuerats med Azure Machine Learning Studio, men här endast visas hur du hanterar fallet med hjälp av binär klassificering modellen.

1. Hämta data till Azure ML med hjälp av den **importera Data** modulen är tillgängliga i den **Data ingående och utgående** avsnitt. Mer information finns i [importera Data modulen](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) referenssida.
2. Välj **Hive-fråga** som den **datakällan** i den **egenskaper** panelen.
3. Klistra in följande Hive-skriptet i den **Hive databasfrågan** redigeraren
   
        select * from nyc_stratified_sample;
4. Ange URI för HDInsight-klustret (detta finns i Azure-portalen), Hadoop-autentiseringsuppgifter, platsen för utdata och Azure namn/nyckelbehållare för lagringskontonamnet.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

Ett exempel på en binär klassificering experiment läsa data från Hive-tabell visas i följande bild:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

När experimentet har skapats klickar du på **konfigurera Web Service** --> **förutsägande webbtjänst**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Kör den automatiskt skapade bedömningen experiment, när den är klar, klickar du på **distribuera webbtjänsten**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Web service instrumentpanelen visar inom kort:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Sammanfattning
Du har skapat en datavetenskap miljö för att skapa skalbara lösningar för slutpunkt till slutpunkt i Azure Data Lake genom att slutföra den här genomgången. Den här miljön används för att analysera en stor offentliga datauppsättning, tar det kanoniska stegen för hur datavetenskap från datainsamling genom modellen utbildning och sedan till distributionen av modellen som en webbtjänst. U-SQL användes för att bearbeta och utforska den exempeldata. Python och Hive användes med Azure Machine Learning Studio för att skapa och distribuera förutsägelsemodeller.

## <a name="whats-next"></a>Nästa steg
Utbildningsväg för den [Team Data vetenskap processen (TDSP)](http://aka.ms/datascienceprocess) innehåller länkar till avsnitt som beskriver varje steg i processen för avancerade analyser. Det finns ett antal genomgång specificerade på den [Team datavetenskap Process genomgång](walkthroughs.md) sida som demonstrerar hur du använder resurser och tjänster i olika förutsägelseanalyser scenarier:

* [Team vetenskap av data i praktiken: använda SQL Data Warehouse](sqldw-walkthrough.md)
* [Team vetenskap av data i praktiken: med HDInsight Hadoop-kluster](hive-walkthrough.md)
* [Team vetenskap av data: använder SQL Server för](sql-walkthrough.md)
* [Översikt över hur datavetenskap med Spark på Azure HDInsight](spark-overview.md)
