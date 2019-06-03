---
title: 'Självstudier: Skapa en klustringsmodell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här självstudieserien med tre delar skapar du en K-Means modell för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419764"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Skapa en klustringsmodell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del två av den här självstudieserien med tre delar skapar du en K-Means modell för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Ange hur många kluster för en algoritm för K-Means
> * Utföra klustring
> * Analysera resultaten

I [del ett](sql-database-tutorial-clustering-model-prepare-data.md), du har lärt dig hur du förbereder data från en Azure SQL-databas för att utföra klustring i R.

I [del tre](sql-database-tutorial-clustering-model-deploy.md), får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring baserat på nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Del två av den här självstudien förutsätter att du har slutfört [ **del ett** ](sql-database-tutorial-clustering-model-prepare-data.md) och dess krav.

## <a name="define-the-number-of-clusters"></a>Definiera antalet kluster

Om du vill klustra dina kunddata, använder du den **K-Means** klustring algoritmen, och en av de enklaste och mest välkända sätten att gruppera data.
Du kan läsa mer om K-Means i [en fullständig handbok för K-means klustring algoritmen](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmen godkänner två indata: Själva informationen och ett fördefinierat nummer ”*k*” som representerar antalet kluster att generera.
Utdata är *k* kluster med indata partitionerad mellan klustren.

Använd en rityta för att fastställa antalet kluster som använder algoritmen det inom grupper KVADRATSUMMA, genom att antalet kluster som extraheras. Det lämpliga antalet kluster som använder är på Böj eller ”vinkel” av området.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Vinkel graph](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Baserat på diagrammet, det ser ut som *k = 4* skulle vara en bra värde och försök. Att *k* värdet grupperas kunder i fyra kluster.

## <a name="perform-clustering"></a>Utföra klustring

I följande R-skriptet använder du funktionen **rxKmeans**, vilket är funktionen K-Means i RevoScaleR-paketet.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analysera resultaten

Nu när du har gjort klustring med K-Means, är nästa steg att analysera resultatet och se om du kan hitta någon användbar information.

Den **clust** objektet innehåller resultaten från K-Means klustring.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Fyra kluster innebär angivna med hjälp av variablerna definieras i [del ett](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = batch-förhållande (totalt antal beställningar helt eller delvis returnerade jämfört med det totala antalet beställningar)
* *itemsRatio* = Returnerad artikel-förhållande (totalt antal objekt som returneras jämfört med antal objekt som har köpt)
* *monetaryRatio* = returnerade belopp-förhållande (totala beloppet för objekt som returneras jämfört med den mängd som har köpt)
* *frekvens* = returnerade frekvens

Datautvinning med K-Means ofta kräver ytterligare analys av resultatet, och ytterligare steg för att bättre förstå varje kluster, men det kan ge några bra leads.
Här är några sätt som du kan tolka resultaten:

* Klustret 1 (största kluster) verkar vara en grupp av kunder som inte är aktiva (alla värden är noll).
* Klustret 3 verkar vara en grupp som står ut när det gäller returnerade beteende.

## <a name="clean-up-resources"></a>Rensa resurser

***Om du inte planerar att fortsätta med den här självstudien***, ta bort tpcxbb_1gb databasen från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **tpcxbb_1gb**, och välj din prenumeration.
1. Välj din **tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två i den här självstudieserien får slutfört du de här stegen:

* Ange hur många kluster för en algoritm för K-Means
* Utföra klustring
* Analysera resultaten

Följ del tre i självstudieserien om du vill distribuera machine learning-modell som du har skapat:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera en klustringsmodell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-clustering-model-deploy.md)