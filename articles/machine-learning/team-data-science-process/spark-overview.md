---
title: Datavetenskap med Spark på Azure HDInsight - Team Data Science Process
description: Spark MLlib toolkit ger betydande maskininlärning modellering funktioner för distribuerad HDInsight-miljö.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718522"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Översikt över datavetenskap med Spark på Azure HDInsight

Den här programsviten avsnitt visar hur du kan utföra vanliga datavetenskapsuppgifter som datainmatning, funktionsframställning, modellering och utvärdering av modellen med hjälp av HDInsight Spark. De data som används är ett exempel på 2013 NYC taxi resa och avgiften datauppsättningen. Modeller som byggts är logistic och linjär regression, slumpmässiga skogar och gradient bättre träd. Avsnitten visar också hur du lagrar dessa modeller i Azure blob storage (WASB) och hur du bedöma och utvärdera deras förutsägande prestanda. Mer avancerade avsnitt beskriver hur modeller kan vara tränas med hjälp av oinskränkt korsvalidering och hyper-parametern. Det här översiktsavsnittet refererar även till de avsnitt som beskriver hur du ställer in Spark-kluster som du behöver för att slutföra stegen i anvisningarna tillgängliga.

## <a name="spark-and-mllib"></a>Spark- och MLlib
[Spark](https://spark.apache.org/) är ett ramverk för parallell bearbetning med öppen källkod som har stöd för minnes intern bearbetning för att öka prestandan för program med stor data analys. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks InMemory-distribuerad beräkning funktioner blir det ett bra alternativ för iterativa algoritmer som används i machine learning och grafberäkningar. [MLlib](https://spark.apache.org/mllib/) är Spark: s skalbara Machine Learning-bibliotek som ger algoritmiska modellerings funktioner till den här distribuerade miljön.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är det Azure-värdbaserade Spark-erbjudandet för öppen källkod. Den innehåller också stöd för **Jupyter PySpark-anteckningsböcker** i Spark-klustret som kan köra Spark SQL-interaktiva frågor för omvandling, filtrering och visualisering av data som lagras i Azure blobs (WASB). PySpark är Python-API för Spark. Kodfragment som tillhandahåller lösningar och visa relevanta områden att visualisera data här körs i Jupyter-anteckningsböcker som installerats på Spark-kluster. Modellering stegen i följande avsnitt innehåller kod som visar hur du tränar, utvärdera, spara och använda varje typ av modellen.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Installationsprogrammet: Spark-kluster och Jupyter notebooks
Steg för konfiguration och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men tillhandahålls Jupyter notebooks för HDInsight Spark 1.6- och Spark 2.0. En beskrivning av antecknings böckerna och länkar till dem finns i [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom koden här och i länkade anteckningsböcker är generisk och bör fungera i ett Spark-kluster. Om du inte använder HDInsight Spark, konfiguration och hantering av steg kanske skiljer sig från vad som anges här. För enkelhetens skull är här länkarna till Jupyter-anteckningsböcker för Spark 1,6 (som ska köras i pySpark-kärnan i Jupyter Notebook-servern) och Spark 2,0 (som ska köras i pySpark3-kärnan i Jupyter Notebook-servern):

### <a name="spark-16-notebooks"></a>Spark 1.6 anteckningsböcker
Dessa datorer är att köras i pySpark-kerneln för Jupyter notebook-server.

- [pySpark-Machine-Learning-data-science-Spark-data-utforskning-modellering. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): ger information om hur du utför data granskning, modellering och poäng med flera olika algoritmer.
- [pySpark-Machine-Learning-data-science-Spark-Advanced-data-prospektering-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i notebook-#1 och modell utveckling med hjälp av widgeten för att justera och kors validering.
- [pySpark-Machine-Learning-data-science-Spark-Model-förbrukning. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): visar hur du operationalisera en sparad modell med python i HDInsight-kluster.

### <a name="spark-20-notebooks"></a>Spark 2.0-anteckningsböcker
Dessa datorer är att köras i pySpark3 kernel Jupyter notebook-server.

- [Spark 2.0-pySpark3-Machine-Learning-data-science-Spark-Advanced-data-utforskning-modellering. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför data granskning, modellering och poängsättning i Spark 2,0-kluster med NYC taxi-resa och pris data uppsättning som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden som vi har lagt till för Spark 2.0. För en mer detaljerad anteckningsbok analyserar NYC Taxi-data, visas i nästa anteckningsboken i den här listan. Se anteckningarna som följer den här listan och som jämför dessa antecknings böcker.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data Datatransformering (Spark SQL-och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av NYC taxi resa och pris data uppsättning som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data Datatransformering (Spark SQL-och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av den välkända flyg data uppsättningen från 2011 och 2012. Vi integrerade flyg data uppsättningen med flyg platsens väder data (till exempel windspeed, temperatur, höjd osv.) före modellering, så att dessa väder funktioner kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flygbolag datauppsättningen har lagts till Spark 2.0-anteckningsböcker för att ge en bättre illustrering användningen av algoritmer för klassificering. Se följande länkar för information om flygbolag i tid avgång datauppsättningen och väder datauppsättning:
> 
> - Flyg-vid-tid-sändnings data: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Flyg plats väder data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Spark 2.0-anteckningsböcker på NYC taxi och flygbolag flygning fördröjning-datauppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på HDI-kluster). Den första bärbara datorn i listan ovan visar många aspekter av utbildningen för data utforskning, visualisering och analys i en bärbar dator som tar mindre tid att köra med insamlade NYC data uppsättningar, där taxi-och pris uppgifter har varit i förväg anslutna: [Spark 2.0-pySpark3-Machine-Learning-data-science-Spark-Advanced-data-prospektering-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Den här antecknings boken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra utgångs punkt för att snabbt utforska den kod vi har angett för Spark 2,0.

<!-- -->

Vägledning för driftsättning av en spark 2,0-modell och modell förbrukning för poängsättning finns i [Spark 1,6-dokumentet om förbrukningen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) för ett exempel som beskriver de steg som krävs. Om du vill använda det här exemplet på Spark 2,0 ersätter du python-kodfragmentet med [den här filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Förutsättningar

Följande procedurer är relaterade till Spark 1.6. Använda anteckningsböcker som beskrivs och länkas till tidigare för Spark 2.0-versionen.

1. Du måste ha en Azure-prenumeration. Om du inte redan har ett kan du läsa [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Du behöver ett Spark 1,6-kluster för att slutföra den här genom gången. Information om hur du skapar ett finns i anvisningarna i [komma igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Kluster typen och-versionen anges från menyn **Välj kluster typ** .

![Konfigurera klustret](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ett ämne som visar hur du använder Scala i stället för python för att slutföra aktiviteter för en slut punkt till slut punkt för data vetenskaps processer finns i [data vetenskap med Scala med Spark på Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 Taxi-data
NYC Taxi resedata är cirka 20 GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48 GB okomprimerad), som består av mer än 173 miljoner enskilda kommunikation och priser betalda för varje resa. Hämtning och dropoff plats och tid, avidentifierade hack (drivrutin) licensnummer och medallion (taxi's unikt id) antalet innehåller varje resa-post. Informationen som täcker alla kommunikation i år 2013 och anges i följande två datauppsättningar för varje månad:

1. 'Trip_data' CSV-filer innehåller resans information, till exempel antalet passagerare, hämta och dropoff pekar utlösas varaktighet och resans längd. Här följer några Exempelposter:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. 'Trip_fare' CSV-filer innehåller information om avgiften betalat för varje förflyttning, till exempel betalningstypen, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala beloppet som betalas. Här följer några Exempelposter:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Vi har tagit ett exempel på 0,1% av de här filerna och anslöt resan\_data och resa\_biljett-CVS-filer till en enda data uppsättning som ska användas som indata-datauppsättning för den här genom gången. Den unika nyckeln för att ansluta till resan\_data och resan\_avgiften består av fälten: Medallion, hacka\_licens och hämtning\_DateTime. Varje post datauppsättningen innehåller följande attribut som representerar en NYC Taxi-resa:

| Fält | Kort beskrivning |
| --- | --- |
| medallion |Maskerade taxi medallion (unikt taxi-id) |
| hack_license |Maskerade Hackney vagnretur licensnummer |
| vendor_id |Taxi leverantörs-id |
| rate_code |NYC taxi andelen avgiften |
| store_and_fwd_flag |Store och vidarebefordra flagga |
| pickup_datetime |Hämta datum och tid |
| dropoff_datetime |Dropoff datum och tid |
| pickup_hour |Välj upp timme |
| pickup_week |Hämta vecka på året |
| Veckodag |WEEKDAY (mellan 1-7) |
| passenger_count |Passagerare i en taxi-resa |
| trip_time_in_secs |Resans tiden i sekunder |
| trip_distance |Resans avstånd i miles REST |
| pickup_longitude |Hämta longitud |
| pickup_latitude |Hämta latitud |
| dropoff_longitude |Dropoff longitud |
| dropoff_latitude |Dropoff latitud |
| direct_distance |Direkt avstånd mellan upphämtnings-och DropOff platser |
| payment_type |Betalnings typ (kontant, kredit kort osv.) |
| fare_amount |Avgiften beloppet i |
| Tilläggsavgift |Tilläggsavgift |
| mta_tax |Transport avgift för MTA-tunnel |
| tip_amount |Tips belopp |
| tolls_amount |Vägtullar belopp |
| total_amount |Totalmängd |
| lutad |Lutad (0/1 för Nej eller Ja) |
| tip_class |Tips klass (0: $0, 1: $0 och 5, 2: $6 – 10, 3: $11-20, 4: > 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra kod från en Jupyter notebook i Spark-klustret
Du kan starta en Jupyter-anteckningsboken från Azure-portalen. Hitta ditt Spark-kluster på instrumentpanelen och klicka om du vill ange hanteringssidan för klustret. Öppna den antecknings bok som är associerad med Spark-klustret genom att klicka på **kluster instrument paneler** -> **Jupyter Notebook**.

![Klusterinstrumentpaneler](./media/spark-overview/spark-jupyter-on-portal.png)

Du kan också bläddra till ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** för att få åtkomst till antecknings böckerna Jupyter. Ersätt det KLUSTERNAMN som en del av denna URL med namnet på ett eget kluster. Du behöver lösenordet för ditt administratörskonto att få åtkomst till de bärbara datorerna.

![Bläddra Jupyter-anteckningsböcker](./media/spark-overview/spark-jupyter-notebook.png)

Välj PySpark om du vill se en katalog som innehåller några exempel på förpaketerade antecknings böcker som använder PySpark-API: et. De antecknings böcker som innehåller kod exemplen för den här serien av Spark-ämne finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Du kan ladda upp antecknings böckerna direkt från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) till Jupyter Notebook-servern i Spark-klustret. På Start sidan för din Jupyter klickar du på knappen **överför** på den högra delen av skärmen. En Utforskaren öppnas. Här kan du klistra in URL: en för GitHub (RAW content) för antecknings boken och klicka på **Öppna**.

Du ser fil namnet i Jupyter-fillistan med knappen **överför** igen. Klicka på den här knappen **överför** . Nu har du importerat anteckningsboken. Upprepa dessa steg för att ladda upp anteckningsböckerna från den här genomgången.

> [!TIP]
> Du kan högerklicka på länkarna i webbläsaren och välja **Kopiera länk** för att hämta URL: en för GitHub RAW-innehåll. Du kan klistra in följande URL i dialogrutan Jupyter ladda upp fil explorer.
> 
> 

Nu kan du:

* Visa koden genom att klicka på den bärbara datorn.
* Kör varje cell genom att trycka på **SKIFT-RETUR**.
* Kör hela antecknings boken genom att klicka på **Cell** -> **Kör**.
* Använda automatisk visualisering av frågor.

> [!TIP]
> PySpark-kerneln visualiserar automatiskt utdata från SQL (HiveQL)-frågor. Du får välja mellan flera olika typer av visualiseringar (tabell, cirkel diagram, linje, yta eller stapel) med hjälp av meny knapparna **typ** i antecknings boken:
>
>

![Logistic regression ROC-kurvan för allmän metod](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Nästa steg
Nu när du har ställts in för ett HDInsight Spark-kluster och har laddat upp Jupyter notebooks, är du redo att arbeta igenom ämnena som motsvarar de tre PySpark-anteckningsböckerna. De visar hur du utforska dina data och sedan hur du skapar och använda modeller. Avancerade utforskning och modellering anteckningsboken visar hur du inkluderar korsvalidering, hyper-parametern oinskränkt, och modellera utvärdering.

**Data utforskning och modellering med Spark:** Utforska data uppsättningen och skapa, score och utvärdera maskin inlärnings modeller genom att gå igenom [skapa binära klassificerings-och Regressions modeller för data med Spark MLlib Toolkit](spark-data-exploration-modeling.md) -avsnittet.

**Modell förbrukning:** Information om hur du visar klassificerings-och Regressions modeller som skapats i det här avsnittet finns i [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md).

**Rensning av kors validering och Cross-parameter**: se [Avancerad data granskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med kors validering och rensning av Hyper-parameter

