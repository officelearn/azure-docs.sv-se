---
title: Djupdykning - avancerad analys - Azure HDInsight
description: Lär dig hur avancerad analys använder algoritmer för att bearbeta stordata i Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646531"
---
# <a name="deep-dive---advanced-analytics"></a>Djupdykning - avancerad analys

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Vad är avancerad analys för HDInsight?

HDInsight ger möjlighet att få värdefull insikt från stora mängder strukturerade, ostrukturerade och snabbrörliga data. Avancerad analys är användningen av mycket skalbara arkitekturer, statistiska och maskininlärningsmodeller och intelligenta instrumentpaneler för att ge dig meningsfulla insikter. Maskininlärning, eller *prediktiv analys,* använder algoritmer som identifierar och lär av relationer i dina data för att göra förutsägelser och styra dina beslut.

## <a name="advanced-analytics-process"></a>Avancerad analysprocess

![Avancerat analysprocessflöde](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

När du har identifierat affärsproblemet och har börjat samla in och bearbeta dina data måste du skapa en modell som representerar den fråga du vill förutsäga. Din modell använder en eller flera maskininlärningsalgoritmer för att göra den typ av förutsägelse som bäst passar dina affärsbehov.  Majoriteten av dina data bör användas för att träna din modell, resten används för att testa eller utvärdera den.

När du har skapat, laddat, testat och utvärderat din modell är nästa steg att distribuera modellen så att den börjar ge svar på dina frågor. Det sista steget är att övervaka modellens prestanda och justera den efter behov.

## <a name="common-types-of-algorithms"></a>Vanliga typer av algoritmer

Avancerade analyslösningar ger en uppsättning maskininlärningsalgoritmer. Här är en sammanfattning av kategorierna av algoritmer och associerade vanliga affärsanvändningsfall.

![Sammanfattningar av maskininlärningskategori](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Tillsammans med att välja de bäst passande algoritmerna måste du överväga om du behöver tillhandahålla data för utbildning. Machine learning algoritmer kategoriseras enligt följande:

* Övervakad - algoritm måste tränas på en uppsättning märkta data innan den kan ge resultat
* Semi-övervakad - algoritm kan utökas med extra mål genom interaktiv fråga av en tränare, som inte var tillgängliga under inledande etappen av utbildningen
* Oövervakad - algoritmen kräver inte träningsdata
* Förstärkning - algoritm använder programvaruagenter för att bestämma idealiskt beteende inom ett specifikt sammanhang (används ofta i robotteknik)

| Algoritm kategori| Användning | Typ av inlärning | Algoritmer |
| --- | --- | --- | -- |
| Klassificering | Klassificera personer eller saker i grupper | Övervakas | Beslutsträd, Logistisk regression, neurala nätverk |
| Klustring | Dela upp en uppsättning exempel i homogena grupper | Oövervakad | K-betyder klustring |
| Mönsteridentifiering | Identifiera frekventa associationer i data | Oövervakad | Association Rules |
| Regression | Förutsäg numeriska utfall | Övervakas | Linjär regression, neurala nätverk |
| Förstärkning | Bestäm optimalt beteende för robotar | Förstärkning | Monte Carlo Simuleringar, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight har flera maskininlärningsalternativ för ett avancerat analysarbetsflöde:

* Maskininlärning och Apache Spark
* R- och ML-tjänster
* Azure Machine Learning och Apache Hive
* Apache Spark och djupinlärning

### <a name="machine-learning-and-apache-spark"></a>Maskininlärning och Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) är ett Azure-värdbaserat erbjudande av [Apache Spark](https://spark.apache.org/), ett enhetligt, ramverk för parallell databearbetning med öppen källkod som använder bearbetning i minnet för att öka Big Data-analys. Spark-bearbetningsmotorn är byggd för hastighet, användarvänlighet och sofistikerad analys. Sparks distribuerade beräkningsfunktioner i minnet gör det till ett bra val för de iterativa algoritmer som används vid maskininlärning och grafberäkningar.

Det finns tre skalbara maskininlärningsbibliotek som ger algoritmiska modelleringsfunktioner till den här distribuerade miljön:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib innehåller det ursprungliga API:et som bygger ovanpå Spark RDD:er.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML är ett nyare paket som tillhandahåller ett API på högre nivå som bygger ovanpå Spark DataFrames för att konstruera ML-pipelines.
* [**MMLSpark**](https://github.com/Azure/mmlspark) – Microsoft Machine Learning-biblioteket för Apache Spark (MMLSpark) är utformat för att göra datavetare mer produktiva på Spark, för att öka antalet experiment och utnyttja avancerade maskininlärningstekniker, inklusive djupinlärning, på mycket stora datamängder. MMLSpark-biblioteket förenklar vanliga modelleringsuppgifter för att bygga modeller i PySpark.

### <a name="r-and-ml-services"></a>R- och ML-tjänster

Som en del av HDInsight kan du skapa ett HDInsight-kluster med [ML Services](../r-server/r-server-overview.md) redo att användas med massiva datauppsättningar och modeller. Den här nya funktionen ger dataforskare och statistiker ett välbekant R-gränssnitt som kan skalas på begäran via HDInsight, utan att klusterinställningar och underhåll är över.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning och Apache Hive

[Azure Machine Learning Studio (klassisk)](https://studio.azureml.net/) innehåller verktyg för att modellera prediktiva analyser och en fullständigt hanterad tjänst som du kan använda för att distribuera dina prediktiva modeller som färdiga att använda webbtjänster. Azure Machine Learning innehåller verktyg för att skapa kompletta lösningar för förutsägelseanalys i molnet för att snabbt skapa, testa, operationalisera och hantera prediktiva modeller. Välj från ett stort algoritmbibliotek, använd en webbaserad studio för att bygga modeller och distribuera enkelt din modell som en webbtjänst.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark och djupinlärning

[Djupinlärning](https://www.microsoft.com/research/group/dltc/) är en gren av maskininlärning som använder *djupa neurala nätverk* (DNN), inspirerade av den mänskliga hjärnans biologiska processer. Många forskare ser djupinlärning som en lovande metod för artificiell intelligens. Några exempel på djupinlärning är översättare i talade språk, bildigenkänningssystem och maskinrekonstruktion. För att främja sitt eget arbete inom djupinlärning har Microsoft utvecklat den kostnadsfria, lättanvända [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)med öppen källkod . Verktygslådan används i stor utsträckning av en mängd olika Microsoft-produkter, av företag över hela världen med ett behov av att distribuera djupinlärning i stor skala, och av studenter som är intresserade av de senaste algoritmerna och teknikerna.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario - Betyg bilder för att identifiera mönster i stadsutveckling

Låt oss granska ett exempel på en avancerad pipeline för analysmaskininlärning med HDInsight.

I det här scenariot ser du hur DNN som produceras i ett ramverk för djupinlärning, Microsofts Cognitive Toolkit (CNTK), kan användas för att göra stora bildsamlingar lagrade i ett Azure Blob Storage-konto med PySpark i ett HDInsight Spark-kluster. Detta tillvägagångssätt tillämpas på ett vanligt DNN-användningsfall, klassificering av flygbilder och kan användas för att identifiera de senaste mönstren i stadsutvecklingen.  Du använder en förtränad bildklassificeringsmodell. Modellen är förutbildad på [CIFAR-10-datauppsättningen](https://www.cs.toronto.edu/~kriz/cifar.html) och har tillämpats på 10 000 innehållna bilder.

Det finns tre viktiga uppgifter i det här avancerade analysscenariot:

1. Skapa ett Azure HDInsight Hadoop-kluster med en Apache Spark 2.1.0-distribution.
2. Kör ett anpassat skript för att installera Microsoft Cognitive Toolkit på alla noder i ett Azure HDInsight Spark-kluster.
3. Ladda upp en förbyggd Jupyter-anteckningsbok till ditt HDInsight Spark-kluster för att tillämpa en tränad Microsoft Cognitive Toolkit-djupinlärningsmodell på filer i ett Azure Blob Storage-konto med Spark Python API (PySpark).

I det här exemplet används den CIFAR-10-bilduppsättning som sammanställts och distribuerats av Alex Krizhevsky, Vinod Nair och Geoffrey Hinton. CIFAR-10 datauppsättning innehåller 60.000 32 × 32 färgbilder som tillhör 10 ömsesidigt uteslutande klasser:

![Exempelbilder av maskininlärning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

För mer information om datauppsättningen, se Alex Krizhevsky's [Learning Flera lager av funktioner från tiny images](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datauppsättningen delades in i en utbildningsuppsättning med 50 000 bilder och en testuppsättning med 10 000 bilder. Den första uppsättningen användes för att träna en tjugolagersdjup faltningsnätverksmodell (ResNet) med hjälp av Microsoft Cognitive Toolkit genom att följa den [här självstudien](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) från Cognitive Toolkit GitHub-databasen. De återstående 10 000 bilderna användes för att testa modellens noggrannhet. Det är här distribuerade datorer spelar in: uppgiften att förbehandling och poängsättning av bilderna är mycket parallelliserbar. Med den sparade tränade modellen i handen använde vi:

* PySpark för att distribuera avbildningarna och tränade modellen till klustrets arbetsnoder.
* Python för att förbehandla avbildningarna på varje nod i HDInsight Spark-klustret.
* Kognitiv verktygslåda för att läsa in modellen och poängsätta förbearbetade bilder på varje nod.
* Jupyter-anteckningsböcker för att köra PySpark-skriptet, aggregera resultaten och använda [Matplotlib](https://matplotlib.org/) för att visualisera modellens prestanda.

Hela förbearbetning/bedömning av de 10 000 bilderna tar mindre än en minut i ett kluster med 4 arbetsnoder. Modellen förutspår exakt etiketterna för ~ 9.100 (91%) Bilder. En förvirringsmatris illustrerar de vanligaste klassificeringsfelen. Matrisen visar till exempel att felmärkning av hundar som katter och vice versa förekommer oftare än för andra etikettpar.

![Resultatdiagram för maskininlärning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Prova det!

Följ [den här självstudien](../spark/apache-spark-microsoft-cognitive-toolkit.md) för att implementera den här lösningen från slutpunkt till slutpunkt: konfigurera ett HDInsight Spark-kluster, installera Cognitive Toolkit och kör jupyter-anteckningsboken som får 10 000 CIFAR-avbildningar.

## <a name="next-steps"></a>Nästa steg

Apache Hive och Azure Machine Learning

* [Apache Hive och Azure Machine Learning från på distans](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Använda ett Azure HDInsight Hadoop-kluster på en datauppsättning med 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark och MLLib

* [Maskininlärning med Apache Spark på HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark med maskininlärning: Använd Apache Spark i HDInsight för att analysera byggnadstemperaturen med HVAC-data](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Apache Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Kognitiv verktygslåda och andra

* [Virtuella Azure-dator för datavetenskap](../../machine-learning/data-science-virtual-machine/overview.md)
* [Vi presenterar H2O.ai på Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
