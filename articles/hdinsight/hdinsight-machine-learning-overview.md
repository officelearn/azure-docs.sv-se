---
title: Översikt över maskininlärning – Azure HDInsight
description: Beskriver de machine learning-alternativ i HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: 7e5f56251e00b59a6e6d3be40612f4c8e381dff1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743008"
---
# <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight gör det möjligt för machine learning med big data, vilket ger möjlighet att få värdefulla insikter från stora mängder (petabyte eller även exabyte) strukturerade och Ostrukturerade, och snabbrörliga data. Det finns flera machine learning-alternativ i HDInsight:  SparkML och Apache Spark MLlib, R, Apache Hive och Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML och MLlib

[HDInsight Spark](spark/apache-spark-overview.md) är ett erbjudande för Azure som värd för [Apache Spark](https://spark.apache.org/), en enhetlig öppen källkod, ett parallellt databearbetningsramverk som stöder intern bearbetning för att höja analyser av stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks InMemory-distribuerad beräkning funktioner blir det ett bra alternativ för iterativa algoritmer som används i machine learning och grafberäkningar. Det finns två skalbar machine learning-bibliotek som få algoritmiska modelleringsfunktioner till den här distribuerad miljö: MLlib och SparkML. MLlib innehåller den ursprungliga API som byggts ovanpå rdd-datauppsättningar. SparkML är ett nyare paket som innehåller en högre nivå API som bygger på dataramar för att konstruera ML pipelines. SparkML stöder ännu inte alla funktioner i MLlib, men ersätter MLlib som Spark's standard machine learning-biblioteket.

Microsoft Machine Learning-biblioteket för Apache Spark [MMLSpark](https://github.com/Azure/mmlspark). Det här biblioteket är utformad för att göra dataexperter mer produktiva i Spark, öka frekvensen för experimentering och utnyttja banbrytande machine learning-tekniker, inklusive djupinlärning på mycket stora datauppsättningar. MMLSpark ger ett lager ovanpå Sparkmls på låg nivå API: er när du bygger skalbara ML-modeller, t.ex indexering strängar coercing data i en layout som förväntas av machine learning-algoritmer och montera funktionen vektorer. MMLSpark-bibliotek förenklar dessa och andra vanliga aktiviteter för att skapa modeller i PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) för närvarande är det mest populära statistiska programmeringsspråket i världen. Det är en öppen källkod datavisualiseringsverktyg med grupper med över 2,5 miljoner användare och växer. R är ett troligt alternativ för många företag som behöver maskininlärning med dess blomstrande användarbas och över 8 000 överförda paket. Du kan skapa ett HDInsight-kluster med ML-tjänster som är redo att användas med enorma datauppsättningar och modeller. Den här funktionen ger dataexperter och statistiker med ett välbekanta R-gränssnitt som kan skalas på begäran via HDInsight, utan att behöva konfiguration och underhåll.

![Utbildning för förutsägelse med R server](./media/hdinsight-machine-learning-overview/r-training.png)

Edge-nod i ett kluster är ett bra ställe att ansluta till klustret och köra R-skript.  Du har också möjlighet att köra R-skript för noder i klustret med hjälp av Scaler's Hadoop Map Reduce eller Spark compute-sammanhang.

Med ML-tjänster på HDInsight med Spark, kan du parallellisera utbildning för noder i ett kluster med hjälp av ett Spark-beräkningskontexten. Du kan köra R-skript direkt på edge-nod med hjälp av alla tillgängliga kärnor samtidigt, efter behov. Du kan också köra din kod från gränsnoden av bearbetning som är fördelade på alla noder i klustret. ML-tjänster på HDInsight med Spark kan också parallellisera funktioner från R med öppen källkod-paket, om så önskas.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning och Apache Hive

Azure Machine Learning tillhandahåller verktyg för att modellen förutsägande analys, samt en helt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som är klara att använda webbtjänster. Azure Machine Learning är en fullständig förutsägelseanalys i molnet som du kan använda för att skapa, testa, operationalisera och hantera förutsägelsemodeller. Välj från ett stort algoritmbibliotek, använda webbaserade verktyg för modellutveckling och enkelt distribuera din modell som en webbtjänst.

![Att göra avancerade analyser som är tillgänglig för Hadoop med Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Skapa funktioner för data i ett HDInsight Hadoop-kluster med hjälp av [Hive-frågor](../machine-learning/team-data-science-process/create-features-hive.md). *Funktionstekniker* försöker ökar förutsägande learning-algoritmer genom att skapa funktioner från rådata som underlättar learning processen. Du kan köra HiveQL frågor från Azure ML och komma åt data som bearbetas i Hive och lagras i blob storage med hjälp av den [importdata modulen](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Djupinlärning](https://www.microsoft.com/en-us/research/group/dltc/) är en gren av machine learning som använder neurala nätverk, bli INSPIRERAD av bioteknik processer i den mänskliga hjärnan. Djupinlärning finns som en lovande metod för att utöka AI många forskare. Exempel på djupinlärning är talat språk översättare, bild av system och datorn resonemang.

Microsoft har utvecklat kostnadsfria, enkel att använda, öppen källkod på för att gå vidare sitt eget arbete i djupinlärning, [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Dessa verktyg används av en mängd olika Microsoft-produkter, företag över hela världen med ett behov av att distribuera djupinlärnings i stor skala och studenter som är intresserade av senaste algoritmer och tekniker. 

## <a name="see-also"></a>Se också

### <a name="scenarios"></a>Scenarier

* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Skapa filmrekommendationer med Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive och Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive och Azure Machine Learning slutpunkt till slutpunkt](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine learning med Apache Spark i HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep learning-resurser

* [Djupinläranede verktygspaket med Apache Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Embarrassingly parallel bildklassificering med Cognitive toolkit + Tensorflow i Apache Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
