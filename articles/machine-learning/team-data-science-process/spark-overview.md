---
title: "Översikt över datavetenskap med Spark på Azure HDInsight | Microsoft Docs"
description: "Spark MLlib toolkit ger betydande maskininlärning integrera affärsmodelleringsfunktioner för distribuerade HDInsight-miljö."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: d9964ace6b59fa65f0f5d4caff28a4291047c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Översikt över datavetenskap med Spark på Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Den här sviten avsnitt visar hur du använder HDInsight Spark slutföra vanliga datavetenskap åtgärder, till exempel datapåfyllning, funktionen tekniker, modellering och utvärdering av modellen. De data som används är ett exempel på 2013 NYC taxi resa och avgiften dataset. Modeller som skapats är logistic och linjär regression, slumpmässiga skogar och toning ökat träd. Avsnitten visar även hur du lagrar dessa modeller i Azure blob storage (WASB) och hur du poängsätta och utvärdera deras förutsägbar prestanda. Mer avancerade avsnitt beskriver hur modeller kan vara tränas med omfattande korsvalidering och hyper-parametern. Det här översiktsavsnittet refererar även till avsnitt som beskriver hur du ställer in Spark-klustret som du måste slutföra stegen i anvisningarna tillgängliga. 

## <a name="spark-and-mllib"></a>Spark och MLlib
[Spark](http://spark.apache.org/) är ett ramverk för parallellbearbetning med öppen källkod som stöder minnesintern bearbetning för att höja prestandan för stora program för stordataanalys. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks funktioner för beräkning för distribuerade i minnet gör det ett bra alternativ för iterativa algoritmer i machine learning och grafberäkningar. [MLlib](http://spark.apache.org/mllib/) är Sparks skalbara machine learning bibliotek som ger den algoritmbaserade modeling funktioner för den här distribuerad miljö. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/hdinsight-apache-spark-overview.md) är öppen källkod Spark Azure värdbaserade erbjuds. Den omfattar också stöd för **Jupyter PySpark-anteckningsböcker** i Spark-klustret som kan köra interaktiva Spark SQL-frågor för att omvandla, filtrering och visualisera data som lagras i Azure BLOB (WASB). PySpark är Python-API för Spark. Kodstycken som tillhandahåller lösningarna och visa de relevanta områdena visualisera data här köras i Jupyter-anteckningsböcker som är installerad på Spark-kluster. Modellering stegen i följande avsnitt innehåller kod som visar hur du träna, utvärdera, spara och använda varje typ av modellen. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Installationsprogrammet: Spark-kluster och Jupyter-anteckningsböcker
Konfigurationsstegen och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men Jupyter-anteckningsböcker som har angetts för både HDInsight Spark 1.6 och 2.0 Spark-kluster. En beskrivning av bärbara datorer och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplats som innehåller dessa. Dessutom koden här länkade anteckningsböcker är generisk och bör fungera på Spark-kluster. Om du inte använder HDInsight Spark kanske klustret installation och hantering av stegen skiljer sig från vad som anges här. För enkelhetens skull är här länkar till Jupyter-anteckningsböcker för Spark 1.6 (för att köras i pySpark-kerneln Jupyter Notebook-Server) och Spark 2.0 (för att köras i kernelns pySpark3 Jupyter-anteckningsbok Server):

### <a name="spark-16-notebooks"></a>Spark 1.6 bärbara datorer
Dessa datorer är att köras i pySpark-kerneln Jupyter-anteckningsbok Server.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): innehåller information om hur du utför datagranskning modellering och bedömningen med flera olika algoritmer.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i anteckningsboken #1 och modellen utveckling med hjälp av hyperparameter justering och korsvalidering.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Visar hur du operationalisera en sparad modell med Python på HDInsight-kluster.

### <a name="spark-20-notebooks"></a>Spark 2.0 bärbara datorer
Dessa datorer är att köra i kernelns pySpark3 Jupyter-anteckningsbok Server.

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför datagranskning modellering, och bedömningen i Spark 2.0-kluster med NYC Taxi resa och avgiften datauppsättning beskrivs [här](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden som vi har angetts för Spark 2.0. För en mer detaljerad anteckningsbok analyserar NYC Taxi data, finns i nästa anteckningsboken i den här listan. Om du hittar information efter den här listan som jämför dessa datorer. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data wrangling (Spark SQL och dataframe operations), utforskning modellering och bedömningen med NYC Taxi resa och avgiften datauppsättning beskrivs [här](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data wrangling (Spark SQL och dataframe operations), utforskning modellering och bedömningen med välkända flygbolag i tid avvikelse datauppsättningen från 2011 och 2012. Vi integrerad flygbolag dataset med flygplats väder data (t.ex. vindhastigheten temperatur, höjd etc.) innan du modellera, så att funktionerna väder kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flygbolag datamängden har lagts till Spark 2.0 bärbara datorer att illustrera bättre användning av algoritmer för klassificering. Se följande länkar för information om flygbolag i tid avvikelse dataset och väder datamängd:

>- Flygbolag i tid avvikelse data: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Flygplats väder data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Spark 2.0 anteckningsböcker på NYC taxi och flygbolag svarta fördröjning-datauppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på HDI-kluster). Första anteckningsboken i listan ovan visas många aspekter av datagranskning, visualisering och ML-modell utbildning i en bärbar dator som det tar mindre tid att köra med ned provtagning NYC datamängd, där taxi och avgiften filer har redan anslutits: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) anteckningsboken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra utgångspunkt för snabbt utforska koden som vi har angetts för Spark 2.0. 

<!-- -->

Vägledning om operationalization av en Spark 2.0 och modellen förbrukningen för resultatfunktioner finns i [Spark 1.6 dokumentet på förbrukningen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) ett exempel som beskriver de steg som krävs. Om du vill använda detta på Spark 2.0, ersätter filen Python-kod med [filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Krav
Följande procedurer relaterade till Spark 1.6. Använd anteckningsböcker beskrivs och länka till tidigare Spark 2.0-versionen. 

1. Du måste ha en Azure-prenumeration. Om du inte redan har en, se [hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. måste en 1.6 Spark-klustret för att slutföra den här genomgången. Om du vill skapa en finns i [komma igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Typ av kluster och version har angetts från den **Välj typ av kluster** menyn. 

![Konfigurera klustret](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ett avsnitt som visar hur du utföra uppgifter för vetenskap en slutpunkt till slutpunkt av data med hjälp av Scala i stället för Python, finns det [datavetenskap med hjälp av Scala med Spark på Azure](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 Taxi data
NYC Taxi resa data är cirka 20 GB komprimerad fil med kommaavgränsade värden (CSV)-filer (~ 48 GB okomprimerade), som består av fler än 173 miljoner enskilda resor och priser betalat för varje resa. Varje post resa innehåller plocka upp och Samlingsbibliotek plats och tid, anonymiserade hackare (drivrutin) licensnummer och medallion (taxi's unikt id) nummer. Data omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

1. 'Trip_data' CSV-filer innehåller resa information, till exempel antalet passagerare, hämta och dropoff pekar utlösas varaktighet och resa längd. Här följer några Exempelposter:
   
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

Vi har tagit en 0,1% exempel på dessa filer och ansluten resan\_data och resa\_färdavgiften CSV-filer i en enda datamängd som används som indata datamängden för den här genomgången. Unik nyckel för att ansluta till resa\_data och resa\_avgiften består av fälten: medallion hackare\_licensen och hämtning\_datetime. Varje post i datauppsättningen innehåller följande attribut som representerar en NYC Taxi resa:

| Fält | Kort beskrivning |
| --- | --- |
| medallion |Anonymiserade taxi medallion (taxi unikt id) |
| hack_license |Anonymiserade Hackney transport licensnummer |
| vendor_id |Taxi leverantörs-id |
| rate_code |NYC taxi frekvensen avgiften |
| store_and_fwd_flag |Lagra och vidarebefordra flaggan |
| pickup_datetime |Hämta datum och tid |
| dropoff_datetime |Dropoff datum och tid |
| pickup_hour |Hämta timme |
| pickup_week |Hämta veckan på året |
| veckodag |Veckodag (mellan 1-7) |
| passenger_count |Passagerare i en taxi resa |
| trip_time_in_secs |Resa tiden i sekunder |
| trip_distance |Resa avstånd som obligatoriska i mil |
| pickup_longitude |Hämta longitud |
| pickup_latitude |Hämta latitud |
| dropoff_longitude |Dropoff longitud |
| dropoff_latitude |Dropoff latitud |
| direct_distance |Dirigera avståndet mellan plocka upp och dropoff platser |
| payment_type |Betalningssätt (CA: er, kreditkortsnummer osv.) |
| fare_amount |Avgiften beloppet i |
| Tillägg |Tillägg |
| mta_tax |MTA skatt |
| tip_amount |Tips belopp |
| tolls_amount |Vägtullar belopp |
| total_amount |Totalmängd |
| lutad |Lutad (0-1 för Nej eller Ja) |
| tip_class |Tips klass (0: 0, 1: $0-5, 2: $6 – 10, 3: $11-20, 4: > 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra kod från en Jupyter notebook i Spark-klustret
Du kan starta Jupyter-anteckningsbok från Azure-portalen. Spark-kluster på instrumentpanelen och klicka på den för att ange hanteringssidan för klustret. Klicka för att öppna den bärbara datorn som är associerade med Spark-klustret **Klusterinstrumentpaneler** -> **Jupyter-anteckningsbok** .

![Klustret instrumentpaneler](./media/spark-overview/spark-jupyter-on-portal.png)

Du kan även bläddra till ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** till Jupyter-anteckningsböcker. Ersätt det KLUSTERNAMN som en del av denna URL med namnet på ditt eget kluster. Du behöver lösenordet för ditt administratörskonto att få åtkomst till de bärbara datorerna.

![Bläddra Jupyter-anteckningsböcker](./media/spark-overview/spark-jupyter-notebook.png)

Välj PySpark att se en katalog som innehåller några exempel på förhand packade bärbara datorer som använder PySpark-API. Datorer som innehåller kodexempel för den här serien Spark-avsnittet finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Du kan ladda upp bärbara datorer direkt från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) till Jupyter-anteckningsbok servern på Spark-kluster. På startsidan för din Jupyter klickar du på den **överför** knappen i den högra delen av skärmen. En Utforskaren öppnas. Här kan du klistra in GitHub (rådata innehåll) URL: en för bärbara datorer och klicka på **öppna**. 

Du ser filnamnet i listan Jupyter-fil med en **överför** igen. Klicka här **överför** knappen. Nu har du importerat den bärbara datorn. Upprepa dessa steg för att ladda upp andra datorer från den här genomgången.

> [!TIP]
> Du kan högerklicka på länkarna i webbläsaren och välj **Kopiera länk** att hämta github raw innehåll Webbadress. Du kan klistra in URL: en i dialogrutan Jupyter överför filen explorer.
> 
> 

Nu kan du:

* Visa koden genom att klicka på den bärbara datorn.
* Köra varje cell genom att trycka på **SKIFT-ange**.
* Kör anteckningsboken genom att klicka på **Cell** -> **kör**.
* Använda automatisk visualisering av frågor.

> [!TIP]
> PySpark-kerneln visualizes automatiskt utdata för SQL (HiveQL)-frågor. Du har möjlighet att välja mellan flera olika typer av grafik (register, cirkeldiagram, rad, område eller fältet) med hjälp av den **typen** menyn knapparna i anteckningsboken:
> 
> 

![Logistic regression ROC-kurvan generisk metod](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Nästa steg
Nu när du har konfigurerat ett HDInsight Spark-kluster och har överfört Jupyter-anteckningsböcker är du redo att gå igenom de avsnitt som motsvarar tre PySpark bärbara datorer. De visar hur du utforska dina data och hur du skapar och använda modeller. Avancerade undersökning och modellering bärbar dator visar hur du inkludera korsvalidering, hyper-parametern omfattande, och modellerar utvärdering. 

**Datagranskning och modellering med Spark:** utforska datauppsättningen och skapa poängsätta och utvärdera modeller för maskininlärning genom att utföra den [skapa binär klassificering och regression modeller för data med Spark MLlib toolkit](spark-data-exploration-modeling.md) avsnittet.

**Modellen förbrukning:** information om hur du poängsätta klassificering och regression modeller som skapats i det här avsnittet finns [poängsätta och utvärdera Spark-inbyggda machine learning-modeller](spark-model-consumption.md).

**Korsvalidering och hyperparameter omfattande**: se [avancerade datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) på hur modeller kan vara tränas med omfattande korsvalidering och hyper-parameter

