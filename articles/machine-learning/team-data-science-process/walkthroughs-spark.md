---
title: Analys på HDInsight Spark med PySpark, Scala - Team Data Science Process
description: Exempel på teamdatascience-processen som går igenom användningen av PySpark och Scala på en Azure HDInsight Spark.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864153"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark datavetenskap genomgångar med PySpark och Scala på Azure

Dessa genomgångar använder PySpark och Scala på ett Azure Spark-kluster för att göra prediktiva analyser. De följer stegen som beskrivs i teamdatavetenskapsprocessen. En översikt över teamdatavetenskapsprocessen finns i [Data Science Process](overview.md). En översikt över Spark på HDInsight finns i [Introduktion till Spark på HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Ytterligare genomgångar av datavetenskap som kör Team Data Science Process grupperas efter den **plattform** som de använder. Se [Genomgångar som kör Team Data Science Process](walkthroughs.md) för en specificering av dessa exempel.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Förutse taxitips med PySpark på Azure Spark

Med hjälp av New York-taxidata förutsäger [genomgången Använd Spark på Azure HDInsight](spark-overview.md) om ett tips betalas och intervallet för förväntade belopp. I det här exemplet används Team Data Science Process i ett scenario med hjälp av ett [Azure HDInsight Spark-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och funktionsingenjörsdata från den allmänt tillgängliga NYC-taxiresan och biljettdatauppsättningen. I det här översiktsavsnittet används ett HDInsight Spark-kluster och jupyter PySpark-anteckningsböcker. Dessa anteckningsböcker visar hur du utforskar dina data och sedan hur du skapar och konsumerar modeller. Den avancerade datautforskningen och modelleringsda anteckningsboken visar hur du inkluderar korsvalidering, hyperparametersvepning och modellutvärdering.

### <a name="data-exploration-and-modeling-with-spark"></a>Datautforskning och modellering med Spark 
Utforska datauppsättningen och skapa, poäng och utvärdera maskininlärningsmodellerna genom att arbeta igenom [modellerna Skapa binär klassificering och regression för data med Spark MLlib-verktygslådan.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Modellförbrukning
Mer information om hur du poängar de klassificerings- och regressionsmodeller som skapats i det här avsnittet finns i [Betygsätta och utvärdera Spark-byggda maskininlärningsmodeller](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Korsvalidering och hyperparametersvepning
Se [Avancerad datautforskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med korsvalidering och hyperparametersvepning.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Förutse taxitips med Scala på Azure Spark

[Genom att använda Scala med Spark på Azure-genomgången](scala-walkthrough.md) förutses om ett tips betalas och intervallet belopp som förväntas betalas. Den visar hur du använder Scala för övervakade maskininlärningsuppgifter med Spark machine learning-biblioteket (MLlib) och SparkML-paketen i ett Azure HDInsight Spark-kluster. Den går igenom de uppgifter som utgör [data science-processen:](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)datainmatning och utforskning, visualisering, funktionsteknik, modellering och modellförbrukning. Modellerna som byggs inkluderar logistisk och linjär regression, slumpmässiga skogar och lutningsbebogda träd.


## <a name="next-steps"></a>Nästa steg

En översikt över teamdatavetenskapsprocessen finns i [översikt över teamdatavetenskapsprocess](overview.md).

En diskussion om livscykeln för teamdatavetenskapsprocessen finns i [livscykeln](lifecycle.md)för Team Data Science Process . Den här livscykeln beskriver de steg, från början till, som projekt vanligtvis följer när de körs. 

