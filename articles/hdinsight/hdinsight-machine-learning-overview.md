---
title: Översikt över maskininlärning – Azure HDInsight
description: Översikt över verktygsinlärningsalternativ för stordata för kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931669"
---
# <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight möjliggör maskininlärning med stordata, vilket ger möjlighet att få värdefull insikt från stora mängder (petabyte, eller till och med exabyte) av strukturerade, ostrukturerade och snabbrörliga data. Det finns flera maskininlärningsalternativ i HDInsight: SparkML och Apache Spark MLlib, R, Apache Hive och Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML och MLlib

[HDInsight Spark](spark/apache-spark-overview.md) är ett Azure-värdbaserat erbjudande av [Apache Spark](https://spark.apache.org/), ett enhetligt, ramverk för parallell databearbetning med öppen källkod som stöder bearbetning i minnet för att öka analys av stordata. Spark-bearbetningsmotorn är byggd för hastighet, användarvänlighet och sofistikerad analys. Sparks distribuerade beräkningsfunktioner i minnet gör det till ett bra val för de iterativa algoritmer som används vid maskininlärning och grafberäkningar. Det finns två skalbara maskininlärningsbibliotek som ger algoritmiska modelleringsfunktioner till den här distribuerade miljön: MLlib och SparkML. MLlib innehåller det ursprungliga API:et som bygger på rdd:er. SparkML är ett nyare paket som tillhandahåller ett API på högre nivå som bygger ovanpå DataFrames för att konstruera ML-pipelines. SparkML stöder ännu inte alla funktioner i MLlib, men ersätter MLlib som Sparks standardbibliotek för maskininlärning.

Microsoft Machine Learning-biblioteket för Apache Spark är [MMLSpark](https://github.com/Azure/mmlspark). Det här biblioteket är utformat för att göra datavetare mer produktiva på Spark, öka antalet experiment och utnyttja banbrytande maskininlärningstekniker, inklusive djupinlärning, på mycket stora datamängder. MMLSpark tillhandahåller ett lager ovanpå SparkML:s api:er på låg nivå när du skapar skalbara ML-modeller, till exempel indexeringssträngar, tvingar in data i en layout som förväntas av maskininlärningsalgoritmer och montering av funktionsvektorer. MMLSpark-biblioteket förenklar dessa och andra vanliga uppgifter för att bygga modeller i PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) är för närvarande det mest populära statistiska programmeringsspråket i världen. Det är ett datavisualiseringsverktyg med öppen källkod med en community med över 2,5 miljoner användare och växande. Med sin blomstrande användarbas och över 8 000 bidragssatta paket är R ett troligt val för många företag som behöver maskininlärning. Du kan skapa ett HDInsight-kluster med ML Services redo att användas med massiva datauppsättningar och modeller. Den här funktionen ger dataforskare och statistiker ett välbekant R-gränssnitt som kan skalas på begäran via HDInsight, utan att klusterinställningar och underhåll finns i belastningen.

![Utbildning för förutsägelse med R-server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Kantnoden i ett kluster är en praktisk plats för att ansluta till klustret och köra dina R-skript.  Du kan också köra R-skript över noderna i klustret med hjälp av ScaleR:s Hadoop-karta minska eller gnista beräkningskontexter.

Med ML Services på HDInsight med Spark kan du parallellisera träningen över noderna i ett kluster med hjälp av en Spark-beräkningskontext. Du kan köra R-skript direkt på kantnoden med alla tillgängliga kärnor parallellt, efter behov. Alternativt kan du köra koden från kantnoden för att starta bearbetning som distribueras över alla noder i klustret. ML Services på HDInsight med Spark möjliggör också parallellisering av funktioner från R-paket med öppen källkod, om så önskas.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning och Apache Hive

Azure Machine Learning innehåller verktyg för att modellera prediktiv analys och en fullständigt hanterad tjänst som du kan använda för att distribuera dina prediktiva modeller som färdiga att använda webbtjänster. Azure Machine Learning är en komplett prediktiv analyslösning i molnet som du kan använda för att skapa, testa, operationalisera och hantera prediktiva modeller. Välj från ett stort algoritmbibliotek, använd en webbaserad studio för att bygga modeller och distribuera enkelt din modell som en webbtjänst.

![Översikt över Microsoft Azure-maskininlärning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Skapa funktioner för data i ett HDInsight Hadoop-kluster med [Hive-frågor](../machine-learning/team-data-science-process/create-features-hive.md). *Funktionsteknik försöker* öka den prediktiva kraften i inlärningsalgoritmer genom att skapa funktioner från rådata som underlättar inlärningsprocessen. Du kan köra HiveQL-frågor från Azure Machine Learning Studio (klassisk) och komma åt data som bearbetas i Hive och lagras i blob-lagring med hjälp av [modulen Importera data](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Djupinlärning](https://www.microsoft.com/en-us/research/group/dltc/) är en gren av maskininlärning som använder neurala nätverk, inspirerade av den mänskliga hjärnans biologiska processer. Många forskare ser djupinlärning som en lovande metod för att förbättra artificiell intelligens. Exempel på djupinlärning är översättare i talade språk, bildigenkänningssystem och maskinrekonstruktion.

För att främja sitt eget arbete inom djupinlärning utvecklade Microsoft den kostnadsfria, lätt att använda [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)med öppen källkod . Den här verktygslådan används av en mängd olika Microsoft-produkter, av företag över hela världen med ett behov av att distribuera djupinlärning i stor skala och av studenter som är intresserade av de senaste algoritmerna och teknikerna.

## <a name="see-also"></a>Se även

### <a name="scenarios"></a>Scenarier

* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generera filmrekommendationer med Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive och Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive och Azure Machine Learning från på distans](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Maskininlärning med Apache Spark på HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Resurser för djupinlärning

* [Använda Microsoft Cognitive Toolkit deep learning-modell med Azure HDInsight Spark-kluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning och AI-ramverk på Virtual Machine (Data Science)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
