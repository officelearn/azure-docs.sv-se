---
title: Djupdykning - advanced analytics – Azure HDInsight
description: Lär dig hur avancerad analys använder algoritmer för att bearbeta stordata.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 81057271fefddf7d204217d73b56e2734c8c3d48
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162842"
---
# <a name="deep-dive---advanced-analytics"></a>Djupdykning – avancerad analys

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Vad är avancerade analyser för HDInsight?

HDInsight ger möjlighet att få värdefulla insikter från stora mängder strukturerade och Ostrukturerade och snabbrörliga data. Avancerade analyser är användning av skalbara arkitekturer, statistiska och maskininlärningsmodeller och intelligent instrumentpaneler för att ge dig meningsfulla insikter. Machine learning, eller *förutsägelseanalys*, använder algoritmer som identifierar och lär dig av relationer i dina data för att göra förutsägelser och hjälper ditt beslut.

## <a name="advanced-analytics-process"></a>Processen för avancerad analys

![Process](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

När du har identifierat problem i verksamheten och har startat samla in och bearbeta dina data, måste du skapa en modell som representerar frågan som du vill förutsäga. Modellen använder en eller flera machine learning-algoritmer för att göra en förutsägelse som bäst passar dina affärsbehov.  Flesta av dina data ska användas för att träna din modell med resten används för att testa eller utvärdera den. 

När du skapar, läser in, testa och utvärdera din modell, är nästa steg att distribuera din modell så att den börjar med svar på dina frågor. Det sista steget är att övervaka prestanda för din modell och finjustera den vid behov. 

## <a name="common-types-of-algorithms"></a>Vanliga typer av algoritmer

Avancerade Analyslösningar ger en uppsättning med machine learning-algoritmer. Här följer en sammanfattning av kategorier av algoritmer och associerade vanliga användningsområden.

![Machine Learning-användningsfall](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Tillsammans med valt bäst passningsåtgärderna algoritm(er), måste du överväga om du måste ange data för utbildning. Machine learning-algoritmer kategoriseras enligt följande:

* Övervakat - måste algoritmen utbildas på en uppsättning märkta data innan den ger resultat
* Delvis övervakat - algoritmen kan förstärkas med extra mål via interaktiv fråga genom en utbildare som inte var tillgängliga under inledande fasen av utbildning
* Oövervakad - kräver algoritmen inte finjusteringsdata 
* Förstärkt - algoritmen använder programvaruagenter för att avgöra perfekt beteende inom ett visst sammanhang (som ofta används i robotteknik)


| Algoritmen kategori| Användning | Learning typ | Algoritmer |
| --- | --- | --- | -- |
| Klassificering | Klassificera personer eller saker i grupper | Övervakat | Beslutsträd, Logistic regression, neurala nätverk |
| Klustring | Att dela upp en uppsättning exempel i enhetliga grupper | Oövervakade | K-means klustring |
| Mönster för identifiering | Identifiera frekventa associationer i data | Oövervakade | Association regler |
| Regression | Förutsäga numeriskt resultat | Övervakat | Linjär regression, neurala nätverk |
| Förstärkt | Fastställa optimal beteende för robotar | Förstärkt | Monte Carlo-simuleringar, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight har flera alternativ för ett arbetsflöde för avancerad analys för maskininlärning:

* [Maskininlärning och Spark](#machine-learning-and-spark)
* [R och ML-tjänster](#r-and-r-server)
* [Azure Machine Learning och Hive](#azure-machine-learning-and-hive)
* [Spark och djupinlärning](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Maskininlärning och Spark

[HDInsight Spark](../spark/apache-spark-overview.md) är ett erbjudande för Azure som värd för [Spark](https://spark.apache.org/), en enhetlig öppen källkod, ramverk för parallellbearbetning av data som använder minnesintern bearbetning för att öka analyser av Stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks InMemory-distribuerad beräkning funktioner blir det ett bra alternativ för iterativa algoritmer som används i machine learning och grafberäkningar. 

Det finns tre skalbar machine learning-bibliotek som få algoritmiska modelleringsfunktioner till den här distribuerad miljö:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib innehåller den ursprungliga API: et är byggt på Spark rdd-datauppsättningar.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML är ett nyare paket som innehåller ett på högre nivå API som byggts ovanpå Spark dataramar för att konstruera ML pipelines.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – Microsoft Machine Learning-biblioteket för Apache Spark (MMLSpark) är utformad för att göra dataexperter mer produktiva i Spark, att öka frekvensen för experimentering och utnyttja avancerade maskininlärning teknik, inklusive djupinlärning på mycket stora datauppsättningar. MMLSpark-bibliotek förenklar vanliga modelleringsuppgifter för att skapa modeller i PySpark. 

### <a name="r-and-ml-services"></a>R och ML-tjänster

Som en del av HDInsight, kan du skapa ett HDInsight-kluster med [ML tjänster](../r-server/r-server-overview.md) redo att användas med enorma datauppsättningar och modeller. Den här nya funktionen ger dataexperter och statistiker med ett välbekanta R-gränssnitt som kan skalas på begäran via HDInsight, utan att behöva konfiguration och underhåll.

### <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning och Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) tillhandahåller verktyg för att modellen förutsägande analys, samt en helt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som är klara att använda webbtjänster. Azure Machine Learning innehåller verktyg för att skapa fullständiga förutsägelseanalyslösningar i molnet för att snabbt skapa, testa, operationalisera och hantera förutsägelsemodeller. Välj från ett stort algoritmbibliotek, använda webbaserade verktyg för modellutveckling och enkelt distribuera din modell som en webbtjänst.

### <a name="spark-and-deep-learning"></a>Spark och djupinlärning

[Djupinlärning](https://www.microsoft.com/research/group/dltc/) är en gren av machine learning som använder *djupa neurala nätverk* (dnn: er), INSPIRERAD av bioteknik processer i den mänskliga hjärnan. Djupinlärning finns som en lovande metod för artificiell intelligens många forskare. Några exempel på djupinlärning är talat språk översättare, bild av system och datorn resonemang. För att gå vidare sitt eget arbete i djupinlärning, Microsoft har utvecklat kostnadsfria, enkel att använda, öppen källkod [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Verktyget används ofta av en mängd olika Microsoft-produkter, företag över hela världen med ett behov av att distribuera djupinlärnings i stor skala och studenter som är intresserade av senaste algoritmer och tekniker. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario – poäng bilder för att identifiera mönster i utveckling av städer

Vi ska gå igenom ett exempel på en pipeline med hjälp av HDInsight för avancerad analys av maskininlärning.

I det här scenariot visas så här dnn: er skapas i ett ramverk för djupinlärning, Microsoft azures Cognitive Toolkit (CNTK), kan ska operationaliseras för bedömning stor bild samlingar som lagras i en Azure Blob Storage-konto med hjälp av PySpark på ett HDInsight Spark-kluster. Den här metoden gäller för en vanliga DNN användningsfall klassificering av Flygfoto, och kan användas för att identifiera de senaste mönster i utveckling av städer.  Du använder en modell för klassificering av förtränade bild. Modellen är förtränade på den [CIFAR 10 datauppsättning](https://www.cs.toronto.edu/~kriz/cifar.html) och har kopplats till 10 000 avdragna avbildningar.

Det finns tre viktiga uppgifter i det här scenariot för avancerad analys:

1. Skapa ett Azure HDInsight Hadoop-kluster med en Apache Spark 2.1.0-distribution. 
2. Köra ett anpassat skript för att installera Microsoft Cognitive Toolkit på alla noder i ett Azure HDInsight Spark-kluster. 
3. Ladda upp en färdiga Jupyter-anteckningsbok till ditt HDInsight Spark-kluster för att tillämpa en tränad Microsoft Cognitive Toolkit deep learning-modell till filer i en Azure Blob Storage-konto med hjälp av Spark Python API (PySpark). 

Det här exemplet används CIFAR 10 avbildningen ange kompilerade och distribuerad av Alex Krizhevsky, Vinod Nair och Geoffrey Hinton. CIFAR-10-datauppsättningen innehåller 60 000 32 × 32 bakgrundsfärg för bilder som hör till 10 ömsesidigt uteslutande klasser:

![Avbildningar](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Mer information om datauppsättningen finns i Alex Krizhevsky [Learning flera lager av funktioner från små bilder](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datauppsättningen har partitionerats i en utbildning uppsättning 50 000 bilder och en test uppsättning 10 000 bilder. Den första uppsättningen användes för att träna en modell för 20 layer djup convolutional resterande nätverk (ResNet) med hjälp av Microsoft Cognitive Toolkit genom att följa [den här självstudien](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) från Cognitive Toolkit GitHub-lagringsplatsen. Återstående 10 000 bilder användes för att testa modellens precision. Det här är var distribuerad databehandling kommer betydelse: uppgiften att bearbeta data i förväg och bedömning bilderna är mycket kan. Med den sparade tränade modellen i hand använde vi:

* PySpark att distribuera avbildningar och tränade modellen till klustrets arbetsnoder.
* Python för att Förbearbeta avbildningar på varje nod i HDInsight Spark-klustret.
* Cognitive Toolkit för att läsa in modellen och poäng förbearbetade bilderna på varje nod.
* Jupyter-anteckningsböcker för att köra PySpark-skript sammanställa resultaten och använda [Matplotlib](https://matplotlib.org/) att visualisera modellens prestanda.

Hela Förbearbeta/bedömning av 10 000 bilder tar mindre än en minut på ett kluster med 4 arbetsnoder. Modellen förutsäger korrekt etiketter med ~ 9,100 (91%) avbildningar. En felmatris illustrerar de vanligaste felen för klassificering. Matrisen visar till exempel att mislabeling hundar som katt och vice versa sker mer ofta än för andra par med etikett.

![Resultat](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Prova!

Följ [den här självstudien](../spark/apache-spark-microsoft-cognitive-toolkit.md) att implementera denna lösning slutpunkt till slutpunkt: installera ett HDInsight Spark-kluster, installera Cognitive Toolkit och kör Jupyter-anteckningsboken som poängsätter 10 000 CIFAR bilder.

## <a name="next-steps"></a>Nästa steg

Hive och Azure Machine Learning

* [Hive och Azure Machine Learning slutpunkt till slutpunkt](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Med ett Azure HDInsight Hadoop-kluster på en datauppsättning som 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark- och MLLib

* [Machine learning med Spark i HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning och Cognitive Toolkit

* [Embarrassingly parallel bildklassificering, med Cognitive Toolkit och TensorFlow på Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Data Science Azure-dator](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introduktion till H2O.ai på Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
