---
title: Datavetenskap med Spark på Azure HDInsight - Team Data Science Process
description: Spark MLlib-verktygslådan ger betydande funktioner för maskininlärningsmodellering till den distribuerade HDInsight-miljön.
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
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718522"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Översikt över datavetenskap med Spark på Azure HDInsight

Den här uppsättningen avsnitt visar hur du använder HDInsight Spark för att slutföra vanliga datascience uppgifter som datainmatning, funktionsteknik, modellering och modellutvärdering. De uppgifter som används är ett urval av 2013 NYC taxiresa och biljettpris datauppsättning. Modellerna som byggs inkluderar logistisk och linjär regression, slumpmässiga skogar och lutningsbebogda träd. Ämnena visar också hur du lagrar dessa modeller i Azure blob storage (WASB) och hur du poäng och utvärdera deras prediktiva prestanda. Mer avancerade ämnen beskriver hur modeller kan tränas med korsvalidering och hyperparametersvepning. Det här översiktsavsnittet refererar också till de avsnitt som beskriver hur du ställer in Spark-klustret som du behöver för att slutföra stegen i de genomgångar som tillhandahålls.

## <a name="spark-and-mllib"></a>Spark och MLlib
[Spark](https://spark.apache.org/) är ett parallellbearbetningsramverk med öppen källkod som stöder bearbetning i minnet för att öka prestandan för stordataanalytiska program. Spark-bearbetningsmotorn är byggd för hastighet, användarvänlighet och sofistikerad analys. Sparks distribuerade beräkningsfunktioner i minnet gör det till ett bra val för de iterativa algoritmer som används vid maskininlärning och grafberäkningar. [MLlib](https://spark.apache.org/mllib/) är Sparks skalbara maskininlärningsbibliotek som tar de algoritmiska modelleringsfunktionerna till den här distribuerade miljön.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är Azure-värderbjudandet av Spark med öppen källkod. Den innehåller också stöd för **Jupyter PySpark-anteckningsböcker** i Spark-klustret som kan köra Spark SQL-interaktiva frågor för omvandling, filtrering och visualisering av data som lagras i Azure Blobbar (WASB). PySpark är Python API för Spark. Kodavsnitten som tillhandahåller lösningarna och visar relevanta ritdiagram för att visualisera data som körs här i Jupyter-anteckningsböcker som är installerade på Spark-klustren. Modelleringsstegen i dessa avsnitt innehåller kod som visar hur du tränar, utvärderar, sparar och använder varje typ av modell.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Installation: Spark kluster och Jupyter bärbara datorer
Installationssteg och kod finns i den här genomgången för att använda en HDInsight Spark 1.6. Men Jupyter-anteckningsböcker finns för både HDInsight Spark 1.6- och Spark 2.0-kluster. En beskrivning av anteckningsböckerna och länkarna till dem finns i [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade anteckningsböckerna allmän och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan stegen för klusterinställningar och hantering skilja sig något från vad som visas här. För enkelhetens skull, här är länkarna till Jupyter bärbara datorer för Spark 1,6 (som ska köras i pySpark kärnan i Jupyter Notebook server) och Spark 2.0 (som ska köras i pySpark3 kärnan i Jupyter Notebook server):

### <a name="spark-16-notebooks"></a>Spark 1.6 bärbara datorer
Dessa bärbara datorer ska köras i pySpark-kärnan på Jupyters bärbara server.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Ger information om hur du utför datautforskning, modellering och bedömning med flera olika algoritmer.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Innehåller ämnen i bärbara #1 och modellutveckling med hyperparameterjustering och korsvalidering.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Visar hur du operationaliserar en sparad modell med Python på HDInsight-kluster.

### <a name="spark-20-notebooks"></a>Spark 2.0 bärbara datorer
Dessa bärbara datorer ska köras i pySpark3-kärnan på Jupyters bärbara server.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Den här filen innehåller information om hur du utför datautforskning, modellering och bedömning i Spark 2.0-kluster med hjälp av NYC Taxi-resan och biljettprisdatauppsättningen som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden vi har angett för Spark 2.0. En mer detaljerad anteckningsbok analyserar NYC Taxi-data i nästa anteckningsbok i den här listan. Se anteckningarna som följer den här listan som jämför dessa anteckningsböcker.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Den här filen visar hur du utför datakäbbel (Spark SQL och dataframe operationer), prospektering, modellering och poängsättning med hjälp av NYC Taxi resa och biljettpris data-set som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Den här filen visar hur du utför datakäbbel (Spark SQL- och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av den välkända airline on-time-avvikelsedatauppsättningen från 2011 och 2012. Vi integrerade flygbolagets datauppsättning med flygplatsens väderdata (till exempel vindhastighet, temperatur, höjd etc.) före modellering, så att dessa väderfunktioner kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flygbolagsdatauppsättningen lades till i Spark 2.0-anteckningsböckerna för att bättre illustrera användningen av klassificeringsalgoritmer. Se följande länkar för information om flygbolagens avgångsdatauppsättning i tid och väderdatauppsättning:
> 
> - Uppgifter om avgång i tid:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Väderdata på flygplatsen:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Spark 2.0-bärbara datorer på NYC-taxi- och flygförseningsdatauppsättningarna kan ta 10 minuter eller mer att köra (beroende på storleken på ditt HDI-kluster). Den första anteckningsboken i listan ovan visar många aspekter av datautforskning, visualisering och ML modell utbildning i en bärbar dator som tar mindre tid att köra med down-sampled NYC datauppsättning, där taxi och filer biljettpris har föranslutits: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Den här anteckningsboken tar mycket kortare tid att avsluta (2-3 minuter) och kan vara en bra utgångspunkt för att snabbt utforska koden vi har gett för Spark 2.0.

<!-- -->

Vägledning om operationalisering av en Spark 2.0-modell och modellförbrukning för bedömning finns i [Spark 1.6-dokumentet om förbrukning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) för ett exempel som beskriver de steg som krävs. Om du vill använda det här exemplet på Spark 2.0 ersätter du Python-kodfilen med [den här filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Krav

Följande procedurer är relaterade till Spark 1.6. Använd de anteckningsböcker som beskrivs och länkats till tidigare för Spark 2.0-versionen.

1. Du måste ha en Azure-prenumeration. Om du inte redan har en, se [Hämta azure gratis provperiod](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Du behöver ett Spark 1.6-kluster för att slutföra den här genomgången. Information om hur du skapar en, se instruktionerna i [Kom igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Klustertypen och versionen anges på menyn **Välj klustertyp.**

![Konfigurera kluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ett ämne som visar hur du använder Scala i stället för Python för att slutföra uppgifter för en datavetenskapsprocess från slutna till sluten tid finns i [Data science med Scala med Spark på Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Den NYC 2013 Taxi data
Den NYC Taxi Trip data är ca 20 GB komprimerade kommaavgränsade värden (CSV) filer (~ 48 GB okomprimerad), bestående av mer än 173 miljoner enskilda resor och de priser som betalas för varje resa. Varje resa post innehåller pickup och dropoff plats och tid, anonymiserade hacka (förarens) licensnummer och medaljong (taxi unika id) nummer. Uppgifterna omfattar alla resor år 2013 och finns i följande två datamängder för varje månad:

1. Csv-filerna "trip_data" innehåller reseinformation, till exempel antal passagerare, hämtnings- och avlämningspunkter, restid och reselängd. Här är några exempelposter:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. CSV-filerna "trip_fare" innehåller information om det pris som betalats för varje resa, till exempel betalningstyp, biljettprisbelopp, tilläggsavgift och skatter, tips och vägtullar samt det totala belopp som betalats. Här är några exempelposter:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Vi har tagit ett 0,1% urval av\_dessa\_filer och gick med i resan data och resa biljettpris CVS filer i en enda datauppsättning att använda som indatauppsättning för denna genomgång. Den unika nyckeln\_för att\_gå med resa data och resa\_biljettpris\_består av fälten: medaljong, hacka licens och pickup datetime. Varje post i datauppsättningen innehåller följande attribut som representerar en NYC Taxi-resa:

| Field | Kort beskrivning |
| --- | --- |
| Medaljong |Anonymiserad taxi medaljong (unik taxi id) |
| hack_license |Anonymiserat licensnummer för Hackney-vagn |
| vendor_id |Id för taxiförsäljare |
| rate_code |NYC taxi pris för biljettpriset |
| store_and_fwd_flag |Lagra och vidarebefordra flagga |
| pickup_datetime |Plocka upp datum & tid |
| dropoff_datetime |Avlämningsdatum & tid |
| pickup_hour |Hämta timme |
| pickup_week |Plocka upp årets vecka |
| Vardag |Veckodag (intervall 1-7) |
| passenger_count |Antal passagerare i en taxiresa |
| trip_time_in_secs |Restid i sekunder |
| trip_distance |Resans tillstånd i miles |
| pickup_longitude |Plocka upp longitud |
| pickup_latitude |Plocka upp latitud |
| dropoff_longitude |Avlämning longitud |
| dropoff_latitude |Latitud för avlämning |
| direct_distance |Direkt avstånd mellan upphämtnings- och avlämningsplatser |
| payment_type |Betalningstyp (kontanter, kreditkort etc.) |
| fare_amount |Biljettprisbelopp i |
| Avgiftsbelagd |Avgiftsbelagd |
| mta_tax |MTA Metro Transport skatt |
| tip_amount |Tips belopp |
| tolls_amount |Avgiftsbelopp |
| total_amount |Totalmängd |
| Tippas |Tippade (0/1 för nej eller ja) |
| tip_class |Tips klass (0: $ 0, 1: $ 0-5, 2: $ 6-10, 3: $ 11-20, 4: > $ 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra kod från en Jupyter-anteckningsbok i Spark-klustret
Du kan starta Jupyter Notebook från Azure-portalen. Leta reda på Spark-klustret på instrumentpanelen och klicka på det för att ange hanteringssidan för klustret. Om du vill öppna anteckningsboken som är associerad med Spark-klustret klickar du på**Jupyter-anteckningsbok** **för klusterpaneler** -> .

![Instrumentpaneler för kluster](./media/spark-overview/spark-jupyter-on-portal.png)

Du kan också ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** bläddra till för att komma åt Jupyter bärbara datorer. Ersätt CLUSTERNAME-delen av den här URL:en med namnet på ditt eget kluster. Du behöver lösenordet för ditt administratörskonto för att komma åt anteckningsböckerna.

![Bläddra i Jupyter-anteckningsböcker](./media/spark-overview/spark-jupyter-notebook.png)

Välj PySpark om du vill visa en katalog som innehåller några exempel på förpaketerade anteckningsböcker som använder PySpark-API:et. Anteckningsböckerna som innehåller kodexemplen för den här uppsättningen Spark-avsnitt finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Du kan ladda upp anteckningsböckerna direkt från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) till Jupyters anteckningsboksserver i Spark-klustret. På startsidan för din Jupyter klickar du på **knappen Ladda upp** till höger på skärmen. Den öppnar en utforskare. Här kan du klistra in GitHub-url:en (rått innehåll) för anteckningsboken och klicka på **Öppna**.

Du ser filnamnet på fillistan Jupyter med knappen **Ladda upp** igen. Klicka på den här **knappen Ladda upp.** Nu har du importerat anteckningsboken. Upprepa de här stegen för att ladda upp de andra anteckningsböckerna från den här genomgången.

> [!TIP]
> Du kan högerklicka på länkarna i din webbläsare och välja **Kopiera länk** för att få GitHub raw content URL. Du kan klistra in webbadressen i dialogrutan Jupyter Upload-utforskare.
> 
> 

Nu kan du:

* Se koden genom att klicka på anteckningsboken.
* Kör varje cell genom att trycka på **SKIFT-RETUR**.
* Kör hela anteckningsboken genom att klicka på **Cell** -> **Run**.
* Använd automatisk visualisering av frågor.

> [!TIP]
> PySpark-kärnan visualiserar automatiskt utdata från SQL-frågor (HiveQL). Du får möjlighet att välja mellan flera olika typer av visualiseringar (Tabell, Cirkel, Linje, Område eller Stapel) med hjälp av **menyknapparna Typ** i anteckningsboken:
>
>

![Logistisk regressions-ROC-kurva för generisk metod](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Nästa steg
Nu när du är konfigurerad med ett HDInsight Spark-kluster och har laddat upp Jupyter-anteckningsböckerna är du redo att gå igenom de ämnen som motsvarar de tre PySpark-anteckningsböckerna. De visar hur du utforskar dina data och sedan hur man skapar och konsumerar modeller. Den avancerade datautforskningen och modelleringsda anteckningsboken visar hur du inkluderar korsvalidering, hyperparametersvepning och modellutvärdering.

**Data Exploration och modellering med Spark:** Utforska datauppsättningen och skapa, poäng och utvärdera maskininlärningsmodellerna genom att arbeta igenom [modellerna Skapa binär klassificering och regression för data med Spark MLlib-verktygslådan.](spark-data-exploration-modeling.md)

**Modellförbrukning:** Mer information om hur du poängar de klassificerings- och regressionsmodeller som skapats i det här avsnittet finns i [Betygsätta och utvärdera Spark-byggda maskininlärningsmodeller](spark-model-consumption.md).

**Korsvalidering och hyperparametersvepning:** Se [Avancerad datautforskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med korsvalidering och hyperparametersvepning

