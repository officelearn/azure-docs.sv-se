---
title: Skalbar datavetenskap med Azure Data Lake – Team Data Science Process
description: Så här använder du Azure Data Lake för att utföra datautforskning och binära klassificeringsuppgifter på en datauppsättning.
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
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717924"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalbar datavetenskap med Azure Data Lake: En genomgång från slutna till slutna dagar
Den här genomgången visar hur du använder Azure Data Lake för att göra datautforskning och binära klassificeringsuppgifter på ett exempel på NYC-taxiresan och biljettdatauppsättningen för att förutsäga om ett tips betalas av ett pris. Den går igenom stegen i [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, från datainsamling till modellutbildning och sedan till distributionen av en webbtjänst som publicerar modellen.

## <a name="technologies"></a>Tekniker

Dessa tekniker används i den här genomgången.
* Azure Data Lake Analytics
* U-SQL och Visual Studio
* Python
* Azure Machine Learning
* Skript


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) har alla funktioner som krävs för att göra det enkelt för datavetare att lagra data av valfri storlek, form och hastighet och att utföra databehandling, avancerad analys och maskininlärningsmodellering med hög skalbarhet på ett kostnadseffektivt sätt.   Du betalar per jobb, endast när data faktiskt behandlas. Azure Data Lake Analytics innehåller U-SQL, ett språk som blandar den deklarativa karaktären av SQL med den uttrycksfulla kraften i C# för att ge skalbar distribuerad frågefunktion. Det gör att du kan bearbeta ostrukturerade data genom att använda schema på läsning, infoga anpassad logik och användardefinierade funktioner (UDFs), och inkluderar utökningsbarhet för att möjliggöra finkornig kontroll över hur du kör i stor skala. Mer information om designfilosofin bakom U-SQL finns i [blogginlägget i Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

DataSjöanalys är också en viktig del av Cortana Analytics Suite och fungerar med Azure SQL Data Warehouse, Power BI och Data Factory. Den här kombinationen ger dig en komplett molnstordata och avancerad analysplattform.

Den här genomgången börjar med att beskriva hur du installerar de förutsättningar och resurser som behövs för att slutföra datascience-processaktiviteter. Sedan beskrivs databearbetningsstegen med U-SQL och avslutas med att visa hur du använder Python och Hive med Azure Machine Learning Studio (klassisk) för att skapa och distribuera de prediktiva modellerna.

### <a name="u-sql-and-visual-studio"></a>U-SQL och Visual Studio
Den här genomgången rekommenderar att du använder Visual Studio för att redigera U-SQL-skript för att bearbeta datauppsättningen. U-SQL-skripten beskrivs här och tillhandahålls i en separat fil. Processen omfattar intag, utforskning och sampling av data. Den visar också hur du kör ett U-SQL-skriptjobb från Azure-portalen. Hive-tabeller skapas för data i ett associerat HDInsight-kluster för att underlätta uppbyggnad och distribution av en binär klassificeringsmodell i Azure Machine Learning Studio.

### <a name="python"></a>Python
Den här genomgången innehåller också ett avsnitt som visar hur du skapar och distribuerar en förutsägande modell med Python med Azure Machine Learning Studio. Det ger en Jupyter anteckningsbok med Python skript för stegen i denna process. Anteckningsboken innehåller kod för några ytterligare funktioner engineering steg och modeller konstruktion såsom multiclass klassificering och regression modellering utöver den binära klassificeringsmodellen beskrivs här. Regressionsuppgiften är att förutsäga mängden av spetsen baserat på andra tipsfunktioner.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (klassisk) används för att skapa och distribuera prediktiva modeller med hjälp av två metoder: först med Python-skript och sedan med Hive-tabeller på ett HDInsight -kluster (Hadoop).

### <a name="scripts"></a>Skript
Endast de viktigaste stegen beskrivs i den här genomgången. Du kan ladda ner hela **U-SQL-skriptet** och **Jupyter Notebook** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Krav
Innan du börjar med följande avsnitt måste du ha följande:

* En Azure-prenumeration. Om du inte redan har en, se [Hämta azure gratis provperiod](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Rekommenderas] Visual Studio 2013 eller senare. Om du inte redan har en av dessa versioner installerad kan du ladda ner en gratis communityversion från [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> I stället för Visual Studio kan du också använda Azure-portalen för att skicka Azure Data Lake-frågor. Instruktioner ges om hur du gör det både med Visual Studio och på portalen i avsnittet **Processdata med U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Förbereda datavetenskapsmiljön för Azure Data Lake
Om du vill förbereda datavetenskapsmiljön för den här genomgången skapar du följande resurser:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob-lagringskonto
* Azure Machine Learning Studio (klassiskt) konto
* Azure Data Lake Tools för Visual Studio (rekommenderas)

Det här avsnittet innehåller instruktioner om hur du skapar var och en av dessa resurser. Om du väljer att använda Hive-tabeller med Azure Machine Learning, i stället för Python, för att skapa en modell, måste du också etablera ett HDInsight -kluster (Hadoop). Detta alternativa förfarande beskrivs i avsnitt alternativ 2.


> [!NOTE]
> **Azure Data Lake Store** kan skapas antingen separat eller när du skapar Azure Data Lake **Analytics** som standardlagring. Instruktioner refereras för att skapa var och en av dessa resurser separat, men lagringskontot för Datasjö behöver inte skapas separat.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Skapa en Azure Data Lake Storage


Skapa en ADLS från [Azure-portalen](https://portal.azure.com). Mer information finns i [Skapa ett HDInsight-kluster med DataSjö store med Azure-portalen](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Var noga med att ställa in Cluster AAD Identity i **DataSource-bladet** för det **valfria konfigurationsbladet** som beskrivs där.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Skapa ett Azure Data Lake Analytics-konto
Skapa ett ADLA-konto från [Azure-portalen](https://portal.azure.com). Mer information finns i [Självstudiekurs: kom igång med Azure Data Lake Analytics med Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob-lagringskonto
Skapa ett Azure Blob-lagringskonto från [Azure-portalen](https://portal.azure.com). Mer information finns i avsnittet Skapa ett lagringskonto i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Konfigurera ett Azure Machine Learning Studio -konto (klassiskt)
Registrera dig/i Azure Machine Learning Studio (klassisk) från [studiosidan för Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/) Klicka på knappen **Kom igång nu** och välj sedan en "Free Workspace" eller "Standard workspace". Nu är du redo att skapa experiment i Azure Machine Learning studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Installera Azure Data Lake Tools [Rekommenderas]
Installera Azure Data Lake Tools för din version av Visual Studio från [Azure Data Lake Tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

När installationen är klar öppnar du Visual Studio. Du bör se fliken Datasjö menyn högst upp. Dina Azure-resurser ska visas på den vänstra panelen när du loggar in på ditt Azure-konto.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Den NYC Taxi Trips dataset
Den datauppsättning som används här är en allmänt tillgänglig datauppsättning - [NYC Taxi Trips datauppsättning](https://www.andresmh.com/nyctaxitrips/). Den NYC Taxi Trip data består av cirka 20 GB komprimerade CSV filer (~ 48 GB okomprimerad), inspelning mer än 173 miljoner enskilda resor och de priser som betalas för varje resa. Varje resepost innehåller hämtnings- och avlämningsplatser och tider, anonymiserat hackkortsnummer och medaljongnumret (taxins unika ID)-nummer. Uppgifterna omfattar alla resor år 2013 och finns i följande två datamängder för varje månad:

Csv:n "trip_data" innehåller reseinformation, till exempel antal passagerare, pickup- och avlämningsplatser, resans varaktighet och resans längd. Här är några exempelposter:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Csv:n "trip_fare" innehåller uppgifter om det pris som betalats för varje resa, såsom betalningstyp, biljettprisbelopp, tilläggsavgift och skatter, tips och vägtullar samt det totala belopp som betalats. Här är några exempelposter:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den unika nyckeln\_för att\_gå med resa data och resa biljettpris\_består\_av följande tre områden: medaljong, hacka licens och pickup datetime. De råa CSV-filerna kan nås från en Azure Storage-blob. U-SQL-skriptet för den här kopplingen finns i avsnittet [Join trip och fare tables.](#join)

## <a name="process-data-with-u-sql"></a>Bearbeta data med U-SQL
De uppgifter om databehandling som illustreras i det här avsnittet omfattar intag, kontroll av kvalitet, utforskande och sampling av data. Hur man går med i resor och biljettpriser tabeller visas också. Det sista avsnittet visar köra ett U-SQL-skript jobb från Azure-portalen. Här är länkar till varje underavsnitt:

* [Datainmatning: läsas i data från offentlig blob](#ingest)
* [Kontroller av datakvalitet](#quality)
* [Datautforskning](#explore)
* [Gå med i rese- och pristabeller](#join)
* [Dataprovtagning](#sample)
* [Köra U-SQL-jobb](#run)

U-SQL-skripten beskrivs här och tillhandahålls i en separat fil. Du kan hämta de fullständiga **U-SQL-skripten** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Om du vill köra U-SQL, Öppna Visual Studio klickar du på **Arkiv --> Nytt --> Project**, väljer **U-SQL Project**, namn och sparar det i en mapp.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Det är möjligt att använda Azure Portal för att köra U-SQL i stället för Visual Studio. Du kan navigera till Azure Data Lake Analytics-resursen på portalen och skicka frågor direkt enligt följande bild:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Datainmatning: Läsa i data från offentlig blob

Platsen för data i Azure-bloben refereras som **\_wasb://container namn\_\_\@blob\_storage-konto name.blob.core.windows.net/blob_name** och kan extraheras med **Extractors.Csv()**. Ersätt ditt eget behållarnamn och lagringskontonamn i\_\_följande\_skript för behållarnamns\_\@blob storage-kontonamn i wasb-adressen. Eftersom filnamnen är i samma format, är det möjligt att använda **resedata\_\_\{\*\}.csv** för att läsa i alla 12 resefiler.

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

Eftersom det finns rubriker på den första raden måste du ta bort rubrikerna och ändra kolumntyper till lämpliga rubriker. Du kan antingen spara bearbetade data i Azure Data Lake Storage med **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ eller till Azure Blob storage-konto med **wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

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

På samma sätt kan du läsa i biljettprisdatauppsättningarna. Högerklicka på Azure Data Lake Storage, du kan välja att titta på dina data i **Azure portal - > Data Explorer** eller **Utforskaren** i Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Kontroller av datakvalitet
När rese- och biljetttabeller har lästs in kan datakvalitetskontroller göras på följande sätt. De resulterande CSV-filerna kan matas ut till Azure Blob storage eller Azure Data Lake Storage.

Hitta antalet medaljonger och unikt antal medaljonger:

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

Hitta de medaljonger som hade mer än 100 resor:

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

Hitta dessa ogiltiga poster i form av pickup_longitude:

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



### <a name="data-exploration"></a><a name="explore"></a>Datautforskning
Gör vissa datautforskning med följande skript för att få en bättre förståelse av data.

Hitta fördelningen av tippade och icke-tippade resor:

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

Hitta fördelningen av tips belopp med cut-off värden: 0, 5, 10 och 20 dollar.

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

Hitta grundläggande statistik över resans avstånd:

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

Hitta percentilerna för resans avstånd:

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


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Gå med i rese- och pristabeller
Resor och biljettpriser tabeller kan få sällskap av medaljong, hack_license och pickup_time.

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


För varje nivå av passagerarantal, beräkna antalet poster, genomsnittligt dricksbelopp, variansen för tipsbelopp, procentandel av tippade resor.

    // contingency table
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


### <a name="data-sampling"></a><a name="sample"></a>Dataprovtagning
Välj först slumpmässigt 0,1 % av data från den kopplade tabellen:

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

Gör sedan stratifierad sampling av binär variabel tip_class:

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


### <a name="run-u-sql-jobs"></a><a name="run"></a>Köra U-SQL-jobb
När du har redigerat U-SQL-skript kan du skicka dem till servern med ditt Azure Data Lake Analytics-konto. Klicka på **Datasjö**, **Skicka jobb,** välj ditt **Analytics-konto,** välj **Parallellism**och klicka på **Skicka-knappen.**

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

När jobbet har följts visas status för jobbet i Visual Studio för övervakning. När jobbet är klart kan du till och med spela upp körningen av jobbet och ta reda på flaskhalsstegen för att förbättra jobbets effektivitet. Du kan också gå till Azure-portalen för att kontrollera status för dina U-SQL-jobb.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nu kan du kontrollera utdatafilerna i azure blob-lagring eller Azure-portal. Använd stratifierade exempeldata för vår modellering i nästa steg.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Skapa och distribuera modeller i Azure Machine Learning
Det finns två alternativ för att hämta data till Azure Machine Learning för att skapa och

* I det första alternativet använder du de exempeldata som har skrivits till en Azure Blob (i **datasamplingssteget** ovan) och använder Python för att skapa och distribuera modeller från Azure Machine Learning.
* I det andra alternativet frågar du data i Azure Data Lake direkt med hjälp av en Hive-fråga. Det här alternativet kräver att du skapar ett nytt HDInsight-kluster eller använder ett befintligt HDInsight-kluster där Hive-tabellerna pekar på NY Taxi-data i Azure Data Lake Storage.  Båda dessa alternativ diskuteras i följande avsnitt.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Alternativ 1: Använd Python för att skapa och distribuera maskininlärningsmodeller
Skapa en Jupyter-anteckningsbok på din lokala dator eller i Azure Machine Learning Studio om du vill skapa och distribuera maskininlärningsmodeller med Python. Den Jupyter Notebook som finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) innehåller den fullständiga koden för att utforska, visualisera data, funktionsteknik, modellering och distribution. I den här artikeln omfattas bara modellering och distribution.

### <a name="import-python-libraries"></a>Importera Python-bibliotek
För att kunna köra exemplet Jupyter Notebook eller Python-skriptfilen behövs följande Python-paket. Om du använder azure machine learning notebook-tjänsten har dessa paket förinstallerats.

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


### <a name="read-in-the-data-from-blob"></a>Läsa i data från blob
* Anslutningssträng

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Läsa in som text

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Lägga till kolumnnamn och separata kolumner

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

### <a name="build-machine-learning-models"></a>Skapa maskininlärningsmodeller
Här skapar du en binär klassificeringsmodell för att förutsäga om en resa tippas eller inte. I jupyter notebook kan du hitta andra två modeller: multiclass klassificering och regression modeller.

* Först måste du skapa dummy variabler som kan användas i scikit-learn modeller

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Skapa dataram för modellering

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Utbildning och testning 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistisk regression i träningsset

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Poängtestdatauppsättning

        Y_test_pred = logit_fit.predict(X_test)
* Beräkna utvärderingsmått

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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Skapa webbtjänst-API och förbruka det i Python
Du vill operationalisera maskininlärningsmodellen när den har byggts. Den binära logistikmodellen används här som exempel. Kontrollera att scikit-learn-versionen på din lokala dator är 0.15.1 (Azure Machine Learning Studio är redan åtminstone den här versionen).

* Hitta dina autentiseringsuppgifter för arbetsytan från klassiska inställningar för Azure Machine Learning Studio (klassiskt). Klicka på **Inställningar** --> **namnauktoriseringstoken i**Azure Machine Learning Studio .**Name** --> 

    ![c3 (på)](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Skapa webbtjänst

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Hämta autentiseringsuppgifter för webbtjänster

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Api för anropa webbtjänsten. Vanligtvis väntar 5-10 sekunder efter föregående steg.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Alternativ 2: Skapa och distribuera modeller direkt i Azure Machine Learning
Azure Machine Learning Studio (klassisk) kan läsa data direkt från Azure Data Lake Storage och sedan användas för att skapa och distribuera modeller. Den här metoden använder en Hive-tabell som pekar på Azure Data Lake Storage. Ett separat Azure HDInsight-kluster måste etableras för Hive-tabellen. 

### <a name="create-an-hdinsight-linux-cluster"></a>Skapa ett HDInsight Linux-kluster
Skapa ett HDInsight Cluster (Linux) från [Azure-portalen](https://portal.azure.com). Mer information finns i **avsnittet Skapa ett HDInsight-kluster med åtkomst till Azure Data Lake Storage** i Skapa ett [HDInsight-kluster med DataSjölagring med Azure-portalen](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Skapa hive-tabell i HDInsight
Nu skapar du Hive-tabeller som ska användas i Azure Machine Learning Studio (klassisk) i HDInsight-klustret med hjälp av data som lagras i Azure Data Lake Storage i föregående steg. Gå till hdinsight-klustret som skapats. Klicka på **Inställningar** --> **Egenskaper** --> **Cluster AAD Identity** --> **ADLS Access**, se till att ditt Azure Data Lake Storage-konto läggs till i listan med läs-, skriv- och körningsrättigheter.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klicka sedan på **Instrumentpanel** **bredvid** knappen Inställningar och ett fönster visas. Klicka på **Vyn i** det övre högra hörnet på sidan och du bör se **Frågeredigeraren**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Klistra in följande Hive-skript för att skapa en tabell. Platsen för datakällan finns i Azure Data Lake Storage-referensen på det här sättet: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

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


När frågan är klar bör du se resultaten så här:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Skapa och distribuera modeller i Azure Machine Learning Studio
Du är nu redo att skapa och distribuera en modell som förutsäger om ett tips betalas med Azure Machine Learning eller inte. Stratifierade exempeldata är redo att användas i detta binära klassificeringsproblem (spets eller inte). De prediktiva modellerna med multiklassklassificering (tip_class) och regression (tip_amount) kan också byggas och distribueras med Azure Machine Learning Studio, men här visas bara hur du hanterar fallet med hjälp av den binära klassificeringsmodellen.

1. Hämta data till Azure Machine Learning Studio (klassisk) med hjälp av modulen **Importera data,** som är tillgänglig i avsnittet **Datainmatning och utdata.** Mer information finns på referenssidan [för importdatamodulen.](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)
2. Välj **Hive-fråga** som **datakälla** på **egenskapspanelen.**
3. Klistra in följande Hive-skript i **Hive-databasfrågeredigeraren**

        select * from nyc_stratified_sample;
4. Ange URI för HDInsight-kluster (den här URI finns i Azure-portalen), Hadoop-autentiseringsuppgifter, plats för utdata och Azure Storage-kontonamn/nyckel/behållarnamn.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Ett exempel på en binär klassificering experiment läsning data från Hive tabellen visas i följande figur:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

När experimentet har skapats klickar du på Konfigurera**webbtjänstsförstã¤nsande webbtjänst** **Set Up Web Service** --> 

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Kör det automatiskt skapade bedömningsexperimentet när det är klart och klicka på **Distribuera webbtjänst**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Instrumentpanelen för webbtjänsten visas inom kort:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Sammanfattning
Genom att slutföra den här genomgången har du skapat en datavetenskapsmiljö för att skapa skalbara helhetslösningar i Azure Data Lake. Den här miljön användes för att analysera en stor offentlig datauppsättning och tog den genom de kanoniska stegen i Data Science Process, från datainsamling till modellutbildning och sedan till distributionen av modellen som en webbtjänst. U-SQL användes för att bearbeta, utforska och prova data. Python och Hive användes med Azure Machine Learning Studio (klassisk) för att skapa och distribuera förutsägande modeller.

## <a name="whats-next"></a>Nästa steg
Utbildningsväg för [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) innehåller länkar till ämnen som beskriver varje steg i den avancerade analysprocessen. Det finns en serie genomgångar som är specificerade på sidan Genomgångar i [teamdatavetenskapsprocessen](walkthroughs.md) som visar hur du använder resurser och tjänster i olika scenarier för prediktiv analys:

* [Team Data Science Process i aktion: använda SQL Data Warehouse](sqldw-walkthrough.md)
* [Team Data Science Process i aktion: använda HDInsight Hadoop kluster](hive-walkthrough.md)
* [Teamdatavetenskapsprocessen: använda SQL Server](sql-walkthrough.md)
* [Översikt över datavetenskapsprocessen med Spark på Azure HDInsight](spark-overview.md)
