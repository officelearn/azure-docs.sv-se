---
title: Utforska data i ett Hadoop-kluster och skapa modeller i Azure Machine Learning | Microsoft Docs
description: Med hjälp av Team datavetenskap Process för en slutpunkt till slutpunkt-scenario med ett HDInsight Hadoop-kluster för att skapa och distribuera en modell.
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: beb1c2f88eed8fc38bd32de113835122cfd4e9a4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837335"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team vetenskap av data i praktiken: Använd Azure HDInsight Hadoop-kluster
I den här genomgången ska vi använda den [Team Data vetenskap processen (TDSP)](overview.md) i ett scenario för slutpunkt till slutpunkt. Vi använder en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) att lagra, utforska, och funktionen-tekniker data från den allmänt tillgängliga [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset, och att ned exempel data. Om du vill hantera binära och multiklass-baserad klassificering och regression förutsägande uppgifter, bygga vi modeller av data med Azure Machine Learning. 

En genomgång som visar hur du hanterar en större datamängd finns [Team datavetenskap Process, med hjälp av Azure HDInsight Hadoop-kluster i en dataset 1 TB](hive-criteo-walkthrough.md).

Du kan också använda en bärbar dator IPython för att utföra uppgifter som visas i den här genomgången som använder 1 TB dataset. Mer information finns i [Criteo genomgången använder en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC Taxi resor dataset beskrivning
NYC Taxi resa data är cirka 20 GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48 GB okomprimerade). Den har mer än 173 miljoner enskilda resor och inkluderar priser för varje resa. Varje post resa innehåller vill hämta och Samlingsbibliotek plats och tid, anonymiserade hackare (drivrutin) licensnummer och medallion nummer (den taxi unikt ID). Informationen omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

- Trip_data CSV-filer innehåller resa information. Detta inkluderar antalet passagerare, vill hämta och lämna, resa varaktighet och resa längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Trip_fare CSV-filer innehåller information om avgiften betalat för varje resa. Detta inkluderar betalningssätt, avgiften belopp, tillägg och skatter, tips och vägtullar och Totalmängd betald. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unik nyckel för att ansluta till resa\_data och resa\_avgiften består av fälten: medallion hackare\_licens och hämtning\_datetime. För att få alla detaljer relevanta för en viss resa, räcker det att ansluta med dessa tre nycklar.

## <a name="mltasks"></a>Exempel på förutsägelse uppgifter
Fastställa vilken typ av du vill göra förutsägelser baserat på dataanalys. På så sätt kan tydliggöra uppgifter du behöver ta med i processen. Här följer tre exempel av förutsägelse problem som vi adressen i den här genomgången. De bygger på den *tips\_belopp*:

- **Binär klassificering**: förutsäga huruvida ett tips har betalat för en resa. Som är en *tips\_belopp* som är större än 0 är ett positivt exempel, när en *tips\_belopp* $0 är ett exempel på negativt.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Multiklass-baserad klassificering**: förutsäga intervallet av tips för resan. Vi dela den *tips\_belopp* i fem klasser:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regression uppgiften**: förutsäga mängden tips för en resa.  

## <a name="setup"></a>Konfigurera en HDInsight Hadoop-kluster för avancerade analyser
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

Du kan konfigurera en Azure-miljö för avancerade analyser som använder ett HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md): det här lagringskontot används för att lagra data i Azure Blob storage. De data som används i HDInsight-kluster finns också här.
2. [Anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics Process- och teknikbehov](customize-hadoop-cluster.md). Det här steget skapar ett HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerad på alla noder. Det finns två viktiga steg för att komma ihåg vid anpassning av ditt HDInsight-kluster.
   
   * Kom ihåg att länka storage-konto som skapades i steg 1 med ditt HDInsight-kluster när du skapar den. Det här lagringskontot kommer åt data som bearbetas i klustret.
   * Aktivera fjärråtkomst till huvudnod i klustret när du har skapat klustret. Bläddra till den **Configuration** fliken och markera **aktivera fjärråtkomst**. Det här steget anger de autentiseringsuppgifter som används för fjärrinloggning.
3. [Skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md): du använder den här arbetsytan för att skapa machine learning-modeller. Den här uppgiften riktar när du har slutfört en första datagranskning och ned provtagning med hjälp av HDInsight-klustret.

## <a name="getdata"></a>Hämta data från en offentlig källa
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

Kopiera den [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset till din dator från dess offentlig plats, använder någon av metoderna som beskrivs i [flytta data till och från Azure Blob storage](move-azure-blob.md).

Här beskrivs hur du använder AzCopy för att överföra filer som innehåller data. Om du vill hämta och installera AzCopy, följer du anvisningarna [komma igång med kommandoradsverktyget azcopy](../../storage/common/storage-use-azcopy.md).

1. Från Kommandotolken, kör du följande AzCopy kommandona, ersätter *< path_to_data_folder >* med önskat mål:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. När kopieringen är klar visas totalt av 24 komprimerade filer i datamappen valt. Packa upp de hämtade filerna i samma katalog på den lokala datorn. Anteckna den mapp där de okomprimerade filerna finns. Den här mappen kallas den *< sökväg\_till\_unzipped_data\_filer\>*  i vilka sätt.

## <a name="upload"></a>Ladda upp data till standardbehållaren för HDInsight Hadoop-kluster
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

Ersätt följande parametrar i följande AzCopy-kommandon med de faktiska värdena som du angav när du skapar Hadoop-kluster och packa upp datafilerna.

* ***< Path_to_data_folder >*** katalogen (tillsammans med sökväg) på datorn som innehåller de uppackade datafilerna.  
* ***<storage account name of Hadoop cluster>*** Storage-konto som är kopplad till ditt HDInsight-kluster.
* ***<default container of Hadoop cluster>*** Standardbehållaren som används av klustret. Observera att namnet på standardbehållaren är vanligtvis samma namn som själva klustret. Om klustret kallas ”abc123.azurehdinsight.net”, är standardbehållaren abc123.
* ***<storage account key>*** Nyckel för lagringskontot som används av klustret.

Kör följande två AzCopy-kommandon från en kommandotolk eller ett Windows PowerShell-fönster.

Det här kommandot överför resa data till den ***nyctaxitripraw*** katalog i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Det här kommandot överför avgiften data till den ***nyctaxifareraw*** katalog i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data bör nu vara i Blob storage och redo att användas i HDInsight-klustret.

## <a name="#download-hql-files"></a>Logga in till huvudnod i Hadoop-kluster och förbereda för undersökande dataanalys
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

För att komma åt huvudnod i klustret för undersökande dataanalys och ned sampling av data, följer du proceduren som beskrivs i [åtkomst till huvudnod Hadoop-kluster](customize-hadoop-cluster.md).

I den här genomgången ska vi i första hand använder frågor som skrivits i [Hive](https://hive.apache.org/), en SQL-liknande query language att utföra preliminära data explorations. Hive-frågor som lagras i .hql filer. Vi sedan ned sampel dessa data som ska användas inom Maskininlärning för att skapa modeller.

Hämta .hql filer som innehåller de relevanta Hive-skript från att förbereda klustret för undersökande dataanalys, [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) till en lokal katalog (C:\temp) i huvudnod. Öppna Kommandotolken inifrån huvudnod i klustret för att göra detta, och kör följande två kommandon:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Dessa två kommandon hämta alla .hql filer som behövs i den här genomgången till den lokala katalogen ***C:\temp&#92;***  i huvudnoden.

## <a name="#hive-db-tables"></a>Skapa Hive-databasen och tabeller som är partitionerad per månad
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

Du är nu redo att skapa Hive-tabeller för NYC taxi dataset.
Öppna kommandoraden Hadoop på skrivbordet för huvudnoden i huvudnod i Hadoop-kluster. Ange Hive-katalogen genom att köra följande kommando:

    cd %hive_home%\bin

> [!NOTE]
> Kör alla Hive-kommandon i den här genomgången från Hive bin / directory-fråga. Detta hanterar automatiskt eventuella problem med sökvägen. Vi använder termer ”Hive directory prompt” ”, Hive bin / directory prompt”, och ”Hadoop kommandoraden” synonymt i den här genomgången.
> 
> 

Kör följande kommando i Hadoop-kommandoraden för huvudnoden från Hive directory prompten. Detta skickar Hive-fråga för att skapa Hive-databasen och tabeller:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Här är innehållet i den **C:\temp\sample\_hive\_skapa\_db\_och\_tables.hql** fil. Detta skapar databasen Hive **nyctaxidb**, tabellerna och **resa** och **avgiften**.

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

* Den **resa** tabellen innehåller resa information om varje resa (drivrutinsinformation, är tid, resa avstånd och gånger).
* Den **avgiften** tabellen innehåller information om avgiften (avgiften, tips belopp, vägtullar, och tillägg).

Om du behöver ytterligare hjälp med de här procedurerna eller om du vill undersöka de alternativ som finns i avsnittet [skicka Hive-frågor direkt från kommandoraden Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Läs in data till Hive-tabeller av partitioner
> [!NOTE]
> Detta är vanligtvis en admin-aktivitet.
> 
> 

NYC taxi datamängden har en naturlig partitionering per månad som vi använder för att aktivera snabbare bearbetning och fråga. Följande PowerShell-kommandon (utfärdats från katalogen Hive med Hadoop-kommandoraden) läsa in data till resan och färdavgiften Hive-tabeller, partitionerad per månad.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Den **exempel\_hive\_ladda\_data\_av\_partitions.hql** filen innehåller följande **LADDA** kommandon:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Observera att ett antal Hive-frågor används här i utforskning innebär tittar på bara en eller två partitioner. Men du kan köra dessa frågor över hela datamängden.

### <a name="#show-db"></a>Visa databaser i HDInsight Hadoop-kluster
Om du vill visa de databaser som skapas i HDInsight Hadoop-kluster i Hadoop kommandoradsfönster, kör du följande kommando i Hadoop-kommandoraden:

    hive -e "show databases;"

### <a name="#show-tables"></a>Visa Hive-tabeller i den **nyctaxidb** databas
Att visa tabellerna i den **nyctaxidb** databasen, kör följande kommando i Hadoop-kommandoraden:

    hive -e "show tables in nyctaxidb;"

Vi kan bekräfta att partitioneras tabellerna genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.trip;"

Här är utdatan som förväntas:

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

På liknande sätt kan vi se till att avgiften tabellen är partitionerad genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.fare;"

Här är utdatan som förväntas:

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
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Du kan använda Hive-frågor för att utföra funktionen tekniska uppgifter för de data som läses in i Hive-tabeller och undersökning av data. Här följer exempel på sådana uppgifter:

* Visa översta 10 posterna i båda tabellerna.
* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök data quality longitud och latitud fält.
* Generera binära och multiklass-baserad klassificeringsetiketter baserat på mängden tips.
* Generera funktioner genom att beräkna direkt kommunikation avstånd.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Undersökning: Visa de översta 10 posterna i tabellen resa
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Granska 10 poster från varje tabell om du vill se hur data ser ut. Om du vill kontrollera posterna att köra följande två frågor separat från Hive directory efterfrågas i Hadoop kommandoradskonsol.

Hämta de översta 10 posterna i tabellen kommunikation från den första månaden:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Hämta de översta 10 posterna i tabellen avgiften från den första månaden:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Du kan spara poster till en fil för lämplig visning. En mindre ändring till den föregående frågan åstadkommer detta:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Undersökning: Visa antalet poster i var och en av de 12 partitionerna
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Är av intresse hur antalet turer varierar under kalenderåret. Gruppera efter månad visar fördelningen av resor.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Detta ger oss följande utdata:

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

Här är den första kolumnen är månaden och andra är antalet turer för den månaden.

Vi kan även räkna antalet poster i vår datauppsättning resa genom att köra följande kommando i Kommandotolken Hive directory:

    hive -e "select count(*) from nyctaxidb.trip;"

Detta ger:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Med kommandon som liknar de som visas för resa dataset, kan vi utfärda Hive-frågor från Hive directory prompten att validera antalet poster avgiften datauppsättningen.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Detta ger oss följande utdata:

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

Observera att exakt samma antal turer månaden returneras för både datauppsättningar. Detta ger första verifieringen att data har lästs in korrekt.

Du kan räkna antalet poster i datamängden avgiften med hjälp av följande kommando från Hive directory prompten:

    hive -e "select count(*) from nyctaxidb.fare;"

Detta ger:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Det totala antalet poster i båda tabellerna är också samma. Detta ger en andra verifiering att data har lästs in korrekt.

### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: Resa distribution av medallion
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Det här exemplet identifierar medallions (taxi numbers) med mer än 100 resor inom en viss tidsperiod. Frågan fördelar från partitionerad tabell-åtkomst eftersom den är villkorad av variabeln partition **månad**. Frågeresultatet skrivs till en lokal fil **queryoutput.tsv**i `C:\temp` i huvudnod.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Här är innehållet i den **exempel\_hive\_resa\_antal\_av\_medallion.hql** filen för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion i NYC taxi datamängden identifierar en unik CAB-fil. Du kan identifiera vilka CAB-filer är relativt upptagna genom att fråga vilka som gjorts mer än ett visst antal turer under en viss tidsperiod. I följande exempel identifierar CAB-filer som gjorts flera hundra resor i den första tre månader och sparar frågan resultatet till en lokal fil **C:\temp\queryoutput.tsv**.

Här är innehållet i den **exempel\_hive\_resa\_antal\_av\_medallion.hql** filen för inspektion.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Kör följande kommando från katalogen Hive-prompten:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Undersökning: Resa distribution av medallion och hackare licens
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

När du utforskar en datamängd, vill vi ofta undersöka antalet co-förekomster av grupper av värden. Det här avsnittet innehåller ett exempel på hur du gör detta för CAB-filer och drivrutiner.

Den **exempel\_hive\_resa\_antal\_av\_medallion\_license.hql** filgrupper avgiften datamängden på **medallion** och **hack_license**, och returnerar antalet varje kombination. Här är dess innehåll:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Den här frågan returnerar CAB-filen och drivrutin kombinationer, sorterade efter fallande antalet turer.

Från Kommandotolken Hive directory kör du:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Frågeresultatet skrivs till en lokal fil **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Undersökning: Utvärdera data quality genom att söka efter ogiltig longituden eller latituden poster
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Ett av undersökande dataanalys vanliga mål är att filtrera ut ogiltiga eller felaktiga poster. Exemplet i det här avsnittet anger om fälten longituden eller latituden innehålla ett värde långt utanför området NYC. Eftersom det är troligt att dessa poster har en felaktig longitud latitud-värde som vi vill ta bort dem från alla data som ska användas för modellering.

Här är innehållet **exempel\_hive\_kvalitet\_assessment.hql** filen för inspektion.

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
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

För binära klassificeringsproblem som beskrivs i den [exempel på uppgifter som förutsägelse](hive-walkthrough.md#mltasks) avsnittet är det bra att känna av om ett tips gavs eller inte. Den här distributionen av tips är binär:

* Tips angivna (klass 1, tips\_belopp > 0)  
* Inget tips (0-klassen, tips\_belopp = 0)

Följande **exempel\_hive\_lutad\_frequencies.hql** filen gör detta:

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
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

För multiklass-baserad klassificering problemet som beskrivs i den [exempel på uppgifter som förutsägelse](hive-walkthrough.md#mltasks) avsnittet denna dataset också lämpar sig för en fysisk klassificering för att förutsäga mängden tips angivna. Vi kan använda lagerplatser för att definiera tips intervall i frågan. Klass-distributioner för de olika tips intervall, att använda den **exempel\_hive\_tips\_intervallet\_frequencies.hql** fil. Här är dess innehåll.

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

Kör följande kommando från kommandoraden Hadoop-konsolen:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Undersökning: Compute direkt avståndet mellan två longitud latitud-platser
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

Du kanske vill veta om det är skillnad mellan direkt avståndet mellan två platser och taxi faktiska resa avståndet mellan. En passagerare kanske mindre troligt att tips om de ta reda på att drivrutinen avsiktligt vidtagit dem av en längre väg.

Se en jämförelse mellan faktiska resa avstånd och [Haversine avstånd](http://en.wikipedia.org/wiki/Haversine_formula) mellan två longitud latitud punkter (”” storcirkelavståndet), du kan använda trigonometriska funktioner som finns i Hive:

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

I den föregående frågan R radius jordens i mil och pi konverteras till radianer. Observera att longitud latitud punkter filtreras för att ta bort värden som är långt från området NYC.

I det här fallet vi skriva resultaten till en katalog med namnet **queryoutputdir**. Följande kommandosekvens först skapar den här utdatakatalogen och kör sedan kommandot Hive.

Från Kommandotolken Hive directory kör du:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Frågeresultatet skrivs till nio Azure BLOB (**queryoutputdir/000000\_0** till **queryoutputdir/000008\_0**), under standardbehållaren för Hadoop-kluster.

Om du vill visa storleken på enskilda blobbar, kör du följande kommando från Hive directory prompten:

    hdfs dfs -ls wasb:///queryoutputdir

Om du vill visa innehållet i en viss fil, säg **000000\_0**, använda Hadoop's `copyToLocal` kommando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` rekommenderas inte för användning med dem och kan ta mycket lång tid för stora filer.  
> 
> 

En stor fördel med dessa data finns i en Azure blob är att vi kan utforska data i Machine Learning med hjälp av den [importera Data] [ import-data] modul.

## <a name="#downsample"></a>Ned-sample data och bygga modeller i Machine Learning
> [!NOTE]
> Detta är vanligtvis en data forskare aktivitet.
> 
> 

När analysfasen undersökande data är vi nu redo att ned-sample data för att skapa modeller i Machine Learning. I det här avsnittet visar vi hur du använder en Hive-fråga för att ned-sample data. Machine Learning sedan ansluter till den från den [importera Data] [ import-data] modul.

### <a name="down-sampling-the-data"></a>Ned provtagning data
Det finns två steg i den här proceduren. Först vi ansluta den **nyctaxidb.trip** och **nyctaxidb.fare** tabeller på tre nycklar som finns i alla poster: **medallion**, **hacka\_ licens**, och **hämtning\_datetime**. Vi sedan generera en binär klassificering etikett **lutad**, och en etikett för multiklass-baserad klassificering, **tips\_klassen**.

För att kunna använda ned provtagning data direkt från den [importera Data] [ import-data] modul i Machine Learning, bör du lagra resultaten av den föregående frågan till en intern Hive-tabell. I följande, vi skapa en intern Hive-tabell och fylla i dess innehåll med domänanslutna och ned samplade data.

Frågan gäller standardfunktioner Hive direkt om du vill generera följande från den **hämtning\_datetime** fält:
- Timme på dagen
- Veckan på året
- veckodag (1 står för måndag och 7 står för söndag)

Frågan genererar också direkt avståndet mellan platser är och Samlingsbibliotek. En fullständig lista över dessa funktioner finns i [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Frågan sedan ned-samples data så att frågeresultatet passar in i Azure Machine Learning Studio. Endast ca 1 procent av den ursprungliga datauppsättningen har importerats till studio.

Här är innehållet i **exempel\_hive\_förbereda\_för\_aml\_full.hql** filen som förbereder data för modellen bygga i Machine Learning:

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

Nu har vi en intern tabell **nyctaxidb.nyctaxi_downsampled_dataset**, vilka kan nås med hjälp av den [importera Data] [ import-data] modul från Machine Learning. Vi kan dessutom använda denna dataset för att skapa Machine Learning-modeller.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Med modulen importera Data i Machine Learning dataåtkomst ned provtagning
Skicka Hive-frågor i den [importera Data] [ import-data] modul i Machine Learning, behöver du åtkomst till Machine Learning-arbetsytan. Du måste också tillgång till autentiseringsuppgifterna för klustret och dess associerade lagringskontot.

Här är en del information om den [importera Data] [ import-data] modulen och parametrar för att ange:

**HCatalog server URI**: Om klusternamnet är **abc123**, detta är bara: https://abc123.azurehdinsight.net.

**Hadoop användarkontonamnet**: användarnamnet som valts för klustret (inte fjärråtkomst användarnamn).

**Kontolösenord för Hadoop ser**: lösenordet som valts för klustret (inte fjärråtkomst lösenord).

**Platsen för utdata**: detta väljs ska Azure.

**Azure lagringskontonamnet**: namnet på standardkontot för lagring som är associerade med klustret.

**Azure behållarnamn**: Detta är behållare för standardnamnet för klustret och är vanligtvis samma som klusternamnet. Ett kluster kallas **abc123**, detta är abc123.

> [!IMPORTANT]
> Alla tabeller som vi vill fråga med hjälp av den [importera Data] [ import-data] modul i Machine Learning måste vara en intern tabell.
> 
> 

Så här avgör om en tabell **T** i en databas **D.db** är en intern tabell. Kör följande kommando från katalogen Hive-prompten:

    hdfs dfs -ls wasb:///D.db/T

Om tabellen är en intern tabell och det fylls, måste innehållet visas här.

Ett annat sätt att avgöra om en tabell är en intern tabell är att använda Azure Lagringsutforskaren. Använd den för att navigera till behållaren för standardnamnet på klustret och sedan filtrera efter tabellens namn. Det här bekräftar att den är en intern tabell om tabellen och dess innehåll visas.

Här är en skärmbild av Hive-fråga och [importera Data] [ import-data] modulen:

![Skärmbild av Hive-fråga för modulen importera Data](./media/hive-walkthrough/1eTYf52.png)

Eftersom våra ned provtagning data finns i standardbehållaren kommer är den resulterande Hive-frågan från Machine Learning mycket enkelt. Det är bara en **Välj * från nyctaxidb.nyctaxi\_upplösning\_data**.

Dataset kan nu användas som utgångspunkt för att skapa Machine Learning-modeller.

### <a name="mlmodel"></a>Bygga modeller i Machine Learning
Du kan nu fortsätta att modellskapandet och distribution av modellen i [Maskininlärning](https://studio.azureml.net). Data är redo för att vi ska använda i adressering de förutsägelse problem som konstaterats tidigare:

- **Binär klassificering**: för att förutsäga om huruvida ett tips har betalat för en resa.

  **Deltagaren används:** Two-class logistic regression

  a. För det här problemet etiketten mål (eller klassen) är **lutad**. Den ursprungliga datauppsättningen ned provtagning har några kolumner som är mål minnesläckor för klassificering experimentet. I synnerhet **tips\_klassen**, **tips\_belopp**, och **totala\_belopp** avslöjar information om målet etiketten som är inte tillgänglig vid testning tid. Vi ta bort dessa kolumner från behandling med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modul.

  Följande diagram visar vårt experiment att förutsäga huruvida ett tips har betalat för en given resa:

  ![Diagram över experiment](./media/hive-walkthrough/QGxRz5A.png)

  b. Det här experimentet har våra mål etikett distributioner ungefär 1:1.

   Följande diagram visar fördelningen av tips klassen etiketter för binära klassificeringsproblem:

  ![Diagram över distribution av tips klassen etiketter](./media/hive-walkthrough/9mM4jlD.png)

    Därför kan hämta vi ett område som kurvan (AUC) för 0.987, enligt följande bild:

  ![Diagram över AUC värde](./media/hive-walkthrough/8JDT0F8.png)

- **Multiklass-baserad klassificering**: att förutsäga intervallet av tips för resan, med hjälp av de tidigare definierade klasserna.

  **Deltagaren används:** multiklass-baserad logistic regression

  a. För det här problemet är vårt mål (eller klassen) etikett **tips\_klassen**, vilket kan ta en av fem värden (0,1,2,3,4). Som i fallet med binär klassificering har vi några kolumner som är mål minnesläckor för experimentet. I synnerhet **lutad**, **tips\_belopp**, och **totala\_belopp** avslöja information om det mål som inte är tillgänglig vid Testa tid. Vi ta bort dessa kolumner med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modul.

  Följande diagram visar försöket att förutsäga ett tips är troligt att hamna i vilka bin. Lagerplatser är: klass 0: tips = 0, klass 1: tips > 0 och tips < = $5, klass 2: tips > 5 och tips < = $10, klass 3: tips > 10 och tips < = $20 och klassen 4: tips > 20.

  ![Diagram över experiment](./media/hive-walkthrough/5ztv0n0.png)

  Nu visas hur faktiska test klassen distribution ser ut. Klassen 0 och 1 för klassen är vanliga och andra klasser är ovanliga.

  ![Diagram över test klassen distribution](./media/hive-walkthrough/Vy1FUKa.png)

  b. Det här experimentet använder vi en förvirring matris för att titta på prognosens noggrannhet. Detta visas här:

  ![Förvirring matris](./media/hive-walkthrough/cxFmErM.png)

  Observera att klassen noggrannhet på vanliga klasser är ganska bra, modellen utför inte bra på ”learning” på sällsynta klasser.

- **Regression uppgiften**: att förutsäga mängden tips för en resa.

  **Deltagaren används:** Boosted beslutsträdet

  a. För det här problemet etiketten mål (eller klassen) är **tips\_belopp**. Mål-minnesläckor i det här fallet är: **lutad**, **tips\_klassen**, och **totala\_belopp**. Dessa variabler avslöja information om hur tips som är vanligtvis inte tillgängliga vid testning tid. Vi ta bort dessa kolumner med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modul.

  Följande diagram visar försöket att förutsäga mängden angivna tips:

  ![Diagram över experiment](./media/hive-walkthrough/11TZWgV.png)

  b. Regression problem mäter vi noggrannhet i förutsägelser genom att titta på kvadratfel i förutsägelser och bestämningskoefficienten:

  ![Skärmbild av förutsägelse statistik](./media/hive-walkthrough/Jat9mrz.png)

  Här är förklaras bestämningskoefficienten är 0.709, innebär som cirka 71 procent av variansen med koefficienter modellen.

> [!IMPORTANT]
> Läs mer om Machine Learning och hur du använder den i [vad är Machine Learning](../studio/what-is-machine-learning.md). Dessutom kan den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/) omfattar en alla experiment och ger en omfattande introduktion till nya funktioner i Machine Learning.
> 
> 

## <a name="license-information"></a>Licensinformationen
Den här genomgången exempel och dess tillhörande skript som delas av Microsoft under MIT-licensen. Mer information finns i **LICENSE.txt** filen i katalogen för exempelkoden på GitHub.

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



