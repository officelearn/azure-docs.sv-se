---
title: "HDInsight Spark genomgång med hjälp av PySpark och Scala i Azure | Microsoft Docs"
description: "Exempel på Team av vetenskapliga data som beskriver genom att använda PySpark och Scala på ett Azure HDInsight Spark att göra förutsägelseanalyser."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 99b9f047525f46f68dc2c1029f6d53fbda87b69e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Med hjälp av PySpark och Scala på Azure HDInsight Spark datavetenskap genomgång

Dessa genomgång använda PySpark och Scala på en Azure Spark-klustret för att göra förutsägelseanalyser. De följer stegen som beskrivs i Team av vetenskapliga data. En översikt över Team av vetenskapliga data, se [datavetenskap Process](overview.md). En översikt över Spark i HDInsight, se [introduktion till Spark i HDInsight](../../hdinsight/hdinsight-apache-spark-overview.md).

Ytterligare datavetenskap genomgångar som kör Team datavetenskap Process grupperas efter den **plattform** som de använder. Se [genomgång köra Team av vetenskapliga data](walkthroughs.md) för en specificering av de här exemplen.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Förutsäga taxi tips med hjälp av PySpark på Azure Spark

Den [använda Spark på Azure HDInsight](spark-overview.md) genomgången använder data från New York taxibilar för att förutsäga om ett tips är betald och intervallet för belopp förväntas vara betald. Den använder teamet vetenskap av data i ett scenario med en [Azure HDInsight Spark-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, och funktionen tekniker data från offentligt tillgängliga NYC taxi resan och färdavgiften dataset. Det här översiktsavsnittet ställer du in med ett HDInsight Spark-kluster och Jupyter PySpark anteckningsböcker som används i resten av den här genomgången. Dessa anteckningsböcker visar hur du utforska dina data och hur du skapar och använda modeller. Avancerade undersökning och modellering bärbar dator visar hur du inkludera korsvalidering, hyper-parametern omfattande, och modellerar utvärdering.

### <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark 
Utforska datauppsättningen och skapa poängsätta och utvärdera modeller för maskininlärning genom att utföra den [skapa binär klassificering och regression modeller för data med Spark MLlib toolkit](spark-data-exploration-modeling.md) avsnittet.

### <a name="model-consumption"></a>Modell-förbrukning
Information om hur du poängsätta klassificering och regression modeller som skapats i det här avsnittet finns [poängsätta och utvärdera Spark-inbyggda machine learning-modeller](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Korsvalidering och hyperparameter omfattande
Se [avancerade datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) på hur modeller kan vara tränas med omfattande korsvalidering och hyper-parametern.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Förutsäga taxi tips på Azure Spark Scala

Den [Använd Scala med Spark på Azure](scala-walkthrough.md) genomgången använder data från New York taxibilar för att förutsäga om ett tips är betald och intervallet för belopp förväntas vara betald. Den visar hur du använder Scala för övervakade maskininlärning uppgifter med Spark-machine learning-biblioteket (MLlib) och SparkML paket i ett Azure HDInsight Spark-kluster. Den vägleder dig igenom de aktiviteter som utgör den [datavetenskap Process](http://aka.ms/datascienceprocess): datapåfyllning och undersökning, visualisering, funktionen tekniker, modellering och förbrukning av modellen. Modeller som skapats är logistic och linjär regression, slumpmässiga skogar och toning ökat träd.


## <a name="next-steps"></a>Nästa steg

En beskrivning av de viktiga komponenter som utgör teamet datavetenskap Process finns [Team datavetenskap processöversikt](overview.md).

En beskrivning av livscykeln Team av vetenskapliga data som du kan använda för att strukturera datavetenskap projekt finns [Team datavetenskap Process livscykel](lifecycle.md). Livscykeln beskrivs stegen från början till slut att projekt vanligtvis följer när de utförs. 

