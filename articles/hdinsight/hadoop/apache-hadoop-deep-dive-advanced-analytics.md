---
title: Ingående - advanced analytics - Azure HDInsight | Microsoft Docs
description: Lär dig hur avancerade analyser använder algoritmer för att bearbeta stordata.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f121a1f95eed04bf8086c03a5d4406c092dc9445
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400681"
---
# <a name="deep-dive---advanced-analytics"></a>Ingående - avancerade analyser

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Vad är en avancerad analys för HDInsight?

HDInsight ger möjlighet att få värdefulla insikter från stora mängder strukturerad, Ostrukturerade och flytta snabbt data. Avancerade analyser är användning av skalbara arkitekturer statistiska och machine learning-modeller och intelligent instrumentpaneler för att förse dig med meningsfull insikter. Maskininlärning, eller *förutsägelseanalyser*, används algoritmer som identifierar och Läs från relationer i dina data att göra förutsägelser och hjälper ditt beslut.

## <a name="advanced-analytics-process"></a>Processen för avancerade analyser

![Process](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

När du har identifierat problem i verksamheten och har startat att samla in och bearbetning av dina data, måste du skapa en modell som representerar frågan som du vill förutsäga. Din modell använder en eller flera maskininlärningsalgoritmer för att göra en förutsägelse som bäst passar dina affärsbehov.  Flesta av dina data ska användas för att träna din modell med resten används för att testa eller utvärdera den. 

Nästa steg är att distribuera din modell så att den startar tillhandahåller svar på dina frågor när du skapar, läsa, testa och utvärdera din modell. Det sista steget är att övervaka prestanda för din modell och justera vid behov. 

## <a name="common-types-of-algorithms"></a>Vanliga typer av algoritmer

Avancerade Analyslösningar tillhandahåller en uppsättning maskininlärningsalgoritmer. Här följer en sammanfattning av de algoritmer och associerade vanliga användningsområden.

![Användningsfall för Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Du behöver tänka oavsett om du behöver ange data för träning, tillsammans med att välja bäst passningsåtgärderna algoritm(er). Maskininlärningsalgoritmer kategoriseras på följande sätt:

* Övervakad - måste algoritmen få utbildning för en uppsättning märkta data innan den ger resultat
* Delvis övervakat - algoritmen kan förstärkas med en extra mål genom interaktiva förfrågan av en trainer som inte var tillgängliga under den inledande fasen av utbildning
* Oövervakad - kräver algoritmen inte utbildning data 
* Förstärkning - algoritmen använder programvaruagenter för att bestämmer perfekt inom en särskild kontext (som ofta används i robotics)


| Algoritmen kategori| Användning | Learning typ | Algoritmer |
| --- | --- | --- | -- |
| Klassificering | Klassificera personer eller saker i grupper | Övervakad | Beslutsträd, Logistic regression, neurala nätverk |
| Klustring | Delar en uppsättning exempel i homogen grupper | Oövervakad | K-means klustring |
| Identifiering av mönster | Identifiera ofta kopplingarna i data | Oövervakad | Kopplingen regler |
| Regression | Förutsäga numeriska resultat | Övervakad | Linjär regression, neurala nätverk |
| Förstärkning | Bestäm optimala för robotar | Förstärkning | Monte Carlo-simulering, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight har flera alternativ för ett arbetsflöde för avancerade analyser för maskininlärning:

* [Machine Learning och Spark](#machine-learning-and-spark)
* [R och R Server](#r-and-r-server)
* [Azure Machine Learning och Hive](#azure-machine-learning-and-hive)
* [Spark och djup inlärning](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Machine Learning och Spark

[HDInsight Spark](../spark/apache-spark-overview.md) är en Azure-baserad erbjudande av [Spark](http://spark.apache.org/), en enhetlig, öppen källa, ramverk för parallellbearbetning av data som använder minnesintern bearbetning för att öka analyser av Stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks funktioner för beräkning för distribuerade i minnet gör det ett bra alternativ för iterativa algoritmer i machine learning och grafberäkningar. 

Det finns tre skalbara machine learning-bibliotek som få algoritmbaserade modellering funktioner till den här distribuerad miljö:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib innehåller ursprungliga API byggda på Spark RDDs.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML är ett nyare paket som innehåller ett högre API byggda på Spark DataFrames för att konstruera ML pipelines.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – Microsoft Machine Learning-biblioteket för Apache Spark (MMLSpark) är utformad för att göra datavetare mer produktiv på Spark ökar mängden undersökningar och använder maskininlärning för senaste tekniker, inklusive djup learning på mycket stora datauppsättningar. MMLSpark bibliotek förenklar vanliga modelleringsuppgifter för att skapa modeller i PySpark. 

### <a name="r-and-r-server"></a>R och R Server

Som en del av HDInsight kan du skapa ett HDInsight-kluster med [R Server](../r-server/r-server-overview.md) redo att användas med stora datauppsättningar och modeller. Den här nya funktionen ger dataanalytiker och statistiker med ett bekant R-gränssnitt som kan skalas på begäran via HDInsight, utan extra kostnader för konfiguration och underhåll.

### <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning och Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) tillhandahåller verktyg för att modellen förutsägelseanalyser, samt en helt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som redo att använda webbtjänster. Azure Machine Learning innehåller verktyg för att skapa fullständiga förutsägelseanalyslösningar i molnet för att snabbt skapa, testa, operationalisera och hantera förutsägelsemodeller. Välj ett stort algoritmbibliotek, använder en webbaserad studio för att skapa modeller och enkelt distribuera din modell som en webbtjänst.

### <a name="spark-and-deep-learning"></a>Spark och djup inlärning

[Djupgående learning](https://www.microsoft.com/research/group/dltc/) är en del av maskininlärning som använder *djupa neurala nätverk* (DNNs) inspirerat av mänsklig hjärna biologiska processer. Djupgående learning finns i många forskare som ett Orderlöfte tillvägagångssätt för styrs av datorn. Några exempel på djup learning är talas översättare, bild recognition system och motivationen för datorn. Microsoft har utvecklat gratis, enkelt att använda, öppen källkod för att arbetet i djupgående utbildning i förväg, [Microsoft kognitiva Toolkit](https://www.microsoft.com/cognitive-toolkit/). Verktyget används ofta av en mängd olika Microsoft-produkter, företag över hela världen behöver distribuera djup learning i större skala och studenter som är intresserade av de senaste algoritmer och tekniker. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario – poäng bilder att identifiera mönster i Urbant utveckling

Nu ska vi se ett exempel på en pipeline som använder HDInsight för avancerade analyser av maskininlärning.

I det här scenariot visas hur DNNs genereras i en djup learning framework Microsoft har kognitiva Toolkit (CNTK), kan operationalized för resultatfunktioner stor bild samlingar som lagras i ett Azure Blob Storage-konto med hjälp av PySpark på ett HDInsight Spark-kluster. Den här metoden tillämpas på ett gemensamt DNN användningsfall Flygfoto avbildningen klassificering, och kan användas för att identifiera de senaste mönster i utveckling av städer.  Du använder en modell för klassificering av före utbildade avbildningen. Modellen är redan utbildade på den [CIFAR 10 dataset](https://www.cs.toronto.edu/~kriz/cifar.html) och har kopplats till 10 000 avdragna bilder.

Det finns tre viktiga uppgifter i det här scenariot för avancerade analyser:

1. Skapa ett Azure HDInsight Hadoop-kluster med ett Apache Spark 2.1.0-distribution. 
2. Köra ett anpassat skript för att installera Microsoft kognitiva Toolkit på alla noder i ett Azure HDInsight Spark-kluster. 
3. Överför en förskapad Jupyter-anteckningsbok till HDInsight Spark-klustret för att tillämpa en tränad Microsoft kognitiva Toolkit djup Lär modell till filer i ett Azure Blob Storage-konto med Spark Python-API (PySpark). 

Det här exemplet använder ange kompilerade och distribuerade genom Alex Krizhevsky och Vinod Nair Geoffrey Hinton CIFAR 10-avbildningen. CIFAR-10-datauppsättningen innehåller 60 000 32 × 32 färg bilder som hör till 10 ömsesidigt uteslutande klasser:

![Avbildningar](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Mer information om dataset finns Alex Krizhevsky [Learning flera lager av funktioner från små bilder](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datamängden har partitionerats till en uppsättning 50 000 bilder utbildning och en test uppsättning 10 000 bilder. Den första uppsättningen användes för att träna en 20 layer djup convolutional återstående nätverk (ResNet)-modell med Microsoft kognitiva Toolkit genom att följa [självstudierna](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) från kognitiva Toolkit GitHub-lagringsplatsen. De återstående 10 000 bilderna användes för att testa modellens precision. Det är där distribuerad datoranvändning kommer i play: aktiviteten i förväg bearbetning och bedömningen avbildningar är hög parallell. Med den sparade tränade modellen i hand använde vi:

* PySpark att distribuera avbildningar och tränade modellen på worker klusternoder.
* Python till Förbearbeta avbildningar på varje nod i HDInsight Spark-klustret.
* Kognitiva Toolkit för att läsa in modellen och poäng förbearbetade avbildningar på varje nod.
* Jupyter-anteckningsböcker för att köra skriptet PySpark sammanställa resultaten och använda [Matplotlib](https://matplotlib.org/) visualisera modellen prestanda.

Den hela förbearbetning/poängsättningen av 10 000 bilder tar mindre än en minut på ett kluster med 4 arbetsnoderna. Modellen beräknar korrekt etiketter ~ 9,100 (91%) bilder. En matris med förvirring illustrerar de vanligaste felen klassificering. Matrisen visar exempelvis att mislabeling hund som katt och vice versa sker mer ofta än för andra etikett-par.

![Resultat](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Prova!

Följ [självstudierna](../spark/apache-spark-microsoft-cognitive-toolkit.md) att implementera den här lösningen slutpunkt till slutpunkt: installera ett HDInsight Spark-kluster, installera kognitiva Toolkit och kör Jupyter-anteckningsbok som poäng 10 000 CIFAR bilder.

## <a name="next-steps"></a>Nästa steg

Hive och Azure Machine Learning

* [Hive och Azure Machine Learning slutpunkt till slutpunkt](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Med Azure HDInsight Hadoop-kluster på en datamängd för 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark och MLLib

* [Maskininlärning med Spark i HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](../spark/apache-spark-machine-learning-mllib-ipython.md)

Djupgående Learning och kognitiva Toolkit

* [Embarrassingly parallella avbildningen klassificering, med kognitiva Toolkit och TensorFlow på Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Datavetenskap virtuella Azure-datorn](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introduktion till H2O.ai på Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
