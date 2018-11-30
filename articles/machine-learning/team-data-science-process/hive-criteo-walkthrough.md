---
title: Team Data Science Process fungerar – med hjälp av ett Azure HDInsight Hadoop-kluster på en datauppsättning som 1 TB | Microsoft Docs
description: Med Team Data Science Process för ett scenario för slutpunkt till slutpunkt med ett HDInsight Hadoop-kluster för att skapa och distribuera en modell med hjälp av en stor (1 TB) offentligt tillgängliga datauppsättning
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
ms.openlocfilehash: 3aef1b85a462eea74fbe977e9a48054f11acf47a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447052"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Team Data Science Process fungerar – med hjälp av ett Azure HDInsight Hadoop-kluster på en datauppsättning som 1 TB

Den här genomgången visar hur du använder Team Data Science Process i ett scenario för slutpunkt till slutpunkt med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktionen tekniker och ned exempeldata från en offentligt tillgänglig [ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datauppsättningar. Azure Machine Learning används för att skapa en binär klassificeringsmodell för dessa data. Den visar också hur du publicerar en av dessa modeller som en webbtjänst.

Det är också möjligt att använda en IPython notebook för att utföra uppgifter som visas i den här genomgången. Användare som vill testa den här metoden bör kontakta de [Criteo genomgång med en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) avsnittet.

## <a name="dataset"></a>Criteo datauppsättning beskrivning
Criteo data är en Klicka förutsägelse datauppsättning som är cirka 370GB gzip-komprimerade TSV-filer (~1.3TB okomprimerade), som består av mer än 4.3 miljarder poster. Det hämtas från 24 dagars klickar du på data som har gjorts tillgängliga av [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). För att underlätta för datatekniker har data som är tillgängliga för oss att experimentera med uppzippade.

Varje post i den här datauppsättningen innehåller 40 kolumner:

* den första kolumnen är en etikett-kolumn som anger om en användare klickar på en **lägga till** (värdet 1) eller inte på en (värdet 0)
* bredvid 13 kolumner är numeriska, och
* senaste 26 är kategoriska kolumner

Kolumnerna är anonyma och använda en serie uppräknade namn: ”Kol1” (för etikettkolumnen) till ”Col40” (för den sista kategoriska kolumnen).            

Här är ett utdrag ur de första 20 kolumnerna i två observationer (rader) från den här datauppsättningen:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Det finns värden som saknas i både den numeriska och kategoriska kolumnerna i den här datauppsättningen. En enkel metod för att hantera dessa värden beskrivs. Ytterligare information om data prestandahantering när du sparar dem till Hive-tabeller.

**Definition:** *klickningar (CTR):* detta är procentandelen av klick i data. I den här Criteo datauppsättningen är CTR cirka 3.3% eller 0.033.

## <a name="mltasks"></a>Exempel på uppgifter för förutsägelse
Två exempel förutsägelse problem åtgärdas i den här genomgången:

1. **Binär klassificering**: förutsäger om en användare klickar på en Lägg till:
   
   * Klass 0: Ingen Klicka
   * Klass 1: Klicka på
2. **Regression**: förutsäger sannolikheten för en ad klickar du på från funktioner.

## <a name="setup"></a>Ange ett HDInsight Hadoop-kluster för datavetenskap
**Obs:** detta är vanligtvis en **Admin** uppgift.

Konfigurera din Azure Data Science-miljö för att skapa lösningar för förutsägelseanalys med HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md): det här lagringskontot används för att lagra data i Azure Blob Storage. Här lagras data som används i HDInsight-kluster.
2. [Anpassa Azure HDInsight Hadoop-kluster för Data Science](customize-hadoop-cluster.md): det här steget skapar ett Azure HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerat på alla noder. Det finns två viktiga steg (som beskrivs i det här avsnittet) för att slutföra när du anpassar HDInsight-klustret.
   
   * Du måste koppla storage-konto som skapades i steg 1 med ditt HDInsight-kluster när den skapas. Det här lagringskontot används för att komma åt data som kan bearbetas i klustret.
   * Du måste aktivera fjärråtkomst till huvudnoden i klustret när den har skapats. Kom ihåg fjärråtkomst-autentiseringsuppgifter som du anger här (skiljer sig från de som angetts för klustret när skapades): du behöver dem för att slutföra följande procedurer.
3. [Skapa en arbetsyta för Azure ML](../studio/create-workspace.md): den här Azure Machine Learning-arbetsyta används för att skapa machine learning-modeller efter en inledande datagranskning och ned sampling på HDInsight-kluster.

## <a name="getdata"></a>Hämta och använda data från en offentlig källa
Den [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datauppsättning kan nås genom att klicka på länken, godkänna användningsvillkoren och att ange ett namn. En ögonblicksbild av det här ser det ut visas här:

![Acceptera villkoren för Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klicka på **Fortsätt om du vill ladda ned** läsa mer om datauppsättningen och dess tillgänglighet.

Data som finns i en offentlig [Azure blobblagring](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) plats: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. ”Wasb” refererar till Azure Blob Storage-plats. 

1. Data i den här offentliga blob-storage består av tre undermappar uppzippade data.
   
   1. Undermappen *raw-beräkning / /* innehåller de första 21 dagarna data – från dag\_00 dag\_20
   2. Undermappen *raw-tåg/* består av en dag och dag\_21
   3. Undermappen *raw/test/* består av två dagars data, dag\_22 och dag\_23
2. För kunder som vill börja med råa gzip-data, dessa är också tillgängliga i mappen huvudsakliga *raw /* som day_NN.gz, där NN går från 00 och 23.

En annan metod för att komma åt, utforska och modellen som dessa data som inte kräver någon lokal nedladdningar beskrivs senare i den här genomgången när vi skapar Hive-tabeller.

## <a name="login"></a>Logga in till klustrets huvudnod
Logga in till huvudnod i klustret genom att använda den [Azure-portalen](https://ms.portal.azure.com) att hitta klustret. Klicka på ikonen HDInsight Elefant till vänster och sedan dubbelklicka på namnet på klustret. Navigera till den **Configuration** fliken, dubbelklicka på anslutningsikonen längst ned på sidan och ange dina autentiseringsuppgifter för fjärråtkomst när du tillfrågas. Då kommer du till huvudnod i klustret.

Här är en typisk första inloggningen till klustrets huvudnod ser det ut:

![Logga in i klustret](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Till vänster är ”Hadoop kommandoraden”, som vårt bestämmer hög grad för att utforska data. Lägg märke till två användbara URL: er – ”Hadoop Yarn Status” och ”Hadoop namn nod”. URL: en för yarn status visas jobbförlopp och den nod URL: ger information om klusterkonfigurationen.

Nu du ställs in och är redo för att börja första delen av den här genomgången: datautforskning med Hive och förbereder data för Azure Machine Learning.

## <a name="hive-db-tables"></a> Skapa Hive-databasen och tabeller
För att skapa Hive-tabeller för vår Criteo datauppsättningen, öppna den ***Hadoop-kommandoraden*** på skrivbordet för huvudnoden, och ange den Hive-katalogen genom att ange kommandot

    cd %hive_home%\bin

> [!NOTE]
> Kör alla Hive-kommandon i den här genomgången från Hive bin / directory-fråga. Detta hand tar om eventuella problem med sökväg automatiskt. Du kan använda villkoren ”Hive directory prompten” ”, Hive bin / directory-fråga”, och ”Hadoop-kommandoraden” synonymt.
> 
> [!NOTE]
> Om du vill köra en Hive-fråga, kan en alltid använda följande kommandon:
> 
> 

        cd %hive_home%\bin
        hive

När registreringsdatafilen REPL visas med en ”hive >” Logga, bara klipp och klistra in frågan för att köra den.

Följande kod skapar en databas ”criteo” och sedan genererar 4 tabeller:

* en *tabell för generering av antalet* bygger på dagar dag\_00 dag\_20,
* en *tabell som ska användas som träna datamängd* bygger på dag\_21, och
* två *tabeller som test-datauppsättningar* bygger på dag\_22 och dag\_23 respektive.

Dela upp testdatauppsättningen i två olika tabeller eftersom en av dagarna är helgdagar. Målet är att avgöra om modellen kan identifiera skillnader mellan en helgdag och icke-helgdagar från klicka igenom frekvensen.

Skriptet [exempel&#95;hive&#95;skapa&#95;criteo&#95;databasen&#95;och&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) visas här för att underlätta:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Dessa tabeller är externa så att du bara kan peka till deras platser i Azure Blob Storage (wasb).

**Det finns två sätt att köra alla Hive-fråga:**

1. **Med hjälp av kommandoradsverktyget registreringsdatafilen REPL**: först är att utfärda ett kommando ”hive” och kopiera och klistra in en fråga på registreringsdatafilen REPL kommandoraden. Om du vill göra detta, gör du:
   
        cd %hive_home%\bin
        hive
   
     Nu i REPL kommandoradsverktyget kör klippa och klistra in frågan den.
2. **Spara frågor till en fil och kommandot**: andra är att spara frågorna till en .hql-fil ([exempel&#95;hive&#95;skapa&#95;criteo&#95;databasen&#95;och&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) och sedan kör du följande kommando för att köra frågan:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Bekräfta att skapa en databas och tabell
Bekräfta sedan att skapa databasen med följande kommando från Hive bin / directory prompten:

        hive -e "show databases;"

Detta ger:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Detta bekräftar att skapa den nya databasen ”criteo”.

Om du vill se vilka tabeller som har skapats, bara utfärda kommandot här från Hive bin / directory prompten:

        hive -e "show tables in criteo;"

Du bör se följande utdata:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Datagranskning i Hive
Nu är du redo att göra vissa grundläggande datagranskning i Hive. Du börja med att räkna antalet av exemplen i träna och testa datatabeller.

### <a name="number-of-train-examples"></a>Antal train-exempel
Innehållet i [exempel&#95;hive&#95;antal&#95;träna&#95;tabell&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) visas här:

        SELECT COUNT(*) FROM criteo.criteo_train;

Detta ger:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternativt kan en även kör du följande kommando från Hive bin / directory prompten:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Antalet test exemplen i de två datauppsättningarna test
Räkna antalet exemplen i de två test-datauppsättningarna. Innehållet i [exempel&#95;hive&#95;antal&#95;criteo&#95;testa&#95;dag&#95;22&#95;tabell&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) finns här:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Detta ger:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Som vanligt, som du kan även anropa skriptet från Hive bin / directory fråga genom att utfärda kommandot:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Slutligen kan du kontrollera att antalet test exemplen i testdata baserat på dag\_23.

Kommandot för att göra detta liknar den som bara visas (se [exempel&#95;hive&#95;antal&#95;criteo&#95;testa&#95;dag&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Detta ger:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribution av etiketter i train-datauppsättning
Distribution av etiketter i träna datauppsättningen är av intresse. Prova själv genom att visa innehållet i [exempel&#95;hive&#95;criteo&#95;etikett&#95;distribution&#95;träna&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Detta ger distribution av etiketter:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Observera att procentandelen positivt etiketter är cirka 3.3% (konsekvent med den ursprungliga datauppsättningen).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distributioner av vissa numeriska variablerna i datauppsättningen train
Du kan använda Hives intern ”histogram\_numeriska” funktionen för att ta reda på hur distributionen av numeriska variabler som ser ut. Här följer innehållet i [exempel&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Detta ger följande:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

LATERALA visa - explode kombination i Hive används för att en SQL-liknande resultat i stället för vanliga listan. Observera att i den här tabellen, den första kolumnen motsvarar bin-center och andra till bin-frekvens.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Ungefärlig percentilerna för vissa numeriska variablerna i datauppsättningen train
Är också beräkning av ungefärlig percentiler med numeriska variabler. Hive är interna ”: e percentilen\_ungefärlig” gör detta för oss. Innehållet i [exempel&#95;hive&#95;criteo&#95;ungefärliga&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) är:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Detta ger:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Distributionen av percentiler är nära förknippat med histogrammet distributionen av alla numeriska variabler vanligtvis.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Hitta antalet unika värden för vissa kategoriska kolumner i datauppsättningen train
Du kan fortsätta att utforska data, hitta, för vissa kategoriska kolumner, antalet unika värden som de tar. Gör detta genom att visa innehållet i [exempel&#95;hive&#95;criteo&#95;unika&#95;värden&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Detta ger:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Observera att Col15 har unika värden för 19M! Med hjälp av teknik för naïve som ”en-hot kodning” är för att koda sådan hög-dimensionell kategoriska variabler inte är möjligt. Framför allt, en kraftfull, robust teknik kallas [Learning med räknar](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) för stöter på problemet effektivt är beskrivs och visas.

Slutligen ska du titta på antalet unika värden för vissa andra kategoriska kolumner samt. Innehållet i [exempel&#95;hive&#95;criteo&#95;unika&#95;värden&#95;flera&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) är:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Detta ger:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Observera återigen att alla andra kolumner utom Col20, har många unika värden.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Delad förekomsten räknar över par av kategoriska variablerna i datauppsättningen train

Delad förekomsten antalet par av kategoriska variabler är också av intresse. Detta kan fastställas med hjälp av koden i [exempel&#95;hive&#95;criteo&#95;parad&#95;kategoriska&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Omvänd ordna antalen efter deras förekomst och titta på överst 15 i det här fallet. Det ger oss:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Ned exemplet datauppsättningar för Azure Machine Learning
Med utforskat datauppsättningarna och lärt dig att göra den här typen av utforskning för alla variabler (inklusive kombinationer), ned exemplet datamängderna så att modeller i Azure Machine Learning kan byggas. Kom ihåg att fokuserar på problemet: med en uppsättning exempel attribut (funktionen värden från Col2 - Col40), förutsäga om Kol1 är 0 (ingen klicka) eller 1 (klicka).

Att använda Hives interna RAND() funktionen ned exemplet tåget och test datauppsättningar till 1% av den ursprungliga storleken. Skriptet nästa [exempel&#95;hive&#95;criteo&#95;nedsampla&#95;träna&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) gör detta för träna datauppsättningen:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Detta ger:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Skriptet [exempel&#95;hive&#95;criteo&#95;nedsampla&#95;testa&#95;dag&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) gör det åt testdata, dag\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Detta ger:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Slutligen skriptet [exempel&#95;hive&#95;criteo&#95;nedsampla&#95;testa&#95;dag&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) gör det åt testdata, dag\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Detta ger:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Det här gör är du redo att använda vår på provade träna och testa datauppsättningar för att skapa modeller i Azure Machine Learning.

Det finns en sista viktig komponent innan du fortsätter till Azure Machine Learning, som gäller tabellen count. I nästa underavsnitt beskrivs antal tabellen i detalj.

## <a name="count"></a> En kort beskrivning i tabellen count
Som du såg kan ha flera kategoriska variabler en mycket hög dimensionalitet. I den här genomgången, en kraftfull teknik som heter [Learning med räknar](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) för att koda dessa variabler i en effektiv, robust sätt visas. Mer information om den här tekniken är i länken.

[!NOTE]
>I den här genomgången ligger fokus på att använda antal tabeller för att producera compact framställningar av hög-dimensionell kategoriska funktioner. Detta är inte det enda sättet att koda kategoriska funktioner Mer information om andra metoder berörda användare kan ta en titt [en-hot encoding](http://en.wikipedia.org/wiki/One-hot) och [funktions-hashning](http://en.wikipedia.org/wiki/Feature_hashing).
>

Använda data för att skapa antal tabeller på antal data, i mappen raw/antal. I avsnittet modellering användare visas hur du skapar dessa antal tabeller för kategoriska funktioner från grunden, eller du kan också använda en tabell med färdiga antal för sina explorations. I vilka följer, när ”färdiga antal tabeller” avses till, menar vi med antalet tabeller som har angetts. Detaljerade anvisningar om hur du kommer åt dessa tabeller finns i nästa avsnitt.

## <a name="aml"></a> Skapa en modell med Azure Machine Learning
Vår modell processen i Azure Machine Learning för att bygga följer de här stegen:

1. [Hämta data från Hive-tabeller i Azure Machine Learning](#step1)
2. [Skapa experimentet: rensa data och förtränade med antal tabeller](#step2)
3. [Skapa, träna och betygsätta modellen](#step3)
4. [Utvärdera modellen](#step4)
5. [Publicera modellen som en webbtjänst](#step5)

Nu är du redo att skapa modeller i Azure Machine Learning studio. Våra nedåt samplade data sparas som Hive-tabeller i klustret. Använda Azure Machine Learning **importdata** modulen att läsa dessa data. Autentiseringsuppgifter för åtkomst till lagringskontot för det här klustret finns i det som följer.

### <a name="step1"></a> Steg 1: Hämta data från Hive-tabeller i Azure Machine Learning med hjälp av modulen importera Data och välj den för en machine learning-experiment
Starta genom att välja en **+ ny** -> **EXPERIMENT** -> **tomt Experiment**. Från den **Search** längst ned till vänster, Sök efter ”importera Data”-rutan. Dra och släpp den **importdata** modulen in experimentet arbetsytan (den mellersta delen av skärmen) till att använda-modulen för dataåtkomst.

Det här är vad den **importdata** ser ut ungefär som när du hämtar data från Hive-tabellen:

![Importera Data hämtar data](./media/hive-criteo-walkthrough/i3zRaoj.png)

För den **importdata** modulen, värdena för parametrarna som anges i bilden är bara exempel på typ av värden som du måste ange. Här är några allmänna riktlinjer för hur du fyller i parameteruppsättning för den **importdata** modulen.

1. Välj ”Hive-fråga” för **datakälla**
2. I den **Hive databasfråga** , en enkel, MARKERAR du kryssrutan * FROM < din\_databasen\_name.your\_tabell\_namn >-räcker.
3. **Hcatalog server URI**: om klustret är ”abc”, så det är bara: https://abc.azurehdinsight.net
4. **Hadoop-användarkontonamnet**: användarnamn som valts vid tidpunkten för idriftsättning klustret. (Inte fjärråtkomst användarnamnet!)
5. **Hadoop lösenord**: lösenordet för användarnamnet som valts vid tidpunkten för idriftsättning klustret. (Inte fjärråtkomst lösenordet!)
6. **Platsen för utdata**: Välj ”Azure”
7. **Azure storage-kontonamn**: lagringskontot som associerats med klustret
8. **Azure storage-kontonyckel**: nyckeln för lagringskontot som är associerade med klustret.
9. **Azure behållarnamn**: Om klusternamnet är ”abc”, så det är helt enkelt ”abc”, vanligtvis.

När den **importdata** har slutförts hämtar data (du se grön skalstreck i modulen), spara dessa data som en datauppsättning (med ett valfritt namn). Hur detta ser ut:

![Importera Data spara data](./media/hive-criteo-walkthrough/oxM73Np.png)

Högerklicka på utdataporten för den **importdata** modulen. Nu visas en **Spara som datamängd** alternativet och en **visualisera** alternativet. Den **visualisera** alternativet om du visar 100 rader med data, tillsammans med en höger panel som är användbar för vissa sammanfattande statistik. Om du vill spara data helt enkelt välja **Spara som datamängd** och följ anvisningarna.

Välj sparade datauppsättningen för användning i en machine learning-experiment genom att leta upp datauppsättningar med hjälp av den **Search** rutan som visas i följande bild. Sedan skriver ut namnet du gav datauppsättningen delvis vill komma åt den och dra datauppsättningen till åtgärdsfönstervärdens. Släppa den på åtgärdsfönstervärdens väljer den för användning i machine learning modellering.

![Drage datauppsättning till panelen huvudsakliga](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Gör detta för både tåget och test-datauppsättningar. Kom också ihåg att använda databasens namn och tabellnamn som du gav för detta ändamål. De värden som används i bilden är enbart för illustration purposes.* *
> 
> 

### <a name="step2"></a> Steg 2: Skapa ett enkelt experiment i Azure Machine Learning för att förutsäga klick / inga klick
Vår Azure ML-experiment ser ut så här:

![Machine Learning-experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Nu ska du granska de viktigaste komponenterna i det här experimentet. Dra vår sparade träna och testa datauppsättningar in på vår experimentets arbetsyta först.

#### <a name="clean-missing-data"></a>Rensa Data som saknas
Den **Rensa Data som saknas** modulen har namnet antyder: saknade data på ett sätt som kan vara användardefinierade rensas. Titta på den här modulen kan se den här:

![Rensa data som saknas](./media/hive-criteo-walkthrough/0ycXod6.png)

Här kan valde att ersätta alla saknade värden med 0. Det finns andra alternativ, vilket kan ses genom att titta på de nedrullningsbara listorna i modulen.

#### <a name="feature-engineering-on-the-data"></a>Funktionstekniker på data
Det kan finnas flera miljoner unika värden för vissa kategoriska funktioner för stora datauppsättningar. Det är helt och hållet inte lämpligt att använda naïve metoder, till exempel en frekvent kodning för att representera hög-dimensionell kategoriska funktioner. Den här genomgången visar hur du använder antal funktioner med inbyggda Azure Machine Learning-moduler för att generera compact framställningar av dessa hög-dimensionell kategoriska variabler. Såklart resultatet är en mindre Modellstorlek på, snabbare till utbildning och prestandamått som är helt jämförbar med hjälp av andra tekniker.

##### <a name="building-counting-transforms"></a>Att skapa räkna omvandlar
Om du vill skapa antal funktioner, använda den **skapa räkna transformera** modul som är tillgängliga i Azure Machine Learning. Modulen ser ut så här:

![Skapa räkna transformera modul](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![skapa räkna transformera modul](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> I den **antal kolumner** anger du de kolumner som du vill utföra antalet på. Dessa är oftast (som tidigare nämnts) hög-dimensionell kategoriska kolumner. Kom ihåg att Criteo datauppsättningen har 26 kategoriska kolumner: från Col15 till Col40. Här kan räkna med alla och ge sina index (från 15 till 40 avgränsade med kommatecken enligt).
> 

Använda modul i MapReduce-läge (lämplig för stora datauppsättningar), du behöver åtkomst till ett HDInsight Hadoop-kluster (den som används för funktionen utforskning kan återanvändas för detta ändamål samt) och dess autentiseringsuppgifter. Föregående bild illustrerar vilka ifyllda värdena ut (Ersätt de angivna värdena för bilden med de som är relevanta för din egen användningsfallet).

![Modulparametrar](./media/hive-criteo-walkthrough/05IqySf.png)

Bilden ovan visar hur du ange platsen för indata-blob. Den här platsen har data som är reserverade för att skapa antal tabeller på.

När den här modulen är klar sparar transformeringen för senare genom att högerklicka på modulen och välja den **Spara som transformeringen** alternativet:

![Alternativet ”Spara som transformeringen”](./media/hive-criteo-walkthrough/IcVgvHR.png)

I vår experiment arkitektur som visas ovan, motsvarar datauppsättningen ”ytransform2” exakt en sparad antal transformering. Under resten av det här experimentet förutsätts att läsaren används en **skapa räkna transformera** modulen på vissa data för att generera antalet och kan sedan använda dessa antal för att generera antal funktioner på träna och testa datauppsättningar.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Välja vilka antal funktioner för att ingå som en del av träna och testa datauppsättningar
När ett antal transformeringen är klart, kan användaren välja vilka funktioner du vill inkludera i sina träna och testa datauppsättningar med hjälp av den **ändra parametrarna för antal tabell** modulen. Den här modulen visas här för fullständighetens skull. Men i enkelhetens inte faktiskt använder den i vår experiment.

![Ändra antalet tabell](./media/hive-criteo-walkthrough/PfCHkVg.png)

I det här fallet log-oddsen är att användas som kan ses och backoff-kolumnen ignoreras. Du kan också ange parametrar som till exempel skräpinsamling bin tröskelvärdet, hur många pseudo tidigare exempel att lägga till för Utjämning och om du vill använda alla Laplacian brus eller inte. Alla dessa avancerade funktioner och det är att märka att standardvärdena är en bra utgångspunkt för användare som inte har använt den här typen av funktionen generation.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformering av data innan du genererar antal funktioner
Nu fokus ligger på ett viktigt peka om att transformera vår träna och testa data innan du genererar faktiskt antal funktioner. Observera att det finns två **kör R-skript** moduler som används innan antal transformationen tillämpas på våra data.

![Köra R-skript-moduler](./media/hive-criteo-walkthrough/aF59wbc.png)

Här är första R-skriptet:

![Första R-skript](./media/hive-criteo-walkthrough/3hkIoMx.png)

Det här R-skriptet byter namn på våra kolumner till namn ”Kol1” till ”Col40”. Det beror på att antalet transformering förväntar sig att namnen på det här formatet.

Andra R-skriptet balanserar fördelningen mellan positiva och negativa klasser (klasserna 1 och 0 respektive) av ned sampling klassen negativt. R-skriptet här visar hur du gör detta:

![Andra R-skript](./media/hive-criteo-walkthrough/91wvcwN.png)

I den här enkla R-skript i ”pos\_neg\_förhållande” används för att ställa in hur lång balans mellan vilka positiva och negativa klasser. Detta är viktigt att utföra eftersom förbättras klass obalans har vanligtvis prestandafördelarna för klassificering problem där klass-distribution är förvrängd (återkallande att då du har 3.3% positivt klassen och 96.7% negativt klassen).

##### <a name="applying-the-count-transformation-on-our-data"></a>Tillämpa antal omvandlingen på våra data
Slutligen kan du använda den **gäller omvandling** modul för att tillämpa count-transformeringar på vår träna och testa datauppsättningar. Den här modulen tar sparade count-transformeringen som en inmatning och träna eller test datauppsättningar som andra indata och returnerar data med antal funktioner. Den visas här:

![Tillämpa omvandling modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Ett utdrag av vilka antalet funktioner se ut som följande:
Det vara nyttigt att se hur antalet funktioner som ser ut i vårt fall. Här är ett utdrag ur detta:

![Antal funktioner](./media/hive-criteo-walkthrough/FO1nNfw.png)

Den här utdrag visar att för kolumner som räknas måste du hämta antalen och logga oddsen utöver eventuella relevanta backoffs.

Du är nu redo att skapa en Azure Machine Learning-modell med hjälp av dessa transformerade datauppsättningar. I nästa visar avsnitt hur detta kan göras.

### <a name="step3"></a> Steg 3: Skapa, träna och betygsätta modellen

#### <a name="choice-of-learner"></a>Val av learner
Först måste du välja en learner. Använd ett tvåklassförhöjt beslutsträd som våra learner. Här följer sedan standardalternativen för det här learner:

![Tvåklassförhöjt beslutsträd parametrar](./media/hive-criteo-walkthrough/bH3ST2z.png)

Välj standardvärden i experimentet. Observera att standardvärdena är vanligtvis meningsfulla och ett bra sätt att få snabb baslinjer för prestanda. Du kan förbättra prestandan av oinskränkt parametrar om du väljer att när du har en baslinje.

#### <a name="train-the-model"></a>Träna modellen
För utbildning, bara anropa en **Träningsmodell** modulen. De två indatavärdena till den är Tvåklassförhöjt beslutsträd learner och vår train-datauppsättning. Detta visas här:

![Train-modell](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Poängsätt modellen
När du har en tränad modell, är du redo att bedöma mot testdatauppsättningen och utvärdera dess prestanda. Gör detta med hjälp av den **Poängmodell** modulen som visas i följande bild, tillsammans med en **utvärdera modell** modulen:

![Modulen Poängsätta modell](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Steg 4: Utvärdera modellen
Slutligen bör du analysera modellprestanda. Vanligtvis är ett bra mått för två klass (binära) klassificering problem i AUC. Om du vill visualisera detta kopplar in den **Poängmodell** modul till en **utvärdera modell** -modulen för detta. Klicka på **visualisera** på den **utvärdera modell** modulen ger en bild som det nedanstående:

![Utvärdera modulen BDT modell](./media/hive-criteo-walkthrough/0Tl0cdg.png)

I binary (eller två klass) klassificering, problem, ett bra mått på förutsägelsefunktionen är det området Under kurvan (AUC). I följande avsnitt beskrivs våra resultat med hjälp av den här modellen på vår test-datauppsättning. Om du vill ha detta högerklickar du på utdataporten för den **utvärdera modell** modulen och sedan **visualisera**.

![Visualisera utvärdera modell](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Steg 5: Publicera modellen som en webbtjänst
Möjligheten att publicera en Azure Machine Learning-modell som webbtjänster med minsta möjliga ansträngning är en viktig funktion för att göra den allmänt tillgängliga. När det är klart kan vem som helst göra anrop till webbtjänsten med indata att de behöver förutsägelser för och webbtjänsten använder modellen för att returnera dessa förutsägelser.

Om du vill göra detta måste du först spara vår tränade modellen som en Trained Model-objektet. Det gör du genom att högerklicka på den **Träningsmodell** modulen och använder den **Spara som Trained Model** alternativet.

Därefter skapa indata och utdata portar för vår webbtjänst:

* en port hämtar data i samma formulär som de data som du behöver förutsägelser för
* en utgångsport returnerar poängsatta etiketter och motsvarande sannolikheter.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Välj några rader med data för inkommande port
Det är lämpligt att använda en **gäller SQL omvandling** modul du ska välja bara 10 raderna som fungerar som indataporten data. Välj bara dessa rader med data för våra indataport med hjälp av SQL-fråga som visas här:

![Indataporten data](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webbtjänst
Nu är du redo att köra ett litet experiment som kan användas för att publicera vår webbtjänst.

#### <a name="generate-input-data-for-webservice"></a>Generera indata för webbtjänsten
Steg zeroth eftersom tabellen count är stor, vidta några rader testdata och generera utdata från den med antal funktioner. Detta kan fungera som indata för vår webbtjänsten. Detta visas här:

![Skapa BDT indata](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> För indata-formatet använder utdata från den **antal Featurizer** modulen. När detta experiment har slutförts kör, spara utdata från den **antal Featurizer** modulen som en datauppsättning. Den här datauppsättningen används för indata i webbtjänsten.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Bedömning experiment för publicera webbtjänsten
Först visas det hur detta ser ut. Den grundläggande strukturen är en **Poängmodell** modul som accepterar vår trained model-objektet och några rader av indata som har genererats i föregående steg med den **antal Featurizer** modulen. Använd ”Välj kolumner i datauppsättning” till projektet ut Scored etiketter och troliga poäng.

![Välj kolumner i datauppsättning](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observera hur **Välj kolumner i datauppsättning** modul kan användas för att ”filtrera” data bort från en datauppsättning. Innehållet visas här:

![Filtrering med Välj kolumner i datauppsättning modulen](./media/hive-criteo-walkthrough/oVUJC9K.png)

Om du vill ha blå indata och utdata portar, klickar du på **förbereda webbtjänsten** i nederkant högra hörnet. Kör det här experimentet också kan vi publicera webbtjänsten: Klicka på den **publicera WEBBTJÄNSTEN** ikonen i det nedre högra, visa här:

![Publicera webbtjänst](./media/hive-criteo-walkthrough/WO0nens.png)

När webbtjänsten har publicerats, kommer du automatiskt till en sida som ser ut så:

![Instrumentpanelen för webbtjänsten](./media/hive-criteo-walkthrough/YKzxAA5.png)

Lägg märke till de två länkarna för webservices på vänster sida:

* Den **begäran/svar** Service (eller Resursposter) är avsedd för enkel förutsägelser och är vad som har använts i den här workshop.
* Den **BATCHKÖRNING** (BES-Service) används för batch förutsägelser och kräver att indata används för att göra förutsägelser som finns i Azure Blob Storage.

Klicka på länken **begäran/svar** tar vi en sida som ger oss före burk koden i C#, python och R. Den här koden kan enkelt användas för att göra anrop till webbtjänsten. Observera att API-nyckeln på den här sidan som ska användas för autentisering.

Det är praktiskt att kopiera den här python-koden över till en ny cell i IPython notebook.

Här är en del av python-kod med rätt API-nyckel.

![Python-kod](./media/hive-criteo-walkthrough/f8N4L4g.png)

Observera att standard-API-nyckel har ersatts med vår webservices API-nyckel. Klicka på **kör** för den här cellen i en IPython notebook ger följande svar:

![IPython-svar](./media/hive-criteo-walkthrough/KSxmia2.png)

Testa för de två exemplen och svar om (i JSON-ramverket för python-skriptet) så kan du tillbaka svar i formatet ”Scored etiketter, Scored sannolikhet”. I det här fallet har standardvärdena valts att i förväg kapslade kod ger (0 för alla numeriska kolumner och strängen ”value” för alla kategoriska kolumner).

Detta avslutar vår genomgång som visar hur du hanterar storskaliga datauppsättning med hjälp av Azure Machine Learning. Du igång med en terabyte data, skapas en förutsägelsemodell och distribuerat det som en webbtjänst i molnet.

