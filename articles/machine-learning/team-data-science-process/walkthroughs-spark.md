---
title: Analytics för HDInsight Spark med PySpark, Scala - Team Data Science Process
description: Exempel på en grupp data vetenskaps process som går igenom användningen av PySpark och Scala på en Azure HDInsight Spark.
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
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864153"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data science genomgångar med PySpark och Scala på Azure

PySpark och Scala använder på ett Azure Spark-kluster dessa genomgångar för att göra förutsägelseanalyser. De följer du anvisningarna i Team Data Science Process. En översikt över Team Data Science Process, se [Data Science Process](overview.md). En översikt över Apache Spark i HDInsight finns i [introduktion till Spark i HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Ytterligare data science genomgångar som kör Team Data Science Process är grupperade efter den **plattform** som de använder. Se [genomgångar kör Team Data Science Process](walkthroughs.md) för en specifikation av de här exemplen.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Förutsäg taxitips med PySpark på Azure Spark

Med New York taxi-data, förutsäger [use Spark på Azure HDInsight](spark-overview.md) -genom gången om ett tips betalas ut och förväntat belopp. I det här exemplet används team data vetenskaps processen i ett scenario med ett [Azure HDInsight Spark-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och tillhandahålla tekniker data från den allmänt tillgängliga NYC taxi-resan och pris data uppsättningen. I det här översikts avsnittet används ett HDInsight Spark-kluster och Jupyter PySpark-anteckningsböcker. Dessa anteckningsböcker visar hur du utforska dina data och sedan hur du skapar och använda modeller. Avancerade utforskning och modellering anteckningsboken visar hur du inkluderar korsvalidering, hyper-parametern oinskränkt, och modellera utvärdering.

### <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark 
Utforska datauppsättningen och skapa, poäng och utvärdera maskininlärningsmodeller genom att utföra den [skapa binär klassificering och regression modeller för data med Spark MLlib toolkit](spark-data-exploration-modeling.md) avsnittet.

### <a name="model-consumption"></a>Modell-förbrukning
Läs hur du bedöma klassificerings- och regressionsmodeller modeller som skapats i det här avsnittet i [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Korsvalidering och finjustering oinskränkt
Se [avancerad datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med hjälp av oinskränkt korsvalidering och hyper-parametern.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Förutsäg taxitips med hjälp av Scala på Azure Spark

I genom gången [Använd Scala med Spark på Azure](scala-walkthrough.md) förutsägs om ett tips betalas ut och intervallet av de belopp som förväntas betalas. Den visar hur du använder Scala för övervakade machine learning-uppgifter med de Spark machine learning-biblioteket (MLlib) och SparkML paket på ett Azure HDInsight Spark-kluster. Vi går igenom de aktiviteter som utgör den [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): datainmatning och utforskning, visualisering, funktionsframställning, modellering och förbrukning av modellen. Modeller som byggts är logistic och linjär regression, slumpmässiga skogar och gradient bättre träd.


## <a name="next-steps"></a>Nästa steg

En översikt över team data science-processen finns i [Översikt över team data science-processen](overview.md).

En beskrivning av livs cykeln för team data science process finns i [livs cykeln för team data science process](lifecycle.md). Den här livs cykeln beskriver stegen, från början till slut, som vanligt vis följer de projekt som körs. 

