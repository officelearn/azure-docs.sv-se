---
title: Utforska data i ett Hadoop-kluster och skapa modeller i Azure Machine Learning | Microsoft Docs
description: Med Team Data Science Process för ett scenario för slutpunkt till slutpunkt, använda ett HDInsight Hadoop-kluster för att skapa och distribuera en modell.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 1b494f78998a03d39b18d4f9bba80642c04c483e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444213"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team Data Science Process i praktiken: Använd Azure HDInsight Hadoop-kluster
I den här genomgången använder vi den [Team Data Science Process (TDSP)](overview.md) i ett scenario för slutpunkt till slutpunkt. Vi använder en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) att lagra, utforska, och funktion-tekniker data från de allmänt tillgängliga [NYC Taxi kommunikation](http://www.andresmh.com/nyctaxitrips/) datauppsättningen, och att nedåtsampla data. Vi bygger modeller av data med Azure Machine Learning för att hantera binära och multiklass-baserad klassificering och regression förutsägande uppgifter. 

En genomgång som visar hur du hanterar en större datauppsättning, finns i [Team Data Science Process - med Azure HDInsight Hadoop-kluster i en datauppsättning som 1 TB](hive-criteo-walkthrough.md).

Du kan också använda en IPython notebook för att utföra uppgifter som visas i den här genomgången som använder 1 TB-datauppsättningen. Mer information finns i [Criteo genomgång med en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC Taxi kommunikation datauppsättning beskrivning
NYC Taxi resedata är cirka 20 GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48 GB okomprimerad). Det har mer än 173 miljoner enskilda kommunikation och inkluderar priser betalat för varje resa. Varje resa post innehåller upphämtning och dropoff plats och tid, avidentifierade hack (drivrutin) licensnummer och medallion nummer (den taxi unikt ID). Data omfattar alla kommunikation under året 2013 och anges i följande två datauppsättningar för varje månad:

- CSV-filer trip_data innehålla resans information. Detta inkluderar antalet passagerare, vill hämta och dropoff punkter, resans varaktighet och resans längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- CSV-filerna trip_fare innehåller information om avgiften betalat för varje resa. Detta inkluderar betalningstypen, avgiften belopp, tillägg och skatter, tips och vägtullar och det totala beloppet som betalas. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den unika nyckeln för att ansluta till resans\_data och resans\_avgiften består av fälten: medallion, hacka\_licens och upphämtning\_datetime. Om du vill hämta alla detaljer relevanta för en viss resa, räcker det att ansluta till med dessa tre nycklar.

## <a name="mltasks"></a>Exempel på uppgifter för förutsägelse
Avgör vilken typ av förutsägelser som du vill göra baserat på analys av data. På så sätt kan du tydliggöra uppgifter som du behöver ta med i processen. Här följer tre exempel på förutsägelse problem som vi bemöter i den här genomgången. De bygger på den *tips\_belopp*:

- **Binär klassificering**: förutsäga om ett tips har betalat för en resa. Det vill säga en *tips\_belopp* som är större än $0 är ett positivt exempel, medan en *tips\_belopp* $0 är ett exempel på negativt.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Multiklass-baserad klassificering**: förutse vilka tips belopp som har betalats för resan. Vi dela upp den *tips\_belopp* i fem klasser:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regression uppgift**: förutsäga mängden tips som har betalat för en resa.  

## <a name="setup"></a>Konfigurera ett HDInsight Hadoop-kluster för avancerad analys
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

Du kan ställa in en Azure-miljö för avancerade analyser som använder ett HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md): det här lagringskontot används för att lagra data i Azure Blob storage. De data som används i HDInsight-kluster finns även här.
2. [Anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics Process and Technology](customize-hadoop-cluster.md). Det här steget skapar ett HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerat på alla noder. Det finns två viktiga steg för att komma ihåg vid anpassning av ditt HDInsight-kluster.
   
   * Kom ihåg att länka storage-konto som skapades i steg 1 med ditt HDInsight-kluster när du skapar den. Det här lagringskontot har åtkomst till data som bearbetas i klustret.
   * Aktivera fjärråtkomst till huvudnoden i klustret när du har skapat klustret. Bläddra till den **Configuration** och sedan **Aktivera Remote**. Det här steget anger de autentiseringsuppgifter som används för fjärrinloggning.
3. [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md): du använder den här arbetsytan för att skapa machine learning-modeller. Den här uppgiften riktar när du har slutfört en första datagranskning och ned-sampling, med hjälp av HDInsight-kluster.

## <a name="getdata"></a>Hämta data från en offentlig källa
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

Kopiera den [NYC Taxi kommunikation](http://www.andresmh.com/nyctaxitrips/) datauppsättningen till din dator från dess offentliga plats att använda någon av metoderna som beskrivs i [flytta data till och från Azure Blob storage](move-azure-blob.md).

Här beskrivs hur du använder AzCopy för att överföra filer som innehåller data. Om du vill hämta och installera AzCopy, följer du anvisningarna [komma igång med kommandoradsverktyget azcopy](../../storage/common/storage-use-azcopy.md).

1. Från Kommandotolken, kör du följande AzCopy-kommandon och Ersätt *< path_to_data_folder >* med önskat mål:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. När kopieringen är klar visas totalt av 24 komprimerade filer i datamappen valt. Packa upp de hämtade filerna till samma katalog på den lokala datorn. Anteckna den mapp där de okomprimerade filerna finns. Den här mappen kallas den *< sökväg\_till\_unzipped_data\_filer\>*  i vilka sätt.

## <a name="upload"></a>Ladda upp data till standardbehållaren för HDInsight Hadoop-kluster
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

I följande AzCopy-kommandon och Ersätt följande parametrar med de faktiska värdena som du angav när du skapar Hadoop-kluster och packat upp datafilerna.

* ***< Path_to_data_folder >*** katalogen (tillsammans med sökväg) på datorn som innehåller de uppzippade datafilerna.  
* ***<storage account name of Hadoop cluster>*** Storage-konto som är associerade med ditt HDInsight-kluster.
* ***<default container of Hadoop cluster>*** Standardbehållaren som används av klustret. Observera att namnet på behållaren som standard är vanligtvis samma namn som själva klustret. Om klustret har anropats ”abc123.azurehdinsight.net” är standardbehållaren abc123.
* ***<storage account key>*** Nyckel för lagringskontot som används av ditt kluster.

Kör följande två AzCopy-kommandon från en kommandotolk eller ett Windows PowerShell-fönster.

Det här kommandot laddar upp resedata till den ***nyctaxitripraw*** katalogen i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Det här kommandot laddar upp avgiften data till den ***nyctaxifareraw*** katalogen i standardbehållaren för Hadoop-kluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data bör nu vara i Blob storage, och är redo att användas i HDInsight-klustret.

## <a name="#download-hql-files"></a>Logga in på huvudnoden för Hadoop-kluster och förbereda för undersökande analys
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

Om du vill få åtkomst till klustrets huvudnod klustret för undersökande analys och ned-sampling av data, följer du proceduren som beskrivs i [åtkomst till klustrets huvudnod Hadoop-kluster](customize-hadoop-cluster.md).

I den här genomgången använder vi främst frågor skrivna [Hive](https://hive.apache.org/), ett SQL-liknande frågespråk, utföra preliminär data explorations. Hive-frågor lagras i .hql filer. Vi sedan nedåtsampla dessa data som kan användas inom Machine Learning för att skapa modeller.

För att förbereda klustret för undersökande dataanalys, hämta .hql filer som innehåller de relevanta Hive-skript från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) till en lokal katalog (C:\temp) på huvudnoden. Att göra detta, öppna Kommandotolken från inom huvudnoden för klustret och köra följande två kommandon:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Dessa två kommandon ladda ned alla .hql filer som behövs i den här genomgången till den lokala katalogen ***C:\temp&#92;***  i huvudnoden.

## <a name="#hive-db-tables"></a>Skapa Hive-databasen och tabeller som partitioneras efter månad
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

Du är nu redo att skapa Hive-tabeller för NYC taxi datauppsättningen.
Öppna Hadoop-kommandoraden på skrivbordet för huvudnoden i klustrets huvudnod Hadoop-kluster. Ange Hive-katalogen genom att köra följande kommando:

    cd %hive_home%\bin

> [!NOTE]
> Kör alla Hive-kommandon i den här genomgången från Hive bin / directory-fråga. Det hanterar problem med sökväg automatiskt. Vi använder termer ”Hive directory prompten” ”, Hive bin / directory-fråga”, och ”Hadoop-kommandoraden” synonymt i den här genomgången.
> 
> 

Från Hive directory prompten, kör du följande kommando i Hadoop-kommandoraden för huvudnoden. Detta skickar Hive-fråga för att skapa Hive-databasen och tabeller:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Här är innehållet i den **C:\temp\sample\_hive\_skapa\_db\_och\_tables.hql** fil. Detta skapar Hive-databasen **nyctaxidb**, och tabellerna **resans** och **avgiften**.

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

* Den **resans** tabellen innehåller resans information om varje väg (Mer information, vill hämta tid, resa avståndet och gånger).
* Den **avgiften** tabellen innehåller information om avgiften (avgiften, tips belopp, vägtullar, och tilläggsavgifter).

Om du behöver ytterligare hjälp med de här procedurerna eller om du vill undersöka alternativ som finns i avsnittet [skicka Hive-frågor direkt från kommandoraden Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Läsa in data till Hive-tabeller med partitioner
> [!NOTE]
> Detta är vanligtvis en admin-uppgift.
> 
> 

NYC taxi datauppsättningen har en naturlig partitionerade efter månad, så vi använder för att aktivera snabbare bearbetningen och frågeprestanda. Följande PowerShell-kommandon (utfärdats från katalogen Hive med Hadoop-kommandoraden) läsa in data till resan och färdavgiften Hive-tabeller, partitioneras efter månad.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Den **exempel\_hive\_läsa in\_data\_av\_partitions.hql** filen innehåller följande **läsa in** kommandon:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Observera att ett antal Hive-frågor används här i processen för utforskning innebär tittar på bara en eller två partitioner. Men dessa frågor kan köras i hela datamängden.

### <a name="#show-db"></a>Visa databaser i HDInsight Hadoop-kluster
Om du vill visa de databaser som skapats i HDInsight Hadoop-kluster i Hadoop-Kommandotolken kör du följande kommando i Hadoop-kommandoraden:

    hive -e "show databases;"

### <a name="#show-tables"></a>Visa Hive-tabeller i den **nyctaxidb** databas
Att visa tabeller i den **nyctaxidb** databasen, kör du följande kommando i Hadoop-kommandoraden:

    hive -e "show tables in nyctaxidb;"

Vi kan bekräfta att partitioneras tabellerna genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.trip;"

Här är den förväntade utdatan:

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

På samma sätt kan vi se till att avgiften tabellen är partitionerad genom att köra följande kommando:

    hive -e "show partitions nyctaxidb.fare;"

Här är den förväntade utdatan:

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

## <a name="#explore-hive"></a>Datagranskning och de funktioner i Hive
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Du kan använda Hive-frågor för att utföra datagranskning och funktionen tekniska uppgifter för de data som lästs in till Hive-tabeller. Här följer exempel på sådana uppgifter:

* Visa de översta 10 posterna i båda tabellerna.
* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök datakvaliteten fält för longitud och latitud.
* Generera binära och inom klassificeringsetiketter baserat på datamängd som tips.
* Skapa funktioner med databehandling direkt resans avstånd.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Utforskning: Visa de översta 10 posterna i tabellen resa
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Granska 10 poster från varje tabell för att se hur data ser ut. Om du vill kontrollera posterna, kör du följande två frågor separat från Hive directory prompten i Hadoop-Kommandotolken.

Hämta de översta 10 posterna i resetabellen från den första månaden:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Hämta de översta 10 posterna i tabellen avgiften från den första månaden:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Du kan spara poster till en fil för bekväm visning. Små ändringar till den föregående frågan åstadkommer detta:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Utforskning: Visa antalet poster i var och en av de 12 partitionerna
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Är hur antalet resor varierar under kalenderåret av intresse. Gruppering efter månad visar fördelningen av kommunikation.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Det ger oss följande utdata:

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

Här är den första kolumnen är månaden och andra är antalet resor för den månaden.

Vi kan också räknas det totala antalet poster i vår datauppsättning resa genom att köra följande kommando i Kommandotolken för Hive-katalogen:

    hive -e "select count(*) from nyctaxidb.trip;"

Detta ger:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Vi kan utfärda Hive-frågor från Hive directory uppmanas att avgiften-datauppsättning för att verifiera antalet poster med kommandon som de som visas för resans datauppsättningen.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Det ger oss följande utdata:

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

Observera att exakt samma antal turer per månad returneras för båda datauppsättningar. Detta ger den första verifieringen att data har lästs in korrekt.

Du kan räkna det totala antalet poster i avgiften datauppsättning med hjälp av följande kommando från prompten för Hive-directory:

    hive -e "select count(*) from nyctaxidb.fare;"

Detta ger:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Det totala antalet poster i båda tabellerna är också desamma. Detta ger en andra verifiering att data har lästs in korrekt.

### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Resa distribution enligt medallion
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Det här exemplet identifierar medallions (taxi-nummer) som är större än 100 kommunikation inom en viss tidsperiod. Frågan dra nytta av partitionerade tabellåtkomst, eftersom det villkor av variabeln partition **månad**. Frågeresultatet skrivs till en lokal fil **queryoutput.tsv**i `C:\temp` på huvudnoden.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Här är innehållet i den **exempel\_hive\_resans\_antal\_av\_medallion.hql** filen för granskning.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion i NYC taxi-datauppsättningen identifierar en unik CAB-fil. Du kan identifiera vilka CAB-filer är förhållandevis upptagna genom att fråga vilka som gjorde mer än ett visst antal kommunikation i en viss tidsperiod. I följande exempel identifierar CAB-filer som gjort fler än hundra kommunikation i de första tre månader, och sparar frågeresultaten till en lokal fil **C:\temp\queryoutput.tsv**.

Här är innehållet i den **exempel\_hive\_resans\_antal\_av\_medallion.hql** filen för granskning.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Från Hive directory prompten, kör du följande kommando:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Utforskning: Resa distribution enligt medallion och hack licens
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

När du utforskar en datauppsättning, vill vi ofta undersöka numret på co-förekomsterna av grupper med värden. Det här avsnittet innehåller ett exempel på hur du gör detta för CAB-filer och drivrutiner.

Den **exempel\_hive\_resans\_antal\_av\_medallion\_license.hql** filgrupper avgiften datauppsättningen på **medallion** och **hack_license**, och returnerar antalet för varje kombination. Här följer innehållet:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Den här frågan returnerar kombinationer av CAB-filen och drivrutin, sorterade efter fallande antal och RETUR.

Från Hive directory prompten, kör du:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Frågeresultatet skrivs till en lokal fil **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Utforskning: Utvärdera datakvaliteten genom att söka efter ogiltig longitud och latitud poster
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Ett gemensamt mål av undersökande analys är att filtrera ut ogiltiga eller felaktiga poster. I exemplet i det här avsnittet anger om antingen longituden eller latituden fält som innehåller ett värde långt utanför området NYC. Eftersom det är troligt att dessa poster har en felaktig longitud-latitudvärdet som vi vill ta bort dem från alla data som ska användas för modellering.

Här är innehållet i **exempel\_hive\_kvalitet\_assessment.hql** filen för granskning.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Från Hive directory prompten, kör du:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Den *-S* argument som ingår i det här kommandot Undertrycker status skärmen utskriften Hive Map/Reduce-jobb. Detta är användbart eftersom det gör skärmen för att skriva ut av Hive-frågeresultatet lättare att läsa.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Utforskning: Binära klass distributioner av resans tips
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

För binär klassificering problemet som beskrivs i den [exempel på uppgifter för förutsägelse](hive-walkthrough.md#mltasks) avsnitt, det är bra att veta om ett tips har angetts eller inte. Den här distributionen av tips är binär:

* Tips angivna (klass 1, tips\_belopp > $0)  
* Inga tips (0-klass, tips\_belopp = $0)

Följande **exempel\_hive\_lutad\_frequencies.hql** filen gör detta:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Från Hive directory prompten, kör du:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Utforskning: Klass distributioner i inställningen för multiklass-baserad
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

För multiklass-baserad klassificering problemet som beskrivs i den [exempel på uppgifter för förutsägelse](hive-walkthrough.md#mltasks) avsnittet den här datauppsättningen också lämpar sig för en naturlig klassificering att förutsäga mängden tips angivna. Vi kan använda lagerplatser för att definiera tips intervall i frågan. Hämta klass-distributioner för de olika tips intervall med den **exempel\_hive\_tips\_intervallet\_frequencies.hql** fil. Här följer innehållet.

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

Kör följande kommando från Hadoop-Kommandotolken:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Utforskning: Compute direkt avståndet mellan två platser för longitud, latitud
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Du kanske vill veta om det är skillnad mellan direkt avståndet mellan två platser, och faktiska resans avståndet från taxi. En passagerare kan vara mindre troligt att tips om de ta reda på att drivrutinen har avsiktligt ägt dem med en längre väg.

Se en jämförelse mellan faktiska resans avståndet och [Haversine avståndet](http://en.wikipedia.org/wiki/Haversine_formula) mellan två longitud, latitud punkter (”” storcirkelavståndet), kan du använda trigonometrifunktioner som är tillgängliga i Hive:

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

I den föregående frågan R är radien för jorden i mil och pi konverteras till radianer. Observera att longitud, latitud punkter filtreras för att ta bort värden som är långt från området NYC.

I det här fallet vi skriva resultaten till en katalog med namnet **queryoutputdir**. Följande kommandosekvens först skapar den här katalogen och kör sedan kommandot Hive.

Från Hive directory prompten, kör du:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Frågeresultatet skrivs till nio Azure-blobar (**queryoutputdir/000000\_0** till **queryoutputdir/000008\_0**), under standardbehållaren för Hadoop-kluster.

Om du vill visa storleken på enskilda blobar, kör du följande kommando från prompten för Hive-katalogen:

    hdfs dfs -ls wasb:///queryoutputdir

Om du vill se innehållet i en viss fil, säger **000000\_0**, använda Hadoops `copyToLocal` kommando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` rekommenderas inte för användning med dem och kan vara mycket långsamt för stora filer.  
> 
> 

En stor fördel av informationen finns i en Azure-blob är att vi kan utforska data i Machine Learning, med hjälp av den [importdata] [ import-data] modulen.

## <a name="#downsample"></a>Nedåtsampla data och skapa modeller i Machine Learning
> [!NOTE]
> Detta är vanligtvis en data Science-aktivitet.
> 
> 

Efter analysfasen undersökande data är vi nu redo att nedåtsampla data för att skapa modeller i Machine Learning. I det här avsnittet visar vi hur du använder en Hive-fråga för att nedåtsampla data. Machine Learning sedan får åtkomst från den [importdata] [ import-data] modulen.

### <a name="down-sampling-the-data"></a>Ned-sampling data
Det finns två steg i den här proceduren. Först vi ansluta den **nyctaxidb.trip** och **nyctaxidb.fare** tabeller på tre nycklar som finns i alla poster: **medallion**, **hack\_ licens**, och **upphämtning\_datetime**. Vi sedan generera en binär klassificeringsetikett **lutad**, och en inom klassificeringsetikett **tips\_klass**.

För att kunna använda samplas ned data direkt från den [importdata] [ import-data] modul i Machine Learning, bör du lagra resultatet av den föregående frågan till en intern Hive-tabell. I det följer vi skapa en intern Hive-tabell och fylla i dess innehåll med domänanslutna och samplas ned data.

Frågan gäller standardfunktioner Hive direkt om du vill generera följande från den **upphämtning\_datetime** fält:
- timme på dagen
- vecka på året
- WEEKDAY (1 står för måndag och 7 står för söndag)

Frågan genererar även direct avståndet mellan platserna som vill hämta och dropoff. En fullständig lista över funktioner finns i [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Frågan sedan ned-samples data så att frågeresultatet passar in i Azure Machine Learning Studio. Normalt bara 1 procent av den ursprungliga datauppsättningen har importerats till studio.

Här följer innehållet i **exempel\_hive\_förbereda\_för\_aml\_full.hql** -fil som förbereder data för att skapa med Machine Learning-modellen:

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

Nu har vi en intern tabell **nyctaxidb.nyctaxi_downsampled_dataset**, vilka kan nås med hjälp av den [importdata] [ import-data] modul från Machine Learning. Dessutom kan vi använda den här datauppsättningen för att skapa Machine Learning-modeller.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Använda modulen importera Data i Machine Learning för att komma åt data samplas ned
Att skicka Hive-frågor i den [importdata] [ import-data] modulen av Machine Learning, du behöver åtkomst till en Machine Learning-arbetsytan. Du kan också ha tillgång till autentiseringsuppgifterna för klustret och dess associerade lagringskontot.

Här är lite information om den [importdata] [ import-data] modulen och parametrarna för att ange:

**HCatalog server URI**: Om klusternamnet är **abc123**, detta är helt enkelt: https://abc123.azurehdinsight.net.

**Hadoop-användarkontonamnet**: användarnamn som valts för klustret (inte fjärråtkomst användarnamn).

**Kontolösenord för Hadoop ser**: lösenordet du valde för klustret (inte fjärråtkomst lösenord).

**Platsen för utdata**: Detta är valt för att vara Azure.

**Azure storage-kontonamn**: namnet på standardkontot för lagring som är associerade med klustret.

**Azure behållarnamn**: Detta är standard behållarens namn för klustret och är vanligtvis samma som klusternamnet. För ett kluster som heter **abc123**, detta är abc123.

> [!IMPORTANT]
> Någon tabell som vi vill fråga med hjälp av den [importdata] [ import-data] modul i Machine Learning måste vara en intern tabell.
> 
> 

Så här avgör om en tabell **T** i en databas **D.db** är en intern tabell. Från Hive directory prompten, kör du följande kommando:

    hdfs dfs -ls wasb:///D.db/T

Om tabellen är en intern tabell och fylls det, måste innehållet visas här.

Ett annat sätt att avgöra om en tabell är en intern tabell är att använda Azure Storage Explorer. Du kan använda den för att navigera till standardnamnet för behållare i klustret och sedan filtrera efter tabellnamnet. Detta bekräftar att det är en intern tabell om tabellen och dess innehåll visas.

Här visas en skärmbild av Hive-frågan och [importdata] [ import-data] modulen:

![Skärmbild av Hive-fråga för modulen importera Data](./media/hive-walkthrough/1eTYf52.png)

Eftersom vår samplas ned data finns i standardbehållaren, är den resulterande Hive-frågan från Machine Learning väldigt enkelt. Det är bara en **Välj * från nyctaxidb.nyctaxi\_upplösning\_data**.

Datauppsättningen kan nu användas som utgångspunkt för att skapa Machine Learning-modeller.

### <a name="mlmodel"></a>Skapa modeller i Machine Learning
Du kan nu fortsätta till modellskapandet och distribution av modeller i [Maskininlärning](https://studio.azureml.net). Data är klar så att vi kan använda i adressering förutsägelse-problem som identifierats tidigare:

- **Binär klassificering**: för att förutsäga om ett tips har betalat för en resa.

  **Learner används:** tvåklassförhöjt logistic regression

  a. Detta mål (eller klassen) etiketten är **lutad**. Den ursprungliga datauppsättningen för ned-samplas har några kolumner som är mål läckage av det här experimentet klassificering. I synnerhet **tips\_klass**, **tips\_belopp**, och **totala\_belopp** avslöjar information om mål-etiketten som är inte tillgänglig vid testning tid. Vi ta bort dessa kolumner från behandling med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modulen.

  Följande diagram visar våra experiment att förutsäga om ett tips har betalat för en viss resa:

  ![Diagram över experiment](./media/hive-walkthrough/QGxRz5A.png)

  b. Det här experimentet har våra mål etikett distributioner ungefär 1:1.

   Följande diagram visar fördelningen av tips klass etiketter för binära klassifikationsproblem:

  ![Diagram över distributionen av tips klass etiketter](./media/hive-walkthrough/9mM4jlD.png)

    Därför kan hämta vi ett område under kurvan (AUC) för 0.987, enligt följande bild:

  ![Diagram över AUC värde](./media/hive-walkthrough/8JDT0F8.png)

- **Multiklass-baserad klassificering**: att förutsäga vilka tips betalat för resan, med hjälp av de tidigare definierade klasserna.

  **Learner används:** inom logistic regression

  a. För det här problemet är våra mål (eller klassen) etikett **tips\_klass**, vilket kan ta en av fem värden (0,1,2,3,4). Som i fallet med binär klassificering har vi några kolumner som är mål läckage av det här experimentet. I synnerhet **lutad**, **tips\_belopp**, och **totala\_belopp** avslöja information om det mål som inte är tillgänglig Testa tid. Vi ta bort de här kolumnerna med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modulen.

  Följande diagram visar experiment att förutsäga ett tips är sannolikt att hamna i vilka bin. Lagerplatserna är: klass 0: tips = $0, klass 1: tips > $0 och tips < = 5 dollar, klass 2: tips > 5 och tips < = 10 USD, klass 3: tips > $10 och tips < = $20 och klass 4: tips > 20.

  ![Diagram över experiment](./media/hive-walkthrough/5ztv0n0.png)

  Nu visar vi hur faktiska test klass distribution ser ut. Klass 0 och klass 1 är vanliga och andra klasser är ovanliga.

  ![Diagram över distribution av test-klass](./media/hive-walkthrough/Vy1FUKa.png)

  b. Det här experimentet använder vi en felmatris och titta på förutsägelse noggrannhet. Detta visas här:

  ![Felmatris](./media/hive-walkthrough/cxFmErM.png)

  Observera att klassen noggrannhet på vanliga klasser är ganska bra, modellen inte gör ett bra jobb med ”learning” på de sällsynta klasserna.

- **Regression uppgift**: att förutsäga mängden tips som har betalat för en resa.

  **Learner används:** Boosted beslutsträd

  a. Detta mål (eller klassen) etiketten är **tips\_belopp**. Mål-läckage i det här fallet är: **lutad**, **tips\_klass**, och **totala\_belopp**. Dessa variabler avslöja information om hur mycket av tips som normalt inte är tillgängligt vid testning tid. Vi ta bort de här kolumnerna med hjälp av den [Välj kolumner i datauppsättning] [ select-columns] modulen.

  Följande diagram visar experiment till att förutse hur mycket av det angivna tipset:

  ![Diagram över experiment](./media/hive-walkthrough/11TZWgV.png)

  b. Regression problem mäter vi noggrannhet för förutsägelsen genom att titta på kvadratfel i förutsägelserna och bestämningskoefficient:

  ![Skärmbild av förutsägelse statistik](./media/hive-walkthrough/Jat9mrz.png)

  Här förklaras har gjorts är 0.709, innebär som cirka 71 procent av variansen med modellen koefficienter.

> [!IMPORTANT]
> Läs mer om Machine Learning och hur du använder den i [vad är Machine Learning](../studio/what-is-machine-learning.md). Dessutom kan den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/) omfattar en omfattar experiment och ger en omfattande introduktion till antalet funktioner av Machine Learning.
> 
> 

## <a name="license-information"></a>Licensinformation
Den här exempelgenomgång och dess tillhörande skript som delas av Microsoft under MIT-licensen. Mer information finns i den **LICENSE.txt** filen i katalogen för exempelkoden på GitHub.

## <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi kommunikation hämtningssidan](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxitransport Resedata av Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionen forskning och statistik](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



