---
title: 'Självstudie: bygga en kluster modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här själv studie serien i tre delar skapar du en K-metod modell för att utföra klustring i R med Azure SQL Database Machine Learning Services (för hands version).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453105"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudie: bygga en kluster modell i R med Azure SQL Database Machine Learning Services (förhands granskning)

I del två av den här själv studie serien i tre delar skapar du en K-medels modell i R för att utföra klustring. I nästa del av serien distribuerar du den här modellen i en SQL-databas med Azure SQL Database Machine Learning Services (för hands version).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Definiera antalet kluster för en K-= algoritm
> * Utför klustring
> * Analysera resultaten

I [del ett](sql-database-tutorial-clustering-model-prepare-data.md)har du lärt dig hur du förbereder data från en Azure SQL-databas för att utföra klustring.

I [del tre](sql-database-tutorial-clustering-model-deploy.md)får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring i R baserat på nya data.

## <a name="prerequisites"></a>Krav

* Del två i den här självstudien förutsätter att du har slutfört [**del en**](sql-database-tutorial-clustering-model-prepare-data.md) och dess krav.

## <a name="define-the-number-of-clusters"></a>Definiera antalet kluster

Om du vill klustra dina kund data använder du algoritmen för **medel-** till-kluster, ett av de enklaste och mest välkända sätten att gruppera data.
Du kan läsa mer om K-medel i [en fullständig guide till en algoritm för att köra kluster](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmen accepterar två indata: själva data och ett fördefinierat nummer "*k*" som representerar antalet kluster som ska genereras.
Utdatan är *k* kluster med indata som partitioneras bland klustren.

Om du vill fastställa antalet kluster för algoritmen som ska användas, använder du en observations kurva inom grupper summan av rutorna efter antalet extraherade kluster. Det korrekta antalet kluster som ska användas finns i ritytans böjning eller "vinkel".

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Vinklat diagram](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Baserat på diagrammet ser det ut som *k = 4* är ett bra värde att prova. Det *k* värdet kommer att gruppera kunderna i fyra kluster.

## <a name="perform-clustering"></a>Utför klustring

I följande R-skript använder du funktionen **rxKmeans**, som är funktionen K-medel i RevoScaleR-paketet.

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

Nu när du har utfört klustren med hjälp av K--medel är nästa steg att analysera resultatet och se om du kan hitta information som kan åtgärdas.

**Clust** -objektet innehåller resultatet från K-innebär klustring.

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

De fyra klustren innebär att använda de variabler som definieras i [del ett](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = retur order kvot (totalt antal beställningar som delvis eller helt returnerade jämfört med det totala antalet beställningar)
* *itemsRatio* = returnera objekts kvot (totalt antal returnerade objekt jämfört med antalet köpta objekt)
* *monetaryRatio* = retur belopps kvot (total belopps summa för artiklar som returneras jämfört med det belopp som köpts)
* *frekvens* = retur frekvens

Data utvinning med hjälp av K-medel kräver ofta ytterligare analys av resultaten och ytterligare steg för att bättre förstå varje kluster, men det kan ge en del bra leads.
Här är några sätt som du kan tolka följande resultat:

* Kluster 1 (det största klustret) verkar vara en grupp Kunder som inte är aktiva (alla värden är noll).
* Kluster 3 verkar vara en grupp som motsvarar retur beteendet.

## <a name="clean-up-resources"></a>Rensa resurser

***Om du inte kommer att fortsätta med den här självstudien tar du***bort tpcxbb_1gb-databasen från Azure SQL Database-servern.

Följ de här stegen i Azure Portal:

1. Välj **alla resurser** eller **SQL-databaser**på den vänstra menyn i Azure Portal.
1. I fältet **Filtrera efter namn...** anger du **tpcxbb_1gb**och väljer din prenumeration.
1. Välj din **tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två av den här själv studie serien slutförde du följande steg:

* Definiera antalet kluster för en K-= algoritm
* Utför klustring
* Analysera resultaten

Om du vill distribuera Machine Learning-modellen som du har skapat följer du del tre i den här själv studie serien:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera en kluster modell i R med Azure SQL Database Machine Learning Services (förhands granskning)](sql-database-tutorial-clustering-model-deploy.md)