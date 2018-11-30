---
title: Översikt över datavetenskap med Spark på Azure HDInsight | Microsoft Docs
description: Spark MLlib toolkit ger betydande maskininlärning modellering funktioner för distribuerad HDInsight-miljö.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: c8bd44ddc6f4996982a32385b36d24c0531dc0aa
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446321"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Översikt över datavetenskap med Spark på Azure HDInsight

Den här programsviten avsnitt visar hur du kan utföra vanliga datavetenskapsuppgifter som datainmatning, funktionsframställning, modellering och utvärdering av modellen med hjälp av HDInsight Spark. De data som används är ett exempel på 2013 NYC taxi resa och avgiften datauppsättningen. Modeller som byggts är logistic och linjär regression, slumpmässiga skogar och gradient bättre träd. Avsnitten visar också hur du lagrar dessa modeller i Azure blob storage (WASB) och hur du bedöma och utvärdera deras förutsägande prestanda. Mer avancerade avsnitt beskriver hur modeller kan vara tränas med hjälp av oinskränkt korsvalidering och hyper-parametern. Det här översiktsavsnittet refererar även till de avsnitt som beskriver hur du ställer in Spark-kluster som du behöver för att slutföra stegen i anvisningarna tillgängliga. 

## <a name="spark-and-mllib"></a>Spark- och MLlib
[Spark](http://spark.apache.org/) är ett ramverk för parallellbearbetning med öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordataanalys av stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks InMemory-distribuerad beräkning funktioner blir det ett bra alternativ för iterativa algoritmer som används i machine learning och grafberäkningar. [MLlib](http://spark.apache.org/mllib/) är Sparks skalbar machine learning-biblioteket som ger den algoritmiska modellering funktioner för den här distribuerad miljö. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är i Azure-värdbaserade versionen av öppen källkod Spark. Den har även stöd för **Jupyter PySpark-anteckningsböcker** i Spark-klustret som kan köra interaktiva Spark SQL-frågor för att omvandla, filtrera och visualisera data som lagras i Azure BLOB-objekt (WASB). PySpark är Python-API för Spark. Kodfragment som tillhandahåller lösningar och visa relevanta områden att visualisera data här körs i Jupyter-anteckningsböcker som installerats på Spark-kluster. Modellering stegen i följande avsnitt innehåller kod som visar hur du tränar, utvärdera, spara och använda varje typ av modellen. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Installationsprogrammet: Spark-kluster och Jupyter notebooks
Steg för konfiguration och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men tillhandahålls Jupyter notebooks för HDInsight Spark 1.6- och Spark 2.0. En beskrivning av anteckningsböcker och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplatsen som innehåller dessa. Dessutom koden här och i länkade anteckningsböcker är generisk och bör fungera i ett Spark-kluster. Om du inte använder HDInsight Spark, konfiguration och hantering av steg kanske skiljer sig från vad som anges här. För enkelhetens skull följer du länkarna till Jupyter-anteckningsböcker för Spark 1.6 (för att köras i pySpark-kerneln för Jupyter Notebook-server) och Spark 2.0 (för att köras i pySpark3 kernel Jupyter Notebook-server):

### <a name="spark-16-notebooks"></a>Spark 1.6 anteckningsböcker
Dessa datorer är att köras i pySpark-kerneln för Jupyter notebook-server.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): innehåller information om hur du utför datagranskning, modellering och bedömning med flera olika algoritmer.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i anteckningsboken #1 och modellen utveckling med hjälp av finjustering av hyperparametrar och korsvalidering.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Visar hur du operationalisera en sparad modell med hjälp av Python i HDInsight-kluster.

### <a name="spark-20-notebooks"></a>Spark 2.0-anteckningsböcker
Dessa datorer är att köras i pySpark3 kernel Jupyter notebook-server.

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför datagranskning, modellering, och bedömning i Spark 2.0-kluster med NYC Taxi-resa och avgiften-datauppsättning beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden som vi har lagt till för Spark 2.0. För en mer detaljerad anteckningsbok analyserar NYC Taxi-data, visas i nästa anteckningsboken i den här listan. Se information efter den här listan som jämför dessa anteckningsböcker. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data experimenteringsfunktioner (Spark SQL och dataframe åtgärder), undersökning, modellering och bedömning med hjälp av NYC Taxi resa och avgiften datauppsättning beskrivs [här ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data experimenteringsfunktioner (Spark SQL och dataframe åtgärder), undersökning, modellering och bedömning med hjälp av välkända flygbolag i tid avgår datauppsättningen från 2011 och 2012. Vi integrerat flygbolag datauppsättning med en flygplats weather-data (t.ex. vindhastigheten, temperaturen, höjd osv) före modellering, så dessa väder-funktioner kan tas med i modellen.

<!-- -->

> [!NOTE]
> Flygbolag datauppsättningen har lagts till Spark 2.0-anteckningsböcker för att ge en bättre illustrering användningen av algoritmer för klassificering. Se följande länkar för information om flygbolag i tid avgång datauppsättningen och väder datauppsättning:

>- Flygbolag i tid avgång data: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Flygplats weather-data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Spark 2.0-anteckningsböcker på NYC taxi och flygbolag flygning fördröjning-datauppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på HDI-kluster). Första anteckningsboken i listan ovan visar många aspekter av datagranskning, visualisering och ML modellera utbildning på en bärbar dator som tar mindre tid att köra med ned samplas NYC datamängd, där filerna taxi och avgiften har redan domänansluten: [ Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) anteckningsboken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra startpunkt för att snabbt utforska koden har vi lagt till för Spark 2.0. 

<!-- -->

Anvisningar för driftsättningen av en Spark 2.0 och förbrukning av modell för bedömning finns i den [Spark 1.6 dokumentet på förbrukning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) ett exempel som beskriver de steg som krävs. Om du vill använda det på Spark 2.0, Ersätt kodfilen Python med [filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Förutsättningar
Följande procedurer är relaterade till Spark 1.6. Använda anteckningsböcker som beskrivs och länkas till tidigare för Spark 2.0-versionen. 

1. Du måste ha en Azure-prenumeration. Om du inte redan har en, se [skaffa Azure kostnadsfria utvärderingsversionen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. du behöver en Spark 1.6 klustret för att slutföra den här genomgången. För att skapa en, se instruktionerna i [komma igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ av kluster och version har angetts från den **Välj typ av kluster** menyn. 

![Konfigurera klustret](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> En artikel som visar hur du använder Scala i stället för Python för att utföra åtgärder för en slutpunkt till slutpunkt data science process, finns det [datavetenskap med Scala med Spark på Azure](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 Taxi-data
NYC Taxi resedata är cirka 20 GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48 GB okomprimerad), som består av mer än 173 miljoner enskilda kommunikation och priser betalda för varje resa. Plocka upp och dropoff plats och tid, avidentifierade hack (drivrutin) licensnummer och medallion (taxi's unikt id) antalet innehåller varje resa-post. Informationen som täcker alla kommunikation i år 2013 och anges i följande två datauppsättningar för varje månad:

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

Vi har tagit en 0,1% urval av de här filerna och anslutna resan\_data och resans\_färdavgiften CVS-filer till en enda datauppsättning som används som den inkommande datauppsättningen för den här genomgången. Den unika nyckeln för att ansluta till resans\_data och resans\_avgiften består av fälten: medallion, hacka\_licens och upphämtning\_datetime. Varje post datauppsättningen innehåller följande attribut som representerar en NYC Taxi-resa:

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
| direct_distance |Dirigera avståndet mellan plocka upp och dropoff platser |
| payment_type |Betalningstypen (CA: er, kreditkort osv.) |
| fare_amount |Avgiften beloppet i |
| Tilläggsavgift |Tilläggsavgift |
| mta_tax |MTA skatt |
| tip_amount |Tips belopp |
| tolls_amount |Vägtullar belopp |
| total_amount |Totalmängd |
| lutad |Lutad (0/1 för Nej eller Ja) |
| tip_class |Tips klass (0: $0, 1: $0 och 5, 2: $6 – 10, 3: $11-20, 4: > 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra kod från en Jupyter notebook i Spark-klustret
Du kan starta en Jupyter-anteckningsboken från Azure-portalen. Hitta ditt Spark-kluster på instrumentpanelen och klicka om du vill ange hanteringssidan för klustret. Klicka för att öppna anteckningsboken som är associerade med Spark-kluster **Klusterinstrumentpaneler** -> **Jupyter Notebook** .

![Klusterinstrumentpaneler](./media/spark-overview/spark-jupyter-on-portal.png)

Du kan också gå till ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** till Jupyter Notebooks. Ersätt det KLUSTERNAMN som en del av denna URL med namnet på ett eget kluster. Du behöver lösenordet för ditt administratörskonto att få åtkomst till de bärbara datorerna.

![Bläddra Jupyter-anteckningsböcker](./media/spark-overview/spark-jupyter-notebook.png)

Välj PySpark att se en katalog som innehåller några exempel på förhand packade anteckningsböcker som använder PySpark-API. Anteckningsböcker som innehåller kodexempel för den här programsviten av Spark-avsnittet finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Du kan ladda upp anteckningsböckerna direkt från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) till Jupyter notebook-server på Spark-kluster. På startsidan för din Jupyter, klickar du på den **överför** knappen i den högra delen av skärmen. En Utforskaren öppnas. Här kan du klistra in GitHub (raw innehåll)-URL: en för bärbara datorer och klicka på **öppna**. 

Du se namnet på filen på din Jupyter fillistan med en **överför** igen. Klicka här **överför** knappen. Nu har du importerat anteckningsboken. Upprepa dessa steg för att ladda upp anteckningsböckerna från den här genomgången.

> [!TIP]
> Du kan högerklicka på länken i webbläsaren och välj **Kopiera länk** att hämta github URL för raw innehåll. Du kan klistra in följande URL i dialogrutan Jupyter ladda upp fil explorer.
> 
> 

Nu kan du:

* Visa koden genom att klicka på den bärbara datorn.
* Kör varje cell genom att trycka på **SKIFT-ange**.
* Köra anteckningsboken genom att klicka på **Cell** -> **kör**.
* Använda automatisk visualisering av frågor.

> [!TIP]
> PySpark-kerneln visualiserar automatiskt utdata från SQL (HiveQL)-frågor. Du har möjlighet att välja bland flera olika typer av visualiseringar (register, cirkel, rad, området eller fält) med hjälp av den **typ** menyn knappar i anteckningsboken:
> 
> 

![Logistic regression ROC-kurvan för allmän metod](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Nästa steg
Nu när du har ställts in för ett HDInsight Spark-kluster och har laddat upp Jupyter notebooks, är du redo att arbeta igenom ämnena som motsvarar de tre PySpark-anteckningsböckerna. De visar hur du utforska dina data och sedan hur du skapar och använda modeller. Avancerade utforskning och modellering anteckningsboken visar hur du inkluderar korsvalidering, hyper-parametern oinskränkt, och modellera utvärdering. 

**Datagranskning och modellering med Spark:** utforska datauppsättningen och skapa, poäng och utvärdera maskininlärningsmodeller genom att utföra den [skapa binär klassificering och regression modeller för data med Spark MLlib Toolkit](spark-data-exploration-modeling.md) avsnittet.

**Modellera förbrukning:** information om hur du bedöma klassificerings- och regressionsmodeller modeller som skapats i det här avsnittet finns [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md).

**Korsvalidering och finjustering oinskränkt**: se [avancerad datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med hjälp av oinskränkt korsvalidering och hyper-parameter

