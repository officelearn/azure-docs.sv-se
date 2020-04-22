---
title: Utforska data i ett Hadoop-kluster - Team Data Science Process
description: Använda Team Data Science Process för ett heltäckande scenario, med hjälp av ett HDInsight Hadoop-kluster för att skapa och distribuera en modell.
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
ms.openlocfilehash: bf69786f56f52874bd9358ae44a6b88b466e77f4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677463"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Teamdatascience-processen i aktion: Använd Azure HDInsight Hadoop-kluster
I den här genomgången använder vi [Team Data Science Process (TDSP)](overview.md) i ett end-to-end-scenario. Vi använder ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och funktionstekniska data från den allmänt tillgängliga [NYC Taxi Trips-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och för att lägga ned urvalet av data. För att hantera binära och multiklassklassificerings- och regressionsfördiktiva uppgifter skapar vi modeller av data med Azure Machine Learning. 

En genomgång som visar hur du hanterar en större datauppsättning finns i [Team Data Science Process – Använda Azure HDInsight Hadoop-kluster på en datauppsättning med 1 TB](hive-criteo-walkthrough.md).

Du kan också använda en IPython-anteckningsbok för att utföra de uppgifter som visas i genomgången som använder datauppsättningen 1 TB. Mer information finns i [Criteo-genomgången med en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Trips datauppsättning beskrivning
Den NYC Taxi Trip data är ca 20 GB komprimerade kommaavgränsade värden (CSV) filer (~ 48 GB okomprimerad). Den har mer än 173 miljoner individuella resor, och inkluderar de priser som betalas för varje resa. Varje resepost innehåller upphämtning och avlämningsplats och tid, anonymiserat hack (körkort) och medaljongnummer (taxins unika ID). Uppgifterna omfattar alla resor år 2013 och finns i följande två datamängder för varje månad:

- De trip_data CSV-filerna innehåller reseinformation: antalet passagerare, hämtnings- och avlämningspunkter, resans varaktighet och resans längd. Här är några exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- De trip_fare CSV-filerna innehåller information om det pris som betalats för varje resa: betalningstyp, biljettprisbelopp, tilläggsavgift och skatter, tips och vägtullar samt det totala belopp som betalats. Här är några exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den unika nyckeln\_för att\_gå med resa data och resa\_biljettpris består\_av fälten: medaljong, hacka licens och pickup datetime. För att få alla detaljer som är relevanta för en viss resa, är det tillräckligt att gå med dessa tre nycklar.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelseuppgifter
Bestäm vilken typ av förutsägelser du vill göra baserat på dataanalys för att klargöra de nödvändiga processuppgifterna. Här är tre exempel på förutsägelse problem som vi tar itu med i denna genomgång, alla baserat på *\_tips belopp:*

- **Binär klassificering**: Förutsäg om ett tips har betalats för en resa. Det vill än ett *tips\_belopp* som är större än $ 0 är ett positivt exempel, medan ett tips *\_belopp* på $ 0 är ett negativt exempel.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Multiklassklassificering**: Förutsäg intervallet för tipsbelopp som betalats för resan. Vi delar *\_upp tipsbeloppet* i fem klasser:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressionsuppgift**: Förutsäg hur mycket tips som betalats för en resa.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Konfigurera ett HDInsight Hadoop-kluster för avancerad analys
> [!NOTE]
> Detta är vanligtvis en administratörsuppgift.
> 
> 

Du kan konfigurera en Azure-miljö för avancerad analys som använder ett HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-account-create.md): Det här lagringskontot används för att lagra data i Azure Blob-lagring. De data som används i HDInsight-kluster finns också här.
2. [Anpassa Azure HDInsight Hadoop-kluster för avancerad analysprocess och teknik](customize-hadoop-cluster.md). Det här steget skapar ett HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerat på alla noder. Det finns två viktiga steg att komma ihåg när du anpassar HDInsight-klustret.
   
   * Kom ihåg att länka lagringskontot som skapats i steg 1 med ditt HDInsight-kluster när du skapar det. Det här lagringskontot kommer åt data som bearbetas i klustret.
   * När du har skapat klustret aktiverar du Fjärråtkomst till huvudnoden för klustret. Bläddra till fliken **Konfiguration** och välj **Aktivera fjärr .** Det här steget anger de användaruppgifter som används för fjärrinloggning.
3. [Skapa en Azure Machine Learning-arbetsyta:](../studio/create-workspace.md)Du använder den här arbetsytan för att skapa maskininlärningsmodeller. Den här uppgiften åtgärdas efter att ha slutfört en inledande datautforskning och nedsampling med hjälp av HDInsight-klustret.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Hämta data från en offentlig källa
> [!NOTE]
> Detta är vanligtvis en administratörsuppgift.
> 
> 

Om du vill kopiera [NYC Taxi Trips-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) till datorn från dess offentliga plats använder du någon av de metoder som beskrivs i [Flytta data till och från Azure Blob-lagring](move-azure-blob.md).

Här beskriver vi hur du använder AzCopy för att överföra filer som innehåller data. Om du vill hämta och installera AzCopy följer du instruktionerna på [Komma igång med kommandoradsverktyget AzCopy](../../storage/common/storage-use-azcopy.md).

1. Kör följande AzCopy-kommandon i ett kommandotolksfönster och ersätter * \<path_to_data_folder>* med önskat mål:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. När kopian är klar ser du totalt 24 zippade filer i den valda datamappen. Packa upp de nedladdade filerna till samma katalog på den lokala datorn. Anteckna mappen där de okomprimerade filerna finns. Den här mappen kallas * \<\_sökvägen\_till unzipped_data\_\> filer* i det följande.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Ladda upp data till standardbehållaren för HDInsight Hadoop-klustret
> [!NOTE]
> Detta är vanligtvis en administratörsuppgift.
> 
> 

I följande AzCopy-kommandon ersätter du följande parametrar med de faktiska värden som du angav när du skapade Hadoop-klustret och packar upp datafilerna.

* *** \<path_to_data_folder>*** Katalogen (tillsammans med sökvägen) på datorn som innehåller de uppackade datafilerna.  
* ***lagringskontonamnet för Hadoop-kluster>\<*** Lagringskontot som är kopplat till ditt HDInsight-kluster.
* ***standardbehållare för Hadoop-kluster>\<*** Standardbehållaren som används av klustret. Namnet på standardbehållaren är vanligtvis samma namn som själva klustret. Om klustret till exempel kallas "abc123.azurehdinsight.net" är standardbehållaren abc123.
* ***nyckeln för lagringskonto>\<*** Nyckeln för lagringskontot som används av klustret.

Kör följande två AzCopy-kommandon från en kommandotolk eller ett PowerShell-fönster.

Det här kommandot överför resedata till ***katalogen nyctaxitripraw*** i standardbehållaren för Hadoop-klustret.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Det här kommandot överför biljettprisdata till ***katalogen nyctaxifareraw*** i standardbehållaren för Hadoop-klustret.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data bör nu vara i Blob-lagring och redo att förbrukas i HDInsight-klustret.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Logga in på huvudnoden i Hadoop-klustret och förbered dig för dataanalys
> [!NOTE]
> Detta är vanligtvis en administratörsuppgift.
> 
> 

Om du vill komma åt huvudnoden i klustret för dataanalys och nedsprovtagning av data följer du proceduren som beskrivs i [Access huvudnoden för Hadoop Cluster](customize-hadoop-cluster.md).

I den här genomgången använder vi främst frågor skrivna i [Hive](https://hive.apache.org/), ett SQL-liknande frågespråk, för att utföra preliminära datautforskningar. Hive-frågorna lagras i ".hql"-filer. Vi ned-prover sedan dessa data som ska användas inom Machine Learning för att bygga modeller.

Om du vill förbereda klustret för dataanalys laddar du ned filerna ".hql" som innehåller relevanta Hive-skript från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) till en lokal katalog (C:\temp) på huvudnoden. Öppna kommandotolken inifrån huvudnoden i klustret och kör följande två kommandon:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Dessa två kommandon ladda ner alla '.hql' filer som behövs i denna genomgång till den lokala katalogen ***C: \ temp&#92;*** i huvudet noden.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Skapa Hive-databas och tabeller partitionerade efter månad
> [!NOTE]
> Den här uppgiften är vanligtvis för en administratör.
> 
> 

Du är nu redo att skapa Hive-tabeller för NYC taxi datauppsättning.
Öppna kommandoraden Hadoop på skrivbordet för huvudnoden i Hadoop-klustret. Ange Hive-katalogen genom att köra följande kommando:

    cd %hive_home%\bin

> [!NOTE]
> Kör alla Hive-kommandon i den här genomgången från Hive-bin/katalogprompten. Detta hanterar alla sökvägsproblem automatiskt. Vi använder termerna "Hive directory prompt", "Hive bin/ directory prompt" och "Hadoop command line" omväxlande i den här genomgången.
> 
> 

Kör följande kommando på kommandoraden Hadoop på huvudnoden som skapar Hive-databasen och tabellerna i Hive-katalogen:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Här är innehållet i **\_C:\temp\sample\_\_hive create\_db and\_tables.hql-filen** som skapar Hive-databasen **nyctaxidb**och tabellerna **resa** och **biljettpris**.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Detta Hive-skript skapar två tabeller:

* **Resetabellen** innehåller reseinformation om varje resa (förarinformation, hämtningstid, ressträcka och tider).
* **Pristabellen** innehåller biljettuppgifter (biljettprisbelopp, dricksbelopp, vägtullar och tilläggsavgifter).

Om du behöver ytterligare hjälp med dessa procedurer, eller om du vill undersöka alternativa, läser du avsnittet [Skicka Hive-frågor direkt från Hadoop-kommandoraden](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Läsa in data till Hive-tabeller efter partitioner
> [!NOTE]
> Den här uppgiften är vanligtvis för en administratör.
> 
> 

NYC taxi datauppsättning har en naturlig partitionering per månad, som vi använder för att möjliggöra snabbare bearbetning och frågetider. Följande PowerShell-kommandon (som utfärdas från Hive-katalogen med hjälp av Kommandoraden Hadoop) läser in data till tabellen resa och biljettpris, partitionerade efter månad.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**\_Exempelinläsningsdata\_\_för\_\_inläsningsdata av partitions.hql-filen** innehåller följande **LOAD-kommandon:**

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Ett antal av Hive frågor som används här i prospekteringsprocessen innebär att titta på endast en eller två partitioner. Men dessa frågor kan köras över hela datauppsättningen.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Visa databaser i HDInsight Hadoop-klustret
Om du vill visa de databaser som har skapats i HDInsight Hadoop-klustret i kommandoradsfönstret i Hadoop kör du följande kommando på kommandoraden Hadoop:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Visa Hive-tabellerna i **nyctaxidb-databasen**
Om du vill visa tabellerna i **nyctaxidb-databasen** kör du följande kommando på kommandoraden Hadoop:

    hive -e "show tables in nyctaxidb;"

Vi kan bekräfta att tabellerna är partitionerade genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.trip;"

Här är den förväntade produktionen:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

På samma sätt kan vi se till att pristabellen partitioneras genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.fare;"

Här är den förväntade produktionen:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Datautforskning och funktionsteknik i Hive
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Du kan använda Hive-frågor för att utföra datautforskning och funktionsteknik för data som läses in i Hive-tabellerna. Här är exempel på sådana uppgifter:

* Visa de 10 bästa posterna i båda tabellerna.
* Utforska datadistributioner av några fält i olika tidsfönster.
* Undersök datakvaliteten för fälten longitud och latitud.
* Generera binära och multiklassklassificeringsetiketter baserat på tipsmängden.
* Generera funktioner genom att beräkna direkt resa avstånd.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Utforskning: Visa de 10 bästa posterna i bordsresa
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Om du vill se hur data ser ut undersöker du 10 poster från varje tabell. Om du vill granska posterna kör du följande två frågor separat från Hive-katalogprompten i kommandoradskonsolen Hadoop.

Så här får du de 10 bästa posterna i resetabellen från den första månaden:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Så här får du de 10 bästa posterna i pristabellen från den första månaden:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Du kan spara posterna i en fil för bekväm visning med en liten ändring av föregående fråga:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Utforskning: Visa antalet poster i var och en av de 12 partitionerna
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Av intresse är hur antalet resor varierar under kalenderåret. Gruppering per månad visar fördelningen av resor.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Det här kommandot ger följande utdata:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Här är den första kolumnen månaden, och den andra är antalet resor för den månaden.

Vi kan också räkna det totala antalet poster i vår resa datauppsättning genom att köra följande kommando på Hive katalogprompten:

    hive -e "select count(*) from nyctaxidb.trip;"

Det här kommandot ger:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Med kommandon som liknar de som visas för resedatauppsättningen kan vi utfärda Hive-frågor från Hive-katalogens fråga om biljettprisdatauppsättningen för att validera antalet poster.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Det här kommandot ger den här utdata:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Exakt samma antal resor per månad returneras för båda datauppsättningarna, vilket ger den första valideringen att data har lästs in korrekt.

Du kan räkna det totala antalet poster i biljettprisdatauppsättningen med hjälp av följande kommando från Hive-katalogprompten:

    hive -e "select count(*) from nyctaxidb.fare;"

Det här kommandot ger:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Det totala antalet poster i båda tabellerna är också detsamma, vilket ger en andra validering som data har lästs in korrekt.

### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Trip distribution av medaljong
> [!NOTE]
> Den här analysen är vanligtvis en datavetareuppgift.
> 
> 

Det här exemplet identifierar medaljongerna (taxinummer) med fler än 100 resor inom en viss tidsperiod. Frågan drar nytta av den partitionerade tabellåtkomsten eftersom **month**den är beroende av partitionsvariabelmånaden . Frågeresultaten skrivs till en lokal fil, **queryoutput.tsv**, i `C:\temp` på huvudnoden.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Här är innehållet i **\_provet\_\_hive\_\_resa räkna med medallion.hql** fil för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medaljongen i NYC taxi dataset identifierar en unik hytt. Du kan identifiera vilka hytter som är jämförelsevis upptagna genom att fråga vilka som gjort mer än ett visst antal resor under en viss tidsperiod. I följande exempel identifieras hytter som gjort mer än hundra resor under de första tre månaderna och frågeresultatet sparas i en lokal fil, **C:\temp\queryoutput.tsv**.

Här är innehållet i **\_provet\_\_hive\_\_resa räkna med medallion.hql** fil för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Kör följande kommando i hive-katalogprompten:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploration: Trip distribution av medaljong och hacka licens
> [!NOTE]
> Den här uppgiften är vanligtvis för en datavetare.
> 
> 

När vi utforskar en datauppsättning vill vi ofta undersöka fördelningen av grupper av värden. Det här avsnittet innehåller ett exempel på hur du gör denna analys för hytter och förare.

Exempel **\_hive-antalet\_\_resor\_\_av\_medallion license.hql-filen** grupperar biljettprisdatauppsättningen på **medaljong** och **hack_license**och returnerar antalet av varje kombination. Här är dess innehåll:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Den här frågan returnerar kombinationer av hytt och förare, ordnade efter fallande antal resor.

Kör från hive-katalogprompten:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Frågeresultatet skrivs till en lokal fil, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Utforskning: Bedöma datakvalitet genom att söka efter ogiltiga longitud- eller latitudposter
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Ett gemensamt mål för utforskande dataanalys är att såra ut ogiltiga eller dåliga poster. Exemplet i det här avsnittet avgör om fälten longitud eller latitud innehåller ett värde långt utanför NYC-området. Eftersom det är troligt att sådana poster har ett felaktigt longitud-latitudvärde, vill vi eliminera dem från alla data som ska användas för modellering.

Här är innehållet i **provet\_hive\_kvalitet\_assessment.hql** fil för inspektion.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Kör från hive-katalogprompten:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

*Argumentet -S* som ingår i det här kommandot undertrycker statusskärmsutskriften för hive-kartan/minska jobben. Det här kommandot är användbart eftersom det gör screenutskriften för Hive-frågeutdata mer läsbar.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Utforskning: Binära klassfördelningar av resetips
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

För det binära klassificeringsproblemet som beskrivs i avsnittet [Exempel på förutsägelseaktiviteter](hive-walkthrough.md#mltasks) är det bra att veta om ett tips gavs eller inte. Denna distribution av tips är binär:

* tips ges (klass 1, tips\_belopp > $ 0)  
* inget tips (klass 0, tipsbelopp\_= $0)

Följande **exempel\_hive\_\_tippade frequencies.hql** fil visar kommandot för att köra:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Kör från hive-katalogprompten:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Utforskning: Klassfördelningar i flerklassinställningen
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

För multiklassklassificeringsproblemet som beskrivs i avsnittet [Exempel på förutsägelseuppgifter](hive-walkthrough.md#mltasks) lämpar sig den här datauppsättningen också för en naturlig klassificering för att förutsäga mängden tips som ges. Vi kan använda lagerplatser för att definiera tipsintervall i frågan. Om du vill hämta klassfördelningarna för de olika tipsintervallen använder du **exempelfilen\_för hive-tipsintervallet.\_\_\_** Här är dess innehåll.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Kör följande kommando från kommandoradskonsolen Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Utforskning: Beräkna det direkta avståndet mellan två longitud-latitudplatser
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Du kanske vill veta om det finns en skillnad mellan det direkta avståndet mellan två platser och den faktiska resan avstånd av taxi. En passagerare kan vara mindre benägna att tippa om de räkna ut att föraren avsiktligt har tagit dem med en längre rutt.

För att se jämförelsen mellan faktiska resa avstånd och [Haversine avstånd](https://en.wikipedia.org/wiki/Haversine_formula) mellan två longitud-latitud punkter (den "stora cirkeln" avstånd), kan du använda trigonometriska funktioner som finns inom Hive:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

I föregående fråga är R jordens radie i miles och pi konverteras till radianer. Longitud-latitudpunkter filtreras för att ta bort värden som ligger långt från NYC-området.

I det här fallet skriver vi resultaten till en katalog som kallas **queryoutputdir**. Sekvensen för följande kommandon skapar först den här utdatakatalogen och kör sedan hive-kommandot.

Kör från hive-katalogprompten:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Frågeresultaten skrivs till nio Azure-blobbar (**queryoutputdir/000000\_0** till **queryoutputdir/000008\_0**), under standardbehållaren för Hadoop-klustret.

Om du vill se storleken på de enskilda blobbar kör du följande kommando från Hive-katalogprompten:

    hdfs dfs -ls wasb:///queryoutputdir

Om du vill se innehållet i en viss fil, säg **\_000000 0**, använd Hadoops `copyToLocal` kommando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`kan vara mycket långsam för stora filer, och rekommenderas inte för användning med dem.  
> 
> 

En viktig fördel med att ha dessa data finns i en Azure-blob är att vi kan utforska data i Machine Learning, med hjälp av [modulen Importera data.][import-data]

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Ned-exempel data och bygga modeller i Machine Learning
> [!NOTE]
> Detta är vanligtvis en datavetare uppgift.
> 
> 

Efter den undersökande dataanalysfasen är vi nu redo att ned-prov data för att bygga modeller i Machine Learning. I det här avsnittet visar vi hur du använder en Hive-fråga för att nedextpensera data. Machine Learning kommer sedan åt den från modulen [Importera data.][import-data]

### <a name="down-sampling-the-data"></a>Nedsampling av data
Det finns två steg i den här proceduren. Först går vi med i **nyctaxidb.trip** och **nyctaxidb.fare** tabeller på tre nycklar som finns i alla poster: **medaljong,** **hacka\_licens,** och **pickup\_datetime**. Vi genererar sedan en binär klassificeringsetikett, **tippade**, och en multiclass klassificering etikett, **tips\_klass**.

Om du vill kunna använda data med nedstursa data direkt från modulen [Importera data][import-data] i Machine Learning bör du lagra resultaten av föregående fråga i en intern Hive-tabell. I det följande skapar vi en intern Hive-tabell och fyller dess innehåll med de sammanfogade och nedsmakade data.

Frågan tillämpar standard hive-funktioner direkt för att generera följande tidsparametrar från **fältet\_upphämtningsdatumtid:**
- timme på dagen
- vecka på året
- veckodag ('1' står för måndag, och '7' står för söndag)

Frågan genererar också det direkta avståndet mellan hämtnings- och avlämningsplatserna. En fullständig lista över sådana funktioner finns i [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Frågan ned-prover sedan data så att frågeresultaten kan passa in i Azure Machine Learning Studio. Endast cirka 1 procent av den ursprungliga datauppsättningen importeras till studion.

Här är innehållet i **\_exempel\_\_hive\_\_förbereda för aml full.hql** fil som förbereder data för modell byggnad i Machine Learning:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Så här kör du den här frågan från Hive-katalogprompten:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Vi har nu en intern tabell, **nyctaxidb.nyctaxi_downsampled_dataset**, som kan nås med hjälp av [modulen Importera data][import-data] från Machine Learning. Dessutom kan vi använda den här datauppsättningen för att skapa Machine Learning-modeller.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Använd modulen Importera data i Machine Learning för att komma åt nedkomstdata
Om du vill utfärda Hive-frågor i modulen [Importera data][import-data] i Machine Learning behöver du åtkomst till en machine learning-arbetsyta. Du behöver också åtkomst till autentiseringsuppgifterna för klustret och dess associerade lagringskonto.

Här är några detaljer om [modulen Importera data][import-data] och parametrarna för inmatning:

**HCatalog server URI**: Om klusternamnet är **abc123**, använd sedan: https:\//abc123.azurehdinsight.net.

**Hadoop-användarkontonamn**: Användarnamnet som valts för klustret (inte fjärråtkomstanvändarnamnet).

**Hadoop användarkonto lösenord:** Det lösenord som valts för klustret (inte fjärråtkomst lösenord).

**Plats för utdata:** Valt att vara Azure.

**Azure Storage-kontonamn**: Namn på standardlagringskontot som är associerat med klustret.

**Azure-behållarnamn:** Standardbehållarenamnet för klustret och är vanligtvis samma som klusternamnet. För ett kluster som heter **abc123**är namnet abc123.

> [!IMPORTANT]
> Alla tabeller som vi vill fråga med hjälp av modulen [Importera data][import-data] i Machine Learning måste vara en intern tabell.
> 
> 

Så här tar du reda på om en tabell **T** i en databas **D.db** är en intern tabell. Kör följande kommando i hive-katalogprompten:

    hdfs dfs -ls wasb:///D.db/T

Om tabellen är en intern tabell och den är ifylld måste innehållet visas här.

Ett annat sätt att avgöra om en tabell är en intern tabell är att använda Azure Storage Explorer. Använd den för att navigera till standardbehållarens namn på klustret och filtrera sedan efter tabellnamnet. Om tabellen och dess innehåll visas bekräftar detta att det är en intern tabell.

Här är en skärmdump av Hive-frågan och [modulen Importera data:][import-data]

![Skärmbild av Hive-fråga för modulen Importera data](./media/hive-walkthrough/1eTYf52.png)

Eftersom våra nedstursa data finns i standardbehållaren är den resulterande Hive-frågan från Machine Learning enkel. Det är bara en **välj * från nyctaxidb.nyctaxi\_downsampled\_data**.

Datauppsättningen kan nu användas som utgångspunkt för att skapa Machine Learning-modeller.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Skapa modeller i Maskininlärning
Du kan nu gå vidare till modellbygge och modelldistribution i [Machine Learning](https://studio.azureml.net). Uppgifterna är redo för oss att använda för att ta itu med förutsägelse problem som identifierats tidigare:

- **Binär klassificering**: Att förutsäga om ett tips betalades för en resa.

  **Elev används:** Tvåklassad logistisk regression

  a. För det här problemet **tippas**måletiketten (eller klassen) . Den ursprungliga datauppsättningen med nedstursta prov har några kolumner som är målläckor för det här klassificeringsexperimentet. I synnerhet **\_visar tipsklass,** **\_tipsbelopp**och **totalt\_belopp** information om måletiketten som inte är tillgänglig vid testtillfället. Vi tar bort dessa kolumner från övervägande med hjälp av modulen [Välj kolumner i datauppsättning.][select-columns]

  Följande diagram visar vårt experiment för att förutsäga om ett tips betalades för en viss resa:

  ![Diagram över experiment för att förutsäga om dricks betalades](./media/hive-walkthrough/QGxRz5A.png)

  b. För detta experiment var våra måletikettfördelningar ungefär 1:1.

   Följande diagram visar fördelningen av toppklassetiketter för binärklassificeringsproblemet:

  ![Diagram över fördelning av toppklassetiketter](./media/hive-walkthrough/9mM4jlD.png)

    Som ett resultat får vi ett område under kurvan (AUC) på 0,987, som visas i följande figur:

  ![Diagram över AUC-värde](./media/hive-walkthrough/8JDT0F8.png)

- **Multiklassklassificering:** För att förutsäga intervallet för tipsbelopp som betalats för resan, genom att använda de tidigare definierade klasserna.

  **Elev används:** Multiklass logistisk regression

  a. För detta problem är vår måletikett (eller klass) **spetsklass\_**, som kan ta ett av fem värden (0,1,2,3,4). Som i binärklassificeringsfallet har vi några kolumner som är målläckor för det här experimentet. I synnerhet **tippade,** **\_tips belopp**och totalt **\_belopp** avslöjar information om målet etiketten som inte är tillgänglig vid testtillfället. Vi tar bort dessa kolumner med hjälp av modulen [Välj kolumner i datauppsättning.][select-columns]

  Följande diagram visar experimentet för att förutsäga i vilken lagerplats ett tips sannolikt kommer att falla. Facken är: Klass 0: tips = $ 0, klass 1: tips > $ 0 och tips <= $ 5, klass 2: tips > $ 5 och tips <= $ 10, klass 3: tips > $ 10 och tips <= $ 20, och klass 4: tips > $ 20.

  ![Diagram över experiment för att förutsäga bin för spets](./media/hive-walkthrough/5ztv0n0.png)

  Vi visar nu hur den faktiska testklassfördelningen ser ut. Klass 0 och klass 1 är vanliga, och de andra klasserna är sällsynta.

  ![Diagram över testklassfördelning](./media/hive-walkthrough/Vy1FUKa.png)

  b. För det här experimentet använder vi en förvirringsmatris för att titta på förutsägelsenoggrannheterna som visas här:

  ![Matris för förvirring](./media/hive-walkthrough/cxFmErM.png)

  Även om klassen noggrannhet på de förhärskande klasserna är bra, gör modellen inte ett bra jobb med att "lära" på de ovanligare klasserna.

- **Regressionsuppgift**: För att förutsäga hur mycket tips som betalats för en resa.

  **Elev används:** Förstärkt beslutsträd

  a. För det här problemet är måletiketten (eller klassen) **dricksmängd\_**. Målet läcker i detta fall är: **tippade,** **\_spets klass**, och **totalt\_belopp**. Alla dessa variabler visar information om tipsbeloppet som vanligtvis inte är tillgängligt vid testtillfället. Vi tar bort dessa kolumner med hjälp av modulen [Välj kolumner i datauppsättning.][select-columns]

  Följande diagram visar experimentet för att förutsäga mängden av den angivna spetsen:

  ![Diagram över experiment för att förutsäga mängden spets](./media/hive-walkthrough/11TZWgV.png)

  b. För regressionsproblem mäter vi precisionen i förutsägelsen genom att titta på det fyrkantiga felet i förutsägelserna och bestämningskoefficienten:

  ![Skärmbild av förutsägelsestatistik](./media/hive-walkthrough/Jat9mrz.png)

  Här är bestämningskoefficienten 0,709, vilket innebär att cirka 71 procent av variansen förklaras av modellkoefficienterna.

> [!IMPORTANT]
> Mer information om maskininlärning och hur du kommer åt och använder den finns i [Vad är maskininlärning](../studio/what-is-machine-learning.md). Dessutom täcker [Azure AI Gallery](https://gallery.cortanaintelligence.com/) ett urval av experiment och ger en grundlig introduktion till de olika funktionerna i Machine Learning.
> 
> 

## <a name="license-information"></a>Licensinformation
Det här exemplet genomgång och dess medföljande skript delas av Microsoft under MIT-licensen. Mer information finns i **filen LICENSE.txt** i katalogen för exempelkoden på GitHub.

## <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi Trips Ladda ner sida](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC:s taxiresa data av Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



