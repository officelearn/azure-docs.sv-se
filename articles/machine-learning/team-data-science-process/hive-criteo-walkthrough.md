---
title: Använda Azure HDInsight Hadoop Cluster på 1 TB-datauppsättning – Team Data Science Process
description: Använda Team Data Science Process för ett heltäckande scenario med ett HDInsight Hadoop-kluster för att skapa och distribuera en modell med hjälp av en stor (1 TB) offentligt tillgänglig datauppsättning
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
ms.openlocfilehash: 218fb96f6960e194f0fc4a4a3a3e603388b961c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760818"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Teamdatascience-processen i aktion – använda ett Azure HDInsight Hadoop-kluster på en datauppsättning med 1 TB

Den här genomgången visar hur du använder Team Data Science Process i ett heltäckande scenario med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktionstekniker och nedprovsdata från en av de allmänt tillgängliga [Criteo-datauppsättningarna.](https://labs.criteo.com/downloads/download-terabyte-click-logs/) Den använder Azure Machine Learning för att skapa en binär klassificeringsmodell på dessa data. Den visar också hur du publicerar en av dessa modeller som en webbtjänst.

Det är också möjligt att använda en IPython-anteckningsbok för att utföra de uppgifter som presenteras i den här genomgången. Användare som vill prova den här metoden bör konsultera [Criteo genomgång med hjälp av en Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) anslutningsämne.

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Criteo Dataset Beskrivning
Criteo-data är ett klick förutsägelse datauppsättning som är 370 GB gzip komprimerade TSV filer (~ 1,3 TB okomprimerad), bestående av mer än 4,3 miljarder poster. Det tas från 24 dagars klickdata som gjorts tillgängliga av [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). För att underlätta för dataforskare har de data som finns tillgängliga för oss att experimentera med packats upp.

Varje post i den här datauppsättningen innehåller 40 kolumner:

* Den första kolumnen är en etikettkolumn som anger om en användare klickar på ett **tillägg** (värde 1) eller inte klickar på ett (värde 0)
* följande 13 kolumner är numeriska och
* de senaste 26 är kategoriska kolumner

Kolumnerna är anonymiserade och använder en serie uppräknade namn: "Col1" (för etikettkolumnen) till "Col40" (för den sista kategoriska kolumnen).

Här är ett utdrag av de första 20 kolumnerna med två observationer (rader) från denna datauppsättning:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Det saknas värden i både numeriska och kategoriska kolumner i den här datauppsättningen. En enkel metod för hantering av de saknade värdena beskrivs. Ytterligare information om data utforskas när du lagrar dem i Hive-tabeller.

**Definition:** *Klickfrekvens (CTR):* Det här måttet är procentandelen klick i data. I denna Criteo datauppsättning är CTR ca 3,3% eller 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelseuppgifter
Två exempel förutsägelse problem åtgärdas i denna genomgång:

1. **Binär klassificering**: Förutsäger om en användare har klickat på ett tillägg:

   * Klass 0: Inget klick
   * Klass 1: Klicka på
2. **Regression**: Förutsäger sannolikheten för ett annonsklick från användarfunktioner.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Konfigurera ett HDInsight Hadoop-kluster för datavetenskap
> [!NOTE]
> Det här steget är vanligtvis en **administratörsuppgift.**

Konfigurera din Azure Data Science-miljö för att skapa lösningar för förutsägelseanalys med HDInsight-kluster i tre steg:

1. [Skapa ett lagringskonto](../../storage/common/storage-account-create.md): Det här lagringskontot används för att lagra data i Azure Blob Storage. De data som används i HDInsight-kluster lagras här.
2. [Anpassa Azure HDInsight Hadoop-kluster för datavetenskap:](customize-hadoop-cluster.md)Det här steget skapar ett Azure HDInsight Hadoop-kluster med 64-bitars Anaconda Python 2.7 installerat på alla noder. Det finns två viktiga steg (som beskrivs i det här avsnittet) att slutföra när hdinsight-klustret anpassas.

   * Länka lagringskontot som skapats i steg 1 med ditt HDInsight-kluster när det skapas. Det här lagringskontot används för åtkomst till data som kan bearbetas i klustret.
   * Aktivera Fjärråtkomst till huvudnoden för klustret när den har skapats. Kom ihåg de fjärråtkomstautentiseringsuppgifter som du anger här (skiljer sig från de autentiseringsuppgifter som anges vid klusterskapande): slutför följande procedurer.
3. [Skapa en arbetsyta i Azure Machine Learning Studio (klassisk)](../studio/create-workspace.md): Den här Azure Machine Learning-arbetsytan används för att skapa maskininlärningsmodeller efter en inledande datautforskning och nedstursättning på HDInsight-klustret.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Hämta och använda data från en offentlig källa
[Den Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) datauppsättning kan nås genom att klicka på länken, acceptera användarvillkoren, och ge ett namn. En ögonblicksbild visas här:

![Acceptera Criteo-villkor](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klicka på **Fortsätt att hämta** om du vill läsa mer om datauppsättningen och dess tillgänglighet.

Data finns på en [Azure blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) lagringsplats: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "Wasb" refererar till Azure Blob Storage-plats.

1. Data i den här Azure-bloblagringen består av tre undermappar med uppackade data.

   1. Undermappen *rå/räkna/* innehåller de första 21 dagarna\_av data -\_från dag 00 till dag 20
   2. Undermappen *rå/tåg/* består av en enda\_dag med data, dag 21
   3. Undermappen *rå/test/* består av två dagars\_data, dag\_22 och dag 23
2. Råa gzip-data finns också i huvudmappen *raw/* som day_NN.gz, där NN går från 00 till 23.

En alternativ metod för att komma åt, utforska och modellera dessa data som inte kräver några lokala nedladdningar förklaras senare i den här genomgången när vi skapar Hive-tabeller.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Logga in på klusterhuvudnoden
Om du vill logga in på huvudnoden för klustret använder du [Azure-portalen](https://ms.portal.azure.com) för att hitta klustret. Klicka på HDInsight-elefantikonen till vänster och dubbelklicka sedan på namnet på klustret. Navigera till fliken **Konfiguration,** dubbelklicka på CONNECT-ikonen längst ned på sidan och ange dina fjärråtkomstautentiseringsuppgifter när du uppmanas att göra det, så att du kommer till klustrets huvudnod.

Så här ser en typisk första inloggning till klusterhuvudnoden ut:

![Logga in i klustret](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Till vänster finns "Hadoop Command Line", som är vår arbetshäst för datautforskning. Lägg märke till två användbara webbadresser - "Hadoop Yarn Status" och "Hadoop Name Node". Garnstatus-URL:en visar jobbförloppet och namnnod-URL:en ger information om klusterkonfigurationen.

Nu är du konfigurerad och redo att börja första delen av genomgången: datautforskning med Hive och förbereda data för Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Skapa Hive-databas och tabeller
Om du vill skapa Hive-tabeller för vår Criteo-datauppsättning öppnar du ***Hadoop-kommandoraden*** på skrivbordet i huvudnoden och anger Hive-katalogen genom att ange kommandot

    cd %hive_home%\bin

> [!NOTE]
> Kör alla Hive-kommandon i den här genomgången från Hive-bin/katalogprompten. Detta tar hand om eventuella sökvägsproblem automatiskt. Du kan använda termerna "Hive directory prompt", "Hive bin/ directory prompt" och "Hadoop Command Line" omväxlande.
>
> [!NOTE]
> Om du vill köra en Hive-fråga kan man alltid använda följande kommandon:
>
>        cd %hive_home%\bin
>        hive

När Hive REPL visas med ett "hive >"-tecken, klipp enkelt ut och klistra in frågan för att köra den.

Följande kod skapar en databas "criteo" och genererar sedan fyra tabeller:

* Ett *bord för att generera* \_antal byggda\_på dagar dag 00 till dag 20,
* en *tabell för användning som tågdatauppsättning byggd* dag\_21, och
* två *tabeller för användning som testdatauppsättningar* byggda dag\_22 respektive dag\_23.

Dela testdatauppsättningen i två olika tabeller eftersom en av dagarna är en helgdag. Målet är att avgöra om modellen kan identifiera skillnader mellan en helgdag och icke-semester från klickfrekvensen.

[Skriptexemplet&#95;hive&#95;skapa&#95;criteo&#95;databas&#95;och&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) visas här för enkelhetens skull:

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

Alla dessa tabeller är externa så att du kan peka på deras Azure Blob Storage (wasb) platser.

**Det finns två sätt att köra hive-frågan:**

* **Använda Hive REPL-kommandoraden**: Den första är att utfärda ett "hive"-kommando och kopiera och klistra in en fråga på hive REPL-kommandoraden:

        cd %hive_home%\bin
        hive

     Nu på REPL kommandoraden, klippa och klistra in frågan kör den.
* **Spara frågor till en fil och köra kommandot**: Den andra är att spara frågorna till en ".hql" fil ( exempel&#95;[registreringsdatafil&#95;skapa&#95;criteo&#95;databas&#95;och&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) och sedan utfärda följande kommando för att köra frågan:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Bekräfta att databasen och tabellen skapas
Bekräfta sedan skapandet av databasen med följande kommando från Hive-bin/katalogprompten:

        hive -e "show databases;"

Detta ger:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Detta bekräftar skapandet av den nya databasen, "criteo".

För att se vilka tabeller som skapades, helt enkelt utfärda kommandot här från Hive bin / katalog prompt:

        hive -e "show tables in criteo;"

Du bör då se följande utdata:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Datautforskning i Hive
Nu är du redo att göra några grundläggande datautforskning i Hive. Du börjar med att räkna antalet exempel i tåg- och testdatatabellerna.

### <a name="number-of-train-examples"></a>Antal tågexempel
Innehållet i [&#95;bikupa&#95;räkna&#95;tåg&#95;tabell&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) visas här:

        SELECT COUNT(*) FROM criteo.criteo_train;

Detta ger:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternativt kan man också utfärda följande kommando från Hive bin / katalog prompt:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Antal testexempel i de två testdatauppsättningarna
Nu räkna antalet exempel i de två testdatauppsättningar. Innehållet i [&#95;bikupa&#95;räkna&#95;criteo&#95;test&#95;dag&#95;22&#95;tabell&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) är här:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Detta ger:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Som vanligt kan du också anropa skriptet från Hive bin / katalog prompt genom att utfärda kommandot:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Slutligen undersöker du antalet testexempel i testdatauppsättningen baserat på dag\_23.

Kommandot för att göra detta liknar det som visas (se [exempel&#95;bikupa&#95;räkna&#95;criteo&#95;test&#95;dag&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Detta ger:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Etikettfördelning i tågdatauppsättningen
Etikettfördelningen i tågdatauppsättningen är av intresse. För att se detta, visa innehållet i [prov&#95;hive&#95;criteo&#95;etikett&#95;distribution&#95;tåg&#95;table.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Detta ger etikettfördelningen:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Andelen positiva etiketter är cirka 3,3 % (i enlighet med den ursprungliga datauppsättningen).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogramfördelningar av vissa numeriska variabler i tågdatauppsättningen
Du kan använda Hive:s inbyggda "histogramnumerer"\_för att ta reda på hur fördelningen av de numeriska variablerna ser ut. Här är innehållet i [provet&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)

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

LATERAL VIEW - explodera kombination i Hive tjänar till att producera en SQL-liknande utgång i stället för den vanliga listan. I den här tabellen motsvarar den första kolumnen lagerplatscentralen och den andra till lagerplatsfrekvensen.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Ungefärliga percentiler för vissa numeriska variabler i tågdatauppsättningen
Av intresse med numeriska variabler är också beräkningen av ungefärliga percentiler. Hives infödda "percentil\_ca" gör detta för oss. Innehållet i [&#95;bikupa&#95;criteo&#95;ungefärliga&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) är:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Detta ger:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Fördelningen av percentiler är nära relaterad till histogramfördelningen av alla numeriska variabler vanligtvis.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Hitta antal unika värden för vissa kategoriska kolumner i tågdatauppsättningen
Fortsätta datautforskning, hitta, för vissa kategoriska kolumner, antalet unika värden som de tar. För att göra detta, visa innehållet i [prov&#95;hive&#95;criteo&#95;unika&#95;värden&#95;categoricals.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Detta ger:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 har 19M unika värden! Det är inte möjligt att använda naiva tekniker som "en-het kodning" för att koda sådana högdimensionella kategoriska variabler. I synnerhet förklaras och demonstreras en kraftfull, robust teknik som kallas [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) för att hantera detta problem effektivt.

Slutligen titta på antalet unika värden för vissa andra kategoriska kolumner också. Innehållet i [&#95;bikupa&#95;criteo&#95;unika&#95;värden&#95;flera&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) är:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Detta ger:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Återigen, observera att förutom Col20, alla andra kolumner har många unika värden.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Antal samtidig förekomster av par kategoriska variabler i tågdatauppsättningen

Räkningsfördelningarna av par av kategoriska variabler är också av intresse. Detta kan bestämmas med hjälp av koden i [exempel&#95;bikupa&#95;criteo&#95;parat&#95;kategorisk&#95;counts.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Omvänd ordning räknas av deras förekomst och titta på topp 15 i detta fall. Detta ger oss:

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

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Nedåt exempel på datauppsättningarna för Azure Machine Learning
Efter att ha utforskat datauppsättningarna och visat hur du gör den här typen av utforskning för alla variabler (inklusive kombinationer), kan du prova datauppsättningarna så att modeller i Azure Machine Learning kan skapas. Kom ihåg att fokus för problemet är: med tanke på en uppsättning exempel attribut (funktionsvärden från Col2 - Col40), förutsäga om Col1 är en 0 (inget klick) eller en 1 (klick).

Om du vill sänka urvalet av tåg- och testdatauppsättningar till 1 % av den ursprungliga storleken använder du Hives ursprungliga RAND()-funktion. Nästa skript, [exempel&#95;hive&#95;criteo&#95;downsample&#95;träna&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) gör detta för tåget datauppsättning:

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

Skriptet [provet&#95;hive&#95;criteo&#95;downsample&#95;test&#95;dag&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) gör det för testdata, dag\_22:

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


Slutligen, [skriptet provet&#95;hive&#95;criteo&#95;downsample&#95;test&#95;dag&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) gör det för testdata,\_dag 23:

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

Med detta är du redo att använda våra nedsmakade tåg- och testdatauppsättningar för att skapa modeller i Azure Machine Learning.

Det finns en sista viktig komponent innan du går vidare till Azure Machine Learning, som gäller tabellen antal. I nästa underavsnitt beskrivs tabellen antal i detalj.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>En kort diskussion om räknebordet
Som ni såg har flera kategoriska variabler en hög dimensionalitet. I genomgången presenteras en kraftfull teknik som kallas [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) för att koda dessa variabler på ett effektivt och robust sätt. Mer information om denna teknik finns i länken.

>[!NOTE]
>I den här genomgången ligger fokus på att använda räknetabeller för att producera kompakta representationer av högdimensionella kategoriska funktioner. Detta är inte det enda sättet att koda kategoriska funktioner; för mer information om andra tekniker, intresserade användare kan kolla in [en-hot-kodning](https://en.wikipedia.org/wiki/One-hot) och [funktionen hashing](https://en.wikipedia.org/wiki/Feature_hashing).
>

Om du vill skapa räknetabeller på räknedata använder du data i mappen raw/count. I avsnittet modellering visas användare hur du skapar dessa antaltabeller för kategoriska funktioner från grunden, eller alternativt använda en färdigräkningstabell för sina utforskningar. I det följande, när "färdiga räknetabeller" refereras till, menar vi att du använder de räknetabeller som har angetts. Detaljerade instruktioner om hur du kommer åt dessa tabeller finns i nästa avsnitt.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Skapa en modell med Azure Machine Learning
Vår modellbyggprocess i Azure Machine Learning följer följande steg:

1. [Hämta data från Hive-tabeller till Azure Machine Learning](#step1)
2. [Skapa experimentet: rensa data och gör det till en funktion med antal tabeller](#step2)
3. [Bygg, träna och betygsätta modellen](#step3)
4. [Utvärdera modellen](#step4)
5. [Publicera modellen som en webbtjänst](#step5)

Nu är du redo att skapa modeller i Azure Machine Learning studio. Våra nedsmakade data sparas som Hive-tabeller i klustret. Använd modulen **Importdata** för Azure Machine Learning för att läsa dessa data. Autentiseringsuppgifterna för att komma åt lagringskontot för det här klustret finns i följande.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>Steg 1: Hämta data från Hive-tabeller till Azure Machine Learning med modulen Importera data och välj den för ett maskininlärningsexperiment
Börja med att välja ett **+NYTT** -> **EXPERIMENT** -> **tomt experiment**. Sök efter "Importera data" från **sökrutan** längst upp till vänster. Dra och släpp modulen **Importera data** på experimentarbetsytan (den mellersta delen av skärmen) för att använda modulen för dataåtkomst.

Så här ser **importdata** ut när data hämtas från Hive-tabellen:

![Importera data hämtar data](./media/hive-criteo-walkthrough/i3zRaoj.png)

För modulen **Importera data** är värdena för de parametrar som finns i bilden bara exempel på den typ av värden som du behöver ange. Här är några allmänna riktlinjer för hur du fyller i parameteruppsättningen för modulen **Importera data.**

1. Välj "Hive-fråga" för **datakälla**
2. I **frågerutan Hive-databasen** räcker det med ett\_\_enkelt SELECT\_\_* FRÅN <ditt databasnamn.ditt tabellnamn> .
3. **Hcatalog server URI:** Om ditt kluster är "abc", så är detta helt enkelt:https://abc.azurehdinsight.net
4. **Hadoop-användarkontonamn**: Användarnamnet som valdes vid driftsättning av klustret. (INTE fjärråtkomstanvändarnamnet!)
5. **Hadoop-användarkontolösenord**: Lösenordet för det användarnamn som valdes vid driftsättning av klustret. (INTE lösenordet för fjärråtkomst!)
6. **Plats för utdata:** Välj "Azure"
7. **Azure Storage-kontonamn**: Lagringskontot som är associerat med klustret
8. **Azure Storage-kontonyckel:** Nyckeln till lagringskontot som är associerat med klustret.
9. **Azure-behållarnamn**: Om klusternamnet är "abc", är detta helt enkelt "abc", vanligtvis.

När **importdata** har hämtat data (du ser den gröna bocken på modulen) sparar du dessa data som en datauppsättning (med ett namn som du väljer). Hur det ser ut:

![Spara data för import av data](./media/hive-criteo-walkthrough/oxM73Np.png)

Högerklicka på utdataporten för modulen **Importera data.** Detta visar alternativet **Spara som datauppsättning** och ett **visualisera** alternativ. Alternativet **Visualisera** visar, om du klickar på, 100 rader med data, tillsammans med en högerpanel som är användbar för viss sammanfattningsstatistik. Om du vill spara data väljer du bara **Spara som datauppsättning** och följer instruktionerna.

Om du vill välja den sparade datauppsättningen för användning i ett maskininlärningsexperiment letar du reda på datauppsättningarna med hjälp av sökrutan **som** visas i följande bild. Skriv sedan ut namnet du gav datauppsättningen delvis för att komma åt den och dra datauppsättningen till huvudpanelen. Om du släpper den på huvudpanelen väljs den för användning i maskininlärningsmodellering.

![Dra datauppsättningen till huvudpanelen](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Gör detta för både tåget och testdatauppsättningarna. Kom också ihåg att använda databasnamnet och tabellnamnen som du gav för detta ändamål. De värden som används i figuren är endast avsedda som illustrationer.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>Steg 2: Skapa ett experiment i Azure Machine Learning för att förutsäga klick / inga klick
Vårt Azure Machine Learning Studio -experiment (klassiskt) ser ut så här:

![Machine Learning-experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Undersök nu de viktigaste komponenterna i detta experiment. Dra våra sparade tåg- och testdatauppsättningar till vår experimentduk först.

#### <a name="clean-missing-data"></a>Rensa data som saknas
Modulen **Rensa data som saknas** gör vad namnet antyder: den rensar data som saknas på ett sätt som kan anges av användaren. Titta in i den här modulen för att se detta:

![Rensa data som saknas](./media/hive-criteo-walkthrough/0ycXod6.png)

Här väljer du att ersätta alla värden som saknas med en 0: Det finns andra alternativ också, som kan ses genom att titta på rullgardinsmenyn i modulen.

#### <a name="feature-engineering-on-the-data"></a>Funktionsteknik på data
Det kan finnas miljontals unika värden för vissa kategoriska funktioner i stora datauppsättningar. Att använda naiva metoder som en-het kodning för att representera sådana högdimensionella kategoriska funktioner är helt omöjligt. Den här genomgången visar hur du använder räknefunktioner med hjälp av inbyggda Azure Machine Learning-moduler för att generera kompakta representationer av dessa högdimensionella kategoriska variabler. Slutresultatet är en mindre modellstorlek, snabbare utbildningstider och prestandamått som är jämförbara med andra tekniker.

##### <a name="building-counting-transforms"></a>Byggnadsräkningstransformeringar
Om du vill skapa räknefunktioner använder du modulen **Build Counting Transform** som är tillgänglig i Azure Machine Learning. Modulen ser ut så här:

![Skapa modulegenskaper](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![för inventeringstransformering Bygg inventeringstransformering](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> I rutan **Antal kolumner** anger du de kolumner som du vill utföra antal på. Vanligtvis är dessa (som nämnts) högdimensionella kategoriska kolumner. Kom ihåg att Criteo-datauppsättningen har 26 kategoriska kolumner: från Col15 till Col40. Här, räkna med dem alla och ge sina index (från 15 till 40 åtskilda av kommatecken som visas).
>

Om du vill använda modulen i MapReduce-läget (lämplig för stora datauppsättningar) behöver du åtkomst till ett HDInsight Hadoop-kluster (det som används för funktionsutforskning kan återanvändas för detta ändamål också) och dess autentiseringsuppgifter. De tidigare siffrorna visar hur de ifyllda värdena ser ut (ersätt de värden som anges för illustration med de som är relevanta för ditt eget användningsfall).

![Modulparametrar](./media/hive-criteo-walkthrough/05IqySf.png)

Den föregående figuren visar hur du anger indatablolobens plats. Den här platsen har de data som är reserverade för att skapa räknetabeller på.

När den här modulen är klar sparar du transformeringen för senare genom att högerklicka på modulen och välja alternativet **Spara som transformering:**

![Alternativet "Spara som transformera"](./media/hive-criteo-walkthrough/IcVgvHR.png)

I vår experimentarkitektur som visas ovan motsvarar datauppsättningen "ytransform2" exakt en sparad räknetransform. För resten av det här experimentet antas det att läsaren använde en **build counting transform-modul** på vissa data för att generera antal, och kan sedan använda dessa antal för att generera räknefunktioner på tåg- och testdatauppsättningarna.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Välja vilka räknefunktioner som ska ingå som en del av tåg- och testdatauppsättningarna
När ett antal omvandlas redo, kan användaren välja vilka funktioner som ska ingå i sina tåg och testa datauppsättningar med hjälp av modulen **Ändra antal tabellparametrar.** För fullständighet visas den här modulen här. Men för enkelhetens skull faktiskt inte använda den i vårt experiment.

![Ändra parametrar för antal tabell](./media/hive-criteo-walkthrough/PfCHkVg.png)

I det här fallet, som framgår, ska loggoddsen användas och kolumnen bort från baksidan ignoreras. Du kan också ange parametrar som tröskelvärdet för papperskorgen, hur många pseudoförbeställda exempel som ska läggas till för utjämning och om du vill använda laplaciskt brus eller inte. Alla dessa är avancerade funktioner och det bör noteras att standardvärdena är en bra utgångspunkt för användare som är nya för den här typen av funktionsgenerering.

##### <a name="data-transformation-before-generating-the-count-features"></a>Dataomvandling innan du genererar räknefunktionerna
Nu ligger fokus på en viktig punkt om att omvandla våra tåg- och testdata innan vi faktiskt genererar räknefunktioner. Det finns två **Kör R Script** moduler som används innan antalet transformering tillämpas på våra data.

![Köra R-skriptmoduler](./media/hive-criteo-walkthrough/aF59wbc.png)

Här är det första R-skriptet:

![Första R-skriptet](./media/hive-criteo-walkthrough/3hkIoMx.png)

Detta R-skript byter namn på våra kolumner till namnen "Col1" till "Col40". Detta beror på att antalettransformering förväntar sig namn på det här formatet.

Det andra R-skriptet balanserar fördelningen mellan positiva och negativa klasser (klasserna 1 respektive 0) genom att nedsampling av den negativa klassen. R-skriptet här visar hur du gör detta:

![Andra R-skriptet](./media/hive-criteo-walkthrough/91wvcwN.png)

I detta enkla R-skript\_\_används "pos neg ratio" för att ställa in mängden balans mellan de positiva och de negativa klasserna. Detta är viktigt att göra eftersom förbättra klass obalans har oftast prestandafördelar för klassificeringsproblem där klassfördelningen är skev (minns att i detta fall har du 3,3% positiv klass och 96,7% negativ klass).

##### <a name="applying-the-count-transformation-on-our-data"></a>Tillämpa räkneomvandlingen på våra data
Slutligen kan du använda modulen **Använd omvandling** för att tillämpa antalettransformeringar på våra tåg- och testdatauppsättningar. Den här modulen tar den sparade räknetransformeringen som en indata och tåg- eller testdatauppsättningarna som den andra indatan och returnerar data med räknefunktioner. Det visas här:

![Använd omformningsmodul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Ett utdrag av hur räkna funktioner ser ut
Det är lärorikt att se hur räkna funktioner ser ut i vårt fall. Här är ett utdrag av detta:

![Räkna funktioner](./media/hive-criteo-walkthrough/FO1nNfw.png)

Detta utdrag visar att för kolumnerna räknas på, får du räknas och loggodds utöver alla relevanta backoffs.

Du är nu redo att skapa en Azure Machine Learning-modell med hjälp av dessa transformerade datauppsättningar. I nästa avsnitt visas hur detta kan göras.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Steg 3: Bygg, träna och betygsätta modellen

#### <a name="choice-of-learner"></a>Val av elev
Först måste du välja en elev. Använd ett tvåklassigt förstärkt beslutsträd som vår elev. Här är standardalternativen för den här eleven:

![Parametrar för tvåklassuppstufft beslutsträd](./media/hive-criteo-walkthrough/bH3ST2z.png)

För experimentet väljer du standardvärdena. Standardvärdena är meningsfulla och ett bra sätt att få snabba baslinjer på prestanda. Du kan förbättra prestanda genom att svepa parametrar om du väljer att när du har en baslinje.

#### <a name="train-the-model"></a>Träna modellen
För utbildning, helt enkelt åberopa en **tågmodell** modul. De två ingångarna till den är tvåklassen Boosted Decision Tree eleven och vår tågdatauppsättning. Detta visas här:

![Tågmodellmodul](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Poängsätt modellen
När du har en tränad modell är du redo att göra mål på testdatauppsättningen och utvärdera dess prestanda. Gör detta med hjälp av modulen **Poängmodell** som visas i följande bild, tillsammans med en **utvärdera modellmodul:**

![Modulen Poängsätta modell](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>Steg 4: Utvärdera modellen
Slutligen bör du analysera modellens prestanda. Vanligtvis, för två klass (binära) klassificeringsproblem, är ett bra mått AUC. Om du vill visualisera den här kurvan ansluter du modulen **Poängmodell** till modulen **Utvärdera modell.** Om du klickar på **Visualisera** på modulen **Utvärdera modell** ger en bild som följande:

![Utvärdera modul BDT-modell](./media/hive-criteo-walkthrough/0Tl0cdg.png)

I binära klassificeringsproblem (eller två klasser) är ett bra mått på förutsägelsenoggrannhet området för kurva (AUC). Följande avsnitt visar våra resultat med hjälp av denna modell på vår testdatauppsättning. Högerklicka på utdataporten för modulen **Utvärdera modell** och **visualisera**sedan .

![Visualisera modul för utvärdera utvärdera modell](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>Steg 5: Publicera modellen som en webbtjänst
Möjligheten att publicera en Azure Machine Learning-modell som webbtjänster med ett minimum av krångel är en värdefull funktion för att göra den allmänt tillgänglig. När det är gjort kan vem som helst ringa samtal till webbtjänsten med indata som de behöver förutsägelser för, och webbtjänsten använder modellen för att returnera dessa förutsägelser.

Spara först vår utbildade modell som ett tränat modellobjekt genom att högerklicka på modulen **Tågmodell** och använda alternativet **Spara som tränad modell.**

Skapa sedan in- och utdataportar för vår webbtjänst:

* en indataport tar data i samma form som de data som du behöver förutsägelser för
* en utdataport returnerar poängsatta etiketter och tillhörande sannolikheter.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Markera några rader med data för indataporten
Det är praktiskt att använda en **Apply SQL Transformation-modul** för att välja bara 10 rader som ska fungera som indataportdata. Välj bara dessa rader med data för vår indataport med hjälp av SQL-frågan som visas här:

![Data för indata för indata](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webbtjänst
Nu är du redo att köra ett litet experiment som kan användas för att publicera vår webbtjänst.

#### <a name="generate-input-data-for-webservice"></a>Generera indata för webbtjänst
Som ett nollsteg, eftersom antalet tabellen är stor, ta några rader med testdata och generera utdata från den med antal funktioner. Denna utgång kan fungera som indataformat för vår webbtjänst, som visas här:

![Skapa BDT-indata](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Använd modulen Antal **featurizers** utdata för formatet indata. När det här experimentet har körts sparar du utdata från modulen **Count Featurizer** som en datauppsättning. Den här datauppsättningen används för indata i webbtjänsten.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Bedömningsexperiment för publicering av webbtjänst
För det första är den väsentliga strukturen en **poängmodellmodul** som accepterar vårt tränade modellobjekt och några rader indata som genererades i föregående steg med hjälp av modulen **Antal featurizer.** Använd "Välj kolumner i datauppsättning" för att projicera ut poängetiketterna och poängsnanolikheterna.

![Välja kolumner i datauppsättning](./media/hive-criteo-walkthrough/kRHrIbe.png)

Lägg märke till hur modulen **Välj kolumner i datauppsättning** kan användas för att "filtrera" data från en datauppsättning. Innehållet visas här:

![Filtrering med modulen Välj kolumner i datauppsättning](./media/hive-criteo-walkthrough/oVUJC9K.png)

För att få den blå in- och utmatningsportarna klickar du bara på **förbered webbtjänst** längst ned till höger. Köra detta experiment kan vi också publicera webbtjänsten: klicka på **publicera webbtjänst** ikonen längst ner till höger, som visas här:

![Publicera webbtjänst](./media/hive-criteo-walkthrough/WO0nens.png)

När webbtjänsten har publicerats omdirigeras du till en sida som ser ut så här:

![Instrumentpanel för webbtjänsten](./media/hive-criteo-walkthrough/YKzxAA5.png)

Lägg märke till de två länkarna för webbtjänster till vänster:

* **REQUEST/RESPONSE** Service (eller RRS) är avsedd för enstaka förutsägelser och är vad som har använts i denna workshop.
* **BES (BATCH EXECUTION** Service) används för batchförutsägelser och kräver att indata som används för att göra förutsägelser finns i Azure Blob Storage.

Genom att klicka på länken **REQUEST/RESPONSE** tar oss till en sida som ger oss pre-konserverad kod i C #, python och R. Den här koden kan användas enkelt för att ringa samtal till webbtjänsten. API-nyckeln på den här sidan måste användas för autentisering.

Det är praktiskt att kopiera denna python-kod över till en ny cell i IPython anteckningsboken.

Här är ett segment av python-kod med rätt API-nyckel.

![Python-kod](./media/hive-criteo-walkthrough/f8N4L4g.png)

Standard-API-nyckeln har ersatts med vår webbtjänsts API-nyckel. Om du klickar på **Kör** på den här cellen i en IPython-anteckningsbok ger följande svar:

![IPython svar](./media/hive-criteo-walkthrough/KSxmia2.png)

För de två testexemplen som efterfrågas i Python-skriptet JSON får du tillbaka svaren i formuläret "Poängsatta etiketter, poängsatta sannolikheter". I det här fallet har standardvärdena valts som den för konserverade koden tillhandahåller (0:0 för alla numeriska kolumner och strängen "värde" för alla kategoriska kolumner).

Sammanfattningsvis visar vår genomgång hur du hanterar storskalig datauppsättning med Azure Machine Learning. Du började med en terabyte data, konstruerade en förutsägelsemodell och distribuerade den som en webbtjänst i molnet.

