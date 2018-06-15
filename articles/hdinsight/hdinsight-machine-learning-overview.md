---
title: Översikt över Machine learning - Azure HDInsight | Microsoft Docs
description: Beskriver alternativen i HDInsight för maskininlärning.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: b80c49b67c7cca835ae08346561984e168e3df4e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392178"
---
# <a name="machine-learning-on-hdinsight"></a>Maskininlärning på HDInsight

HDInsight kan maskininlärning med stordata, vilket ger möjlighet att få värdefulla insikter från stora mängder (petabyte eller även exabyte) strukturerade, Ostrukturerade, och flytta snabbt data. Det finns flera maskininlärning alternativ i HDInsight: SparkML och MLlib, R, Hive och Microsoft kognitiva Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML och MLlib

[HDInsight Spark](spark/apache-spark-overview.md) är en Azure-baserad erbjudande av [Spark](http://spark.apache.org/), en enhetlig, öppen källa, ramverk för parallellbearbetning av data som stöder minnesintern bearbetning för att höja analyser av stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks funktioner för beräkning för distribuerade i minnet gör det ett bra alternativ för iterativa algoritmer i machine learning och grafberäkningar. Det finns två skalbara machine learning bibliotek som få algoritmbaserade modellering funktioner till den här distribuerad miljö: MLlib och SparkML. MLlib innehåller ursprungliga API byggda på RDDs. SparkML är ett nyare paket som innehåller en högre nivå API byggda på DataFrames för att konstruera ML pipelines. SparkML ännu stöder inte alla funktioner i MLlib, men ersätter MLlib som Sparks standard machine learning-biblioteket.

Microsoft Machine Learning-biblioteket för Apache Spark [MMLSpark](https://github.com/Azure/mmlspark). Det här biblioteket är utformat för att göra datavetare mer produktiv på Spark, öka mängden undersökningar och utnyttja senaste maskininlärning tekniker, inklusive djup learning på mycket stora datauppsättningar. MMLSpark tillhandahåller ett säkerhetslager ovanpå Sparkmls lågnivå-API: er när du skapar skalbara ML-modeller, t.ex fulltextindexering strängar coercing data i en layout som förväntades av machine learning algoritmer och montera funktionen angreppsmetoderna. MMLSpark bibliotek förenklar dessa och andra vanliga uppgifter för att skapa modeller i PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) för närvarande är det mest populära statistiska programmeringsspråket i världen. Det är en öppen källkod data visualiseringen verktyg med över 2,5 miljoner användare och växande. Med dess framgångsrika användarbas och än 8 000 överförda paket är R ett troligt alternativ för många företag som behöver maskininlärning. Du kan skapa ett HDInsight-kluster med R-Server som är redo att användas med stora datauppsättningar och modeller. Den här funktionen gör dataanalytiker och statistiker med ett bekant R-gränssnitt som kan skalas på begäran via HDInsight, utan extra kostnader för konfiguration och underhåll.

![Utbildning för förutsägelse med R server](./media/hdinsight-machine-learning-overview/r-training.png)

Edge-nod i ett kluster ger en lämplig plats att ansluta till klustret och köra R-skript.  Du har också möjlighet att köra R-skript mellan noder i klustret med hjälp av Scaler's Hadoop kartan minska eller compute kontexter för Spark.

Med R Server på HDInsight med Spark parallelize du utbildning mellan noder i ett kluster med hjälp av en Spark beräknings-kontext. Du kan köra R-skript direkt på kantnoden använder alla tillgängliga kärnor parallellt, vid behov. Alternativt kan du köra din kod från kantnod till startar bearbetning som är fördelade på alla noder i klustret. R Server på HDInsight med Spark kan också parallelizing funktioner från öppen källkod R-paket, om så önskas.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning och Hive

Azure Machine Learning innehåller verktyg för att modellen förutsägelseanalyser, samt en helt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som redo att använda webbtjänster. Azure Machine Learning är en fullständig förutsägelseanalys i molnet som du kan använda för att skapa, testa, operationalisera och hantera förutsägelsemodeller. Välj ett stort algoritmbibliotek, använder en webbaserad studio för att skapa modeller och enkelt distribuera din modell som en webbtjänst.

![Gör avancerade analyser tillgänglig till Hadoop med Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Skapa funktioner för data i ett HDInsight Hadoop-kluster med hjälp av [Hive frågor](../machine-learning/team-data-science-process/create-features-hive.md). *Egenskapsval* försöker öka förutsägande kraften i learning algoritmer genom att skapa funktioner från rådata som underlättar learning-processen. Du kan köra HiveQL frågor från Azure ML och komma åt data som bearbetas i Hive och lagras i blob storage med hjälp av den [importera Data modulen](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Djupgående learning](https://www.microsoft.com/en-us/research/group/dltc/) är en del av maskininlärning som använder neurala nätverk, inspirerat av mänsklig hjärna biologiska processer. Djupgående learning finns i många forskare som ett Orderlöfte tillvägagångssätt för att utöka styrs av datorn. Exempel på djup learning är talas översättare, bild recognition system och motivationen för datorn.

För att i förväg arbetet i djup learning Microsoft har utvecklat gratis, enkelt att använda, öppen källkod [Microsoft kognitiva Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Dessa verktyg som används av en mängd olika Microsoft-produkter, företag över hela världen behöver distribuera djup learning i större skala och studenter som är intresserade av de senaste algoritmer och tekniker. 

## <a name="see-also"></a>Se också

### <a name="scenarios"></a>Scenarier

* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generera filmrekommendationer med Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive och Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive och Azure Machine Learning slutpunkt till slutpunkt](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Maskininlärning med Spark i HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Djupgående utbildningsresurser

* [Djupgående learning toolkit med Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Embarrassingly parallella avbildningen klassificering med kognitiva toolkit + Tensorflow på Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
