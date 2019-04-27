---
title: Analytics för HDInsight Spark med PySpark, Scala - Team Data Science Process
description: Exempel på Team Data Science Process som går genom att använda PySpark och Scala i ett Azure HDInsight Spark att göra förutsägelseanalyser.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ce57c37791f9fcf353d08e177eb243f4939b52a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805523"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data science genomgångar med PySpark och Scala på Azure

PySpark och Scala använder på ett Azure Spark-kluster dessa genomgångar för att göra förutsägelseanalyser. De följer du anvisningarna i Team Data Science Process. En översikt över Team Data Science Process, se [Data Science Process](overview.md). En översikt över Apache Spark i HDInsight finns i [introduktion till Spark i HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Ytterligare data science genomgångar som kör Team Data Science Process är grupperade efter den **plattform** som de använder. Se [genomgångar kör Team Data Science Process](walkthroughs.md) för en specifikation av de här exemplen.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Förutsäg taxitips med PySpark på Azure Spark

Den [använda Spark på Azure HDInsight](spark-overview.md) genomgången använder data från New York-taxibilar för att förutsäga om ett tips är betalas ut och intervallet för mängder förväntas betalas. Den använder Team Data Science Process i ett scenario med en [Azure HDInsight Spark-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, och funktion engineer data från offentligt tillgängliga NYC taxi resa och färdavgiften datauppsättning. Det här översiktsavsnittet ställer du in med ett HDInsight Spark-kluster och Jupyter PySpark anteckningsböcker som används i resten av den här genomgången. Dessa anteckningsböcker visar hur du utforska dina data och sedan hur du skapar och använda modeller. Avancerade utforskning och modellering anteckningsboken visar hur du inkluderar korsvalidering, hyper-parametern oinskränkt, och modellera utvärdering.

### <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark 
Utforska datauppsättningen och skapa, poäng och utvärdera maskininlärningsmodeller genom att utföra den [skapa binär klassificering och regression modeller för data med Spark MLlib toolkit](spark-data-exploration-modeling.md) avsnittet.

### <a name="model-consumption"></a>Modell-förbrukning
Läs hur du bedöma klassificerings- och regressionsmodeller modeller som skapats i det här avsnittet i [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Korsvalidering och finjustering oinskränkt
Se [avancerad datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med hjälp av oinskränkt korsvalidering och hyper-parametern.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Förutsäg taxitips med hjälp av Scala på Azure Spark

Den [använda Scala med Spark på Azure](scala-walkthrough.md) genomgången använder data från New York-taxibilar för att förutsäga om ett tips är betalas ut och intervallet för mängder förväntas betalas. Den visar hur du använder Scala för övervakade machine learning-uppgifter med de Spark machine learning-biblioteket (MLlib) och SparkML paket på ett Azure HDInsight Spark-kluster. Vi går igenom de aktiviteter som utgör den [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): datainmatning och utforskning, visualisering, funktionsframställning, modellering och förbrukning av modellen. Modeller som byggts är logistic och linjär regression, slumpmässiga skogar och gradient bättre träd.


## <a name="next-steps"></a>Nästa steg

En beskrivning av de viktigaste komponenterna som utgör Team Data Science Process finns i [Team Data Science Process översikt](overview.md).

En beskrivning av livscykeln för Team Data Science Process som du kan använda för att strukturera dina dataforskningsprojekt finns [Team Data Science Process-livscykeln](lifecycle.md). Livscykeln beskrivs stegen, från början till slut att projekt vanligtvis följer när de utförs. 

