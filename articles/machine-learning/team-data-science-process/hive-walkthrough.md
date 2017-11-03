---
title: Utforska data i ett Hadoop-kluster och skapa modeller i Azure Machine Learning | Microsoft Docs
description: "Med hjälp av Team datavetenskap Process för en slutpunkt till slutpunkt-scenario med HDInsight Hadoop-kluster för att skapa och distribuera en modell med en offentligt tillgängliga dataset."
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 238b7d6bb6289b5f2e8d2a20f4335724087dfd48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team vetenskap av data i praktiken: Använd Azure HDInsight Hadoop-kluster
I den här genomgången ska vi använda den [Team Data vetenskap processen (TDSP)](overview.md) i ett scenario för slutpunkt till slutpunkt med hjälp av en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och funktion tekniker data från den offentligt tillgängliga [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset, och exempeldata nedåt. Modeller av data skapas med Azure Machine Learning att hantera binära och multiklass-baserad klassificering och regression förutsägande uppgifter.

En genomgång som visar hur du hanterar en större datamängd (1 terabyte) för en liknande scenario med HDInsight Hadoop-kluster för databearbetning finns [Team datavetenskap Process, med hjälp av Azure HDInsight Hadoop-kluster i en dataset 1 TB](hive-criteo-walkthrough.md) .

Det är också möjligt att använda en bärbar dator IPython för att utföra aktiviteter visas den här genomgången använder 1 TB dataset. Användare som vill använda den här metoden bör kontakta den [Criteo genomgången använder en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) avsnittet.

## <a name="dataset"></a>NYC Taxi resor Dataset-beskrivning
NYC Taxi resa data är cirka 20GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48GB okomprimerade), som består av fler än 173 miljoner enskilda resor och priser betalat för varje resa. Hämtning och Samlingsbibliotek plats och tid, anonymiserade hackare (drivrutin) licensnummer och medallion (taxi's unikt id) nummer innehåller varje resa-post. Data omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

1. 'Trip_data' CSV-filer innehåller resa information, till exempel antal passagerare, hämtning och dropoff punkter, resa varaktighet och resa längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. 'Trip_fare' CSV-filer innehåller information om avgiften betalat för varje förflyttning, till exempel betalningssätt, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala betald. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unik nyckel för att ansluta till resa\_data och resa\_avgiften består av fälten: medallion hackare\_licensen och hämtning\_datetime.

För att få alla detaljer relevanta för en viss resa, det räcker att ansluta med tre nycklar: ”medallion” ”, hacka\_licens” och ”hämtning\_datetime”.

Vi beskriver vissa mer information om data när vi lagrar dem i Hive-tabeller inom kort.

## <a name="mltasks"></a>Exempel på förutsägelse uppgifter
När närmar sig data som fastställer typ av du vill göra förutsägelser baserat på dess analys hjälper till att klargöra de uppgifter som du behöver ta med i processen.
Här följer tre exempel på förutsägelse problem som vi adressen i den här genomgången vars formulering baseras på den *tips\_belopp*:

1. **Binär klassificering**: förutsäga oavsett betalats ett tips för en resa i d.v.s. en *tips\_belopp* som är större än 0 är ett positivt exempel, när en *tips\_belopp* $0 är ett exempel på negativt.
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0
2. **Multiklass-baserad klassificering**: att förutsäga intervallet av tips för resan. Vi dela den *tips\_belopp* i fem lagerplatser eller klasser:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regression uppgiften**: att förutsäga mängden tips för en resa.  

## <a name="setup"></a>Konfigurera en HDInsight Hadoop-kluster för avancerade analyser
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

Du kan konfigurera en Azure-miljö för avancerade analyser som använder ett HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md): det här lagringskontot används för att lagra data i Azure Blob Storage. De data som används i HDInsight-kluster finns också här.
2. [Anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics Process- och teknikbehov](customize-hadoop-cluster.md). Det här steget skapar ett Azure HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerad på alla noder. Det finns två viktiga steg för att komma ihåg vid anpassning av ditt HDInsight-kluster.
   
   * Kom ihåg att länka storage-konto som skapades i steg 1 med ditt HDInsight-kluster när du skapar den. Det här lagringskontot används för att komma åt data som bearbetas i klustret.
   * När klustret har skapats kan du aktivera fjärråtkomst till huvudnod i klustret. Navigera till den **Configuration** och klicka på **aktivera fjärråtkomst**. Det här steget anger de autentiseringsuppgifter som används för fjärrinloggning.
3. [Skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md): den här Azure Machine Learning-arbetsytan används för att skapa machine learning-modeller. Den här uppgiften riktar sig när du har slutfört en första datagranskning och ned med hjälp av HDInsight-klustret.

## <a name="getdata"></a>Hämta data från en offentlig källa
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

Att hämta den [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset från dess offentlig plats, kan du använda någon av metoderna som beskrivs i [flytta Data till och från Azure Blob Storage](move-azure-blob.md) att kopiera data till din dator.

Här beskrivs hur använder AzCopy för att överföra filer som innehåller data. Hämta och installera AzCopy följer du anvisningarna [komma igång med kommandoradsverktyget Azcopy](../../storage/common/storage-use-azcopy.md).

1. Från Kommandotolken, utfärda följande AzCopy kommandona, ersätter *< path_to_data_folder >* med önskat mål:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. När kopieringen är klar är totalt av 24 komprimerade filer i datamappen valt. Packa upp de hämtade filerna i samma katalog på den lokala datorn. Anteckna den mapp där de okomprimerade filerna finns. Den här mappen kommer kallas den *< sökväg\_till\_unzipped_data\_filer\>*  är det som följer.

## <a name="upload"></a>Ladda upp data till standardbehållaren för Azure HDInsight Hadoop-kluster
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

Ersätt följande parametrar i följande AzCopy-kommandon med de faktiska värdena som du angav när du skapar Hadoop-kluster och packa upp datafilerna.

* ***&#60; path_to_data_folder >*** katalogen (tillsammans med sökväg) på datorn som innehåller de uppackade datafilerna  
* ***&#60; lagringskontonamnet av Hadoop-kluster >*** storage-konto som är kopplad till ditt HDInsight-kluster
* ***&#60; standardbehållaren för Hadoop-kluster >*** standardbehållaren som används av klustret. Observera att namnet på standardbehållaren är vanligtvis samma namn som själva klustret. Om klustret kallas ”abc123.azurehdinsight.net”, är standardbehållaren abc123.
* ***&#60; lagringskontonyckel >*** nyckel för storage-konto som används av klustret

Kör följande två AzCopy-kommandon från en kommandotolk eller ett Windows PowerShell-fönster på datorn.

Det här kommandot överför data resa till ***nyctaxitripraw*** katalog i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Det här kommandot överför data till avgiften ***nyctaxifareraw*** katalog i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data bör nu i Azure Blob Storage och redo att användas i HDInsight-klustret.

## <a name="#download-hql-files"></a>Logga in på huvudnod Hadoop-kluster och och förbereda för undersökande dataanalys
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

För att komma åt huvudnod i klustret för undersökande dataanalys och ned sampling av data, följer du proceduren som beskrivs i [komma åt det Head nod för Hadoop-kluster](customize-hadoop-cluster.md#headnode).

I den här genomgången ska vi i första hand använder frågor som skrivits i [Hive](https://hive.apache.org/), en SQL-liknande query language att utföra preliminära data explorations. Hive-frågor som lagras i .hql filer. Vi sedan exempel ned på dessa data som ska användas i Azure Machine Learning för att skapa modeller.

För att förbereda klustret för undersökande dataanalys, vi hämtar .hql filer som innehåller de relevanta Hive-skript från [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) till en lokal katalog (C:\temp) i huvudnod. Det gör du genom att öppna den **kommandotolk** från inom huvudnod i klustret och utfärda följande två kommandon:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Dessa två kommandon hämtas alla .hql filer som behövs i den här genomgången till den lokala katalogen ***C:\temp &#92;*** i huvudnoden.

## <a name="#hive-db-tables"></a>Skapa Hive-databasen och tabeller som är partitionerad per månad
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

Vi är nu redo att skapa Hive-tabeller för våra NYC taxi dataset.
Öppna i huvudnod i Hadoop-kluster i ***Hadoop kommandoraden*** på skrivbordet för huvudnoden, och ange Hive-katalogen genom att ange kommandot

    cd %hive_home%\bin

> [!NOTE]
> **Kör alla Hive-kommandon i den här genomgången från ovan Hive bin / directory-fråga. Detta hand tar om eventuella problem med sökväg automatiskt. Vi använder termer ”Hive directory prompt” ”, Hive bin / directory prompt”, och ”Hadoop kommandoraden” synonymt i den här genomgången.**
> 
> 

Ange följande kommando i Hadoop kommandoraden för huvudnoden att skicka Hive-fråga för att skapa Hive-databasen och tabeller från Hive directory prompten:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Här är innehållet i den ***C:\temp\sample\_hive\_skapa\_db\_och\_tables.hql*** -fil som skapar Hive databas ***nyctaxidb*** och tabeller ***resa*** och ***avgiften***.

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

Den här Hive-skript skapar två tabeller:

* tabellen ”resa” innehåller resa information om varje resa (drivrutinsinformation, pickup tid, resa avstånd och gånger)
* tabellen ”avgiften” innehåller information om avgiften (avgiften, tips belopp, vägtullar och tillägg).

Om du behöver ytterligare hjälp med dessa procedurer eller vill undersöka de alternativ som finns i avsnittet [skicka Hive-frågor direkt från kommandoraden i Hadoop ](move-hive-tables.md#submit).

## <a name="#load-data"></a>Läs in Data till Hive-tabeller av partitioner
> [!NOTE]
> Detta är vanligtvis en **Admin** aktivitet.
> 
> 

NYC taxi datamängden har en naturlig partitionering per månad som vi använder för att aktivera snabbare bearbetning och fråga. PowerShell-kommandona nedan (utfärdats från en Hive directory med hjälp av den **Hadoop kommandoraden**) att läsa in data ”resa” och ”avgiften” Hive tabeller partitionerad per månad.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Den *exempel\_hive\_ladda\_data\_av\_partitions.hql* filen innehåller följande **LADDA** kommandon.

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Observera att ett antal Hive-frågor som vi använder här pågående utforskning innebär att bara en enda partition eller på bara några partitioner. Men dessa frågor kan köras över hela data.

### <a name="#show-db"></a>Visa databaser i HDInsight Hadoop-kluster
Om du vill visa de databaser som skapas i HDInsight Hadoop-kluster i Hadoop kommandoradsfönster, kör du följande kommando i Hadoop kommandoraden:

    hive -e "show databases;"

### <a name="#show-tables"></a>Visa Hive-tabeller i databasen nyctaxidb
Om du vill visa tabellerna i databasen nyctaxidb, kör du följande kommando i Hadoop kommandoraden:

    hive -e "show tables in nyctaxidb;"

Vi kan bekräfta att partitioneras tabellerna genom att utfärda kommandot nedan:

    hive -e "show partitions nyctaxidb.trip;"

Förväntad utdata visas nedan:

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

På liknande sätt kan vi se till att avgiften tabellen är partitionerad genom att utfärda kommandot nedan:

    hive -e "show partitions nyctaxidb.fare;"

Förväntad utdata visas nedan:

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

## <a name="#explore-hive"></a>Datagranskning och funktionen teknikerna i Hive
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Funktionen tekniska uppgifter för de data som läses in i Hive-tabeller och undersökning av data kan utföras med hjälp av Hive-frågor. Här följer exempel på sådana uppgifter att vi dig igenom i det här avsnittet:

* Visa översta 10 posterna i båda tabellerna.
* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök data quality longitud och latitud fält.
* Generera binära och multiklass-baserad klassificeringsetiketter baserat på de **tips\_belopp**.
* Generera funktioner genom att beräkna direkt kommunikation avstånd.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Undersökning: Visa de översta 10 posterna i tabellen resa
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Om du vill se hur data ser ut, granska vi 10 poster från varje tabell. Kör följande två frågor separat från Hive directory-fråga i kommandoraden för Hadoop-konsolen för att kontrollera posterna.

Hämta de översta 10 posterna i tabellen ”resa” från den första månaden:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Hämta de översta 10 posterna i tabellen ”avgiften” från den första månaden:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Det vara bra att spara poster till en fil för lämplig visning. En mindre ändring i ovanstående frågan åstadkommer detta:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Undersökning: Visa antalet poster i var och en av de 12 partitionerna
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Intressanta är hur antalet turer varierar under kalenderåret. Gruppera efter månad kan vi se hur den här distributionen av resor ser ut.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Detta ger oss utdata:

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

Här är den första kolumnen är månaden och andra antalet turer för den månaden.

Vi kan även räkna antalet poster i vår datauppsättning för kommunikation med följande kommando i Kommandotolken Hive directory.

    hive -e "select count(*) from nyctaxidb.trip;"

Detta ger:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Med kommandon som liknar de som visas för datauppsättningen resan, kan vi utfärda Hive-frågor från Hive directory prompten för datauppsättningen avgiften att validera antalet poster.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Detta ger oss utdata:

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

Observera att exakt samma antal turer månaden returneras för både datamängder. Detta ger första verifieringen att data har lästs in korrekt.

Räknar antalet poster i datauppsättningen avgiften kan göras med kommandot nedan i Hive directory-fråga:

    hive -e "select count(*) from nyctaxidb.fare;"

Detta ger:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Det totala antalet poster i båda tabellerna är också samma. Detta ger en andra verifiering att data har lästs in korrekt.

### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: Resa distribution av medallion
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Det här exemplet identifierar medallion (taxi numbers) med mer än 100 resor inom en viss tidsperiod. Frågan fördelar från partitionerad tabellåtkomst eftersom den är villkorad av variabeln partition **månad**. Frågeresultatet skrivs till en lokal fil queryoutput.tsv i `C:\temp` i huvudnod.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Här är innehållet *exempel\_hive\_resa\_antal\_av\_medallion.hql* filen för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion i datauppsättningen NYC taxi identifierar en unik CAB-fil. Vi kan identifiera vilka CAB-filer är ”upptagna” genom att fråga vilka som gjorts mer än ett visst antal turer under en viss tidsperiod. I följande exempel identifierar CAB-filer som gjorts flera hundra resor i den första tre månader och sparar frågan resultatet till en lokal fil, C:\temp\queryoutput.tsv.

Här är innehållet *exempel\_hive\_resa\_antal\_av\_medallion.hql* filen för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Utfärda kommandot nedan Hive directory-prompten:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Undersökning: Resa distribution av medallion och hack_license
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

När du utforskar en datamängd, vill vi ofta undersöka antalet co-förekomster av grupper av värden. Det här avsnittet ger ett exempel på hur du gör detta för CAB-filer och drivrutiner.

Den *exempel\_hive\_resa\_antal\_av\_medallion\_license.hql* filgrupper avgiften datamängden på ”medallion” och ”hack_license” och Returnerar antal varje kombination. Nedan visas dess innehåll.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Den här frågan returnerar drivrutinen kombinationer sorterade efter fallande antalet turer och CAB-filen.

Från Kommandotolken Hive directory kör du:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Frågeresultatet skrivs till en lokal fil C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Undersökning: Utvärdera data quality genom att söka efter ogiltig longitud/latitud poster
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Ett av undersökande dataanalys vanliga mål är att filtrera ut ogiltiga eller felaktiga poster. Exemplet i det här avsnittet anger om fälten longituden eller latituden innehålla ett värde långt utanför området NYC. Eftersom det är troligt att dessa poster har en felaktig longitud-latitudvärden som vi vill ta bort dem från alla data som ska användas för modellering.

Här är innehållet *exempel\_hive\_kvalitet\_assessment.hql* filen för inspektion.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Från Kommandotolken Hive directory kör du:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Den *-S* argumentet som ingår i det här kommandot förhindrar status skärmen utskriften Hive kartan/minska jobb. Detta är användbart eftersom det gör skärmen utskrift av Hive-frågan mer lättläst.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Undersökning: Binära klassen distributioner av resa tips
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

För binära klassificeringsproblem som beskrivs i den [exempel på uppgifter som förutsägelse](hive-walkthrough.md#mltasks) avsnittet är det bra att känna av om ett tips gavs eller inte. Den här distributionen av tips är binär:

* Tips angivna (klass 1, tips\_belopp > 0)  
* Inget tips (0-klassen, tips\_belopp = 0).

Den *exempel\_hive\_lutad\_frequencies.hql* -exempelfilen nedan gör detta.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Från Kommandotolken Hive directory kör du:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Undersökning: Klassen distributioner i inställningen för multiklass-baserad
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

För multiklass-baserad klassificering problemet som beskrivs i den [exempel på uppgifter som förutsägelse](hive-walkthrough.md#mltasks) avsnittet datamängden också lämpar sig för en fysisk klassificering där vi vill förutsäga mängden tips angivna. Vi kan använda lagerplatser för att definiera tips intervall i frågan. Om du vill hämta klassen distributioner för de olika tips intervall, vi använder den *exempel\_hive\_tips\_intervallet\_frequencies.hql* fil. Nedan visas dess innehåll.

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

Kör följande kommando från kommandoraden för Hadoop-konsolen:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Undersökning: Compute direkt avståndet mellan två longitud latitud-platser
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

Om du har ett mått på direkt avståndet kan oss att ta reda på avvikelse mellan det och det faktiska resa avståndet. Vi motivera funktionen genom att peka att passagerare kanske mindre troligt att tips om de ta reda på att drivrutinen avsiktligt vidtagit dem med en mycket längre väg.

Se en jämförelse mellan faktiska resa avstånd och [Haversine avstånd](http://en.wikipedia.org/wiki/Haversine_formula) mellan två longitud latitud punkter (”” storcirkelavståndet), vi använda trigonometriska funktioner som finns i Hive, därför:

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

R är radius jordens i mil i frågan ovan och pi konverteras till radianer. Observera att longitud latitud punkter ”filtreras” för att ta bort värden som är långt från området NYC.

I det här fallet skriva vi våra resultat till en katalog med namnet ”queryoutputdir”. De kommandon som visas nedan skapar den här målkatalogen först och kör sedan kommandot Hive.

Från Kommandotolken Hive directory kör du:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Frågeresultatet skrivs till 9 Azure BLOB ***queryoutputdir/000000\_0*** till ***queryoutputdir/000008\_0*** under standardbehållaren för Hadoop-kluster.

Om du vill visa storleken på enskilda blobbar kör vi följande kommando från katalogen Hive-prompten:

    hdfs dfs -ls wasb:///queryoutputdir

Om du vill visa innehållet i en viss fil, säg 000000\_0, använder vi Hadoop's `copyToLocal` kommandot därför.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`rekommenderas inte för användning med dem och kan ta mycket lång tid för stora filer.  
> 
> 

En stor fördel med dessa data finns i en Azure blob är att vi kan utforska data i Azure Machine Learning med hjälp av den [importera Data] [ import-data] modul.

## <a name="#downsample"></a>Ned exempel data och bygga modeller i Azure Machine Learning
> [!NOTE]
> Detta är vanligtvis en **Data forskare** aktivitet.
> 
> 

När analysfasen undersökande data är vi nu redo att ned exempel data för att skapa modeller i Azure Machine Learning. I det här avsnittet beskrivs hur du använder en Hive-fråga till exempel nedåt data, som sedan kan nås från den [importera Data] [ import-data] modul i Azure Machine Learning.

### <a name="down-sampling-the-data"></a>Ned provtagning data
Det finns två steg i den här proceduren. Först vi ansluta den **nyctaxidb.trip** och **nyctaxidb.fare** tabeller på tre nycklar som finns i alla poster: ”medallion” ”, hacka\_licens”, och ”hämtning\_datetime”. Vi sedan generera en binär klassificering etikett **lutad** och en etikett för flera klassen klassificering **tips\_klassen**.

För att kunna använda ned exempeldata direkt från den [importera Data] [ import-data] modul i Azure Machine Learning är det nödvändigt att lagra resultaten av ovanstående fråga till en intern Hive-tabell. I följande, vi skapa en intern Hive-tabell och fylla i dess innehåll med den sammanfogade och ned samplade data.

Frågan använder Hive standardfunktioner direkt om du vill generera timme på dagen, veckan på året, veckodag (1 står för måndag och 7 står för söndag) från den ”hämtning\_datetime” fältet och direkt avståndet mellan platser för hämtning och dropoff. Användare kan referera till [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) för en fullständig lista över dessa funktioner.

Frågan sedan exempel ned data så att frågeresultatet passar in i Azure Machine Learning Studio. Endast ca 1% av den ursprungliga datauppsättningen har importerats till Studio.

Nedan visas innehållet i *exempel\_hive\_förbereda\_för\_aml\_full.hql* filen som förbereder data för modellen skapar i Azure Machine Learning.

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

Att köra den här frågan från Hive directory prompten:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Nu har vi en intern tabell ”nyctaxidb.nyctaxi_downsampled_dataset” som kan nås med hjälp av den [importera Data] [ import-data] modul från Azure Machine Learning. Vi kan dessutom använda denna dataset för att skapa Machine Learning-modeller.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Komma åt nedåt samplade data med modulen importera Data i Azure Machine Learning
Som krav för utfärdande av Hive frågor i den [importera Data] [ import-data] modulen för Azure Machine Learning vi behöver åtkomst till en Azure Machine Learning arbetsytan och åtkomst till autentiseringsuppgifterna för klustret och dess associerat lagringskonto.

Vissa detaljer på den [importera Data] [ import-data] modulen och parametrar för att ange:

**HCatalog server URI**: Om klusternamnet är abc123, så det är bara: https://abc123.azurehdinsight.net

**Hadoop användarkontonamnet** : användarnamnet som valts för klustret (**inte** användarnamnet fjärråtkomst)

**Kontolösenord för Hadoop ser** : lösenordet som valts för klustret (**inte** remote access-lösenordet)

**Platsen för utdata** : detta väljs ska Azure.

**Azure lagringskontonamnet** : namnet på standardkontot för lagring som är associerade med klustret.

**Azure behållarnamn** : Detta är behållare för standardnamnet för klustret och är vanligtvis samma som klusternamnet. Detta är bara abc123 för ett kluster som heter ”abc123”.

> [!IMPORTANT]
> **Alla tabeller som vi vill fråga med hjälp av den [importera Data] [ import-data] modul i Azure Machine Learning måste vara en intern tabell.** Ett tips för att avgöra om en tabell T i en databas D.db är en intern tabell är som följer.
> 
> 

Utfärda kommandot Hive directory-prompten:

    hdfs dfs -ls wasb:///D.db/T

Om tabellen är en intern tabell och det fylls, måste innehållet visas här. Ett annat sätt att avgöra om en tabell är en intern tabell är att använda Azure Lagringsutforskaren. Använd den för att navigera till behållaren för standardnamnet på klustret och sedan filtrera efter tabellens namn. Det här bekräftar att den är en intern tabell om tabellen och dess innehåll visas.

Här är en ögonblicksbild av Hive-fråga och [importera Data] [ import-data] modulen:

![Hive-fråga för modulen importera Data](./media/hive-walkthrough/1eTYf52.png)

Observera att eftersom vår ned samplade data finns i standardbehållaren resulterande Hive-frågan från Azure Machine Learning är mycket enkelt och bara en ”Välj * från nyctaxidb.nyctaxi\_upplösning\_data”.

Dataset kan nu användas som utgångspunkt för att skapa Machine Learning-modeller.

### <a name="mlmodel"></a>Bygga modeller i Azure Machine Learning
Vi kan nu fortsätta till modellskapandet och distribution av modellen i [Azure Machine Learning](https://studio.azureml.net). Data är redo för att vi ska använda i adressering förutsägelse problemen som anges ovan:

**1. Binär klassificering**: för att förutsäga om huruvida ett tips har betalat för en resa.

**Deltagaren används:** Two-class logistic regression

a. För det här problemet är våra mål (eller klassen) etiketten ”lutad”. Vår ursprungliga ned provtagning datauppsättning har några kolumner som är mål minnesläckor för klassificering experimentet. I synnerhet: tips\_klassen, tips\_belopp och totalt\_belopp avslöjar information om det mål som inte är tillgänglig vid testning tid. Vi ta bort dessa kolumner från beräkningen som använder den [Välj kolumner i datauppsättning] [ select-columns] modul.

Ögonblicksbilden nedan visar vårt experiment att förutsäga huruvida ett tips har betalat för en given resa.

![Experiment ögonblicksbild](./media/hive-walkthrough/QGxRz5A.png)

b. Det här experimentet har våra mål etikett distributioner ungefär 1:1.

Ögonblicksbilden nedan visar fördelningen av tips klassen etiketter för binära klassificeringsproblem.

![Distribution av tips klassen etiketter](./media/hive-walkthrough/9mM4jlD.png)

Därför kan får vi en AUC av 0.987 som visas i bilden nedan.

![AUC värde](./media/hive-walkthrough/8JDT0F8.png)

**2. Multiklass-baserad klassificering**: att förutsäga intervallet av tips för resan, med hjälp av de tidigare definierade klasserna.

**Deltagaren används:** multiklass-baserad logistic regression

a. För det här problemet är vårt mål (eller klassen) etiketten ”tips\_klass” vilket kan ta en av fem värden (0,1,2,3,4). Som i fallet med binär klassificering har vi några kolumner som är mål minnesläckor för experimentet. I synnerhet: lutad, tips\_totala angivet\_belopp avslöjar information om det mål som inte är tillgänglig vid testning tid. Vi ta bort dessa kolumner med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modul.

Ögonblicksbilden nedan visar vårt experiment att förutsäga ett tips är troligt att hamna i vilka bin (klass 0: tips = 0, klass 1: tips > 0 och tips < = $5, klass 2: tips > 5 och tips < = $10, klass 3: tips > 10 och tips < = $20 Klass 4: tips > 20)

![Experiment ögonblicksbild](./media/hive-walkthrough/5ztv0n0.png)

Nu visas hur våra faktiska test klassen distribution ser ut. Vi finns att medan klassen 0 och 1 för klassen är vanliga, andra klasser sällsynta.

![Testa klassen distribution](./media/hive-walkthrough/Vy1FUKa.png)

b. Det här experimentet använder vi en förvirring matris för att titta på vår prognosens noggrannhet. Detta visas nedan.

![Förvirring matris](./media/hive-walkthrough/cxFmErM.png)

Observera att vår klass noggrannhet på vanliga klasser är ganska bra, modellen utför inte bra på ”learning” på sällsynta klasser.

**3. Regression uppgiften**: att förutsäga mängden tips för en resa.

**Deltagaren används:** Boosted beslutsträdet

a. För det här problemet är vårt mål (eller klassen) etiketten ”tips\_belopp”. I det här fallet är vårt mål minnesläckor: lutad, tips\_klass, totalt antal\_belopp; dessa variabler avslöja information om hur tips som är vanligtvis inte tillgängliga vid testning tid. Vi ta bort dessa kolumner med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modul.

Ögonblicksbild belows visar vårt experiment att förutsäga mängden angivna tips.

![Experiment ögonblicksbild](./media/hive-walkthrough/11TZWgV.png)

b. Vi mäter noggrannhet med vår prognoser för genom att titta på kvadratfel i förutsägelser, bestämningskoefficienten och liknande för regressionsproblem. Vi visas dessa nedan.

![Förutsägelse statistik](./media/hive-walkthrough/Jat9mrz.png)

Vi se att om bestämningskoefficienten är 0.709 förklaras innebär ungefär 71% varians med vår modell koefficienter.

> [!IMPORTANT]
> Om du vill veta mer om Azure Machine Learning och hur du använder den, se [vad är Machine Learning?](../studio/what-is-machine-learning.md). En resurs som är användbar för att spela upp med en massa Machine Learning-experiment i Azure Machine Learning är den [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/). Galleriet omfattar en alla experiment och ger en omfattande introduktion till nya funktioner i Azure Machine Learning.
> 
> 

## <a name="license-information"></a>Licensinformationen
Den här genomgången exempel och dess tillhörande skript som delas av Microsoft under MIT-licensen. Kontrollera filen LICENSE.txt i katalogen exempelkoden på GitHub för mer information.

## <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi resor hämtningssidan](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxitransport resa Data av Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
