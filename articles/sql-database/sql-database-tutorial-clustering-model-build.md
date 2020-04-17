---
title: 'Självstudiekurs: Skapa en klustermodell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här självstudieserien i tre delar skapar du en K-Means-modell för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453105"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudiekurs: Skapa en klustermodell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del två av den här självstudieserien i tre delar ska du skapa en K-Means-modell i R för att utföra klustring. I nästa del av den här serien distribuerar du den här modellen i en SQL-databas med Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Definiera antalet kluster för en K-Means-algoritm
> * Utföra klustring
> * Analysera resultaten

I [del ett](sql-database-tutorial-clustering-model-prepare-data.md)lärde du dig hur du förbereder data från en Azure SQL-databas för att utföra klustring.

I [del tre](sql-database-tutorial-clustering-model-deploy.md)får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring i R baserat på nya data.

## <a name="prerequisites"></a>Krav

* Del två av den här självstudien förutsätter att du har slutfört [**del ett**](sql-database-tutorial-clustering-model-prepare-data.md) och dess förutsättningar.

## <a name="define-the-number-of-clusters"></a>Definiera antalet kluster

Om du vill samla dina kunddata använder du **K-Means-klusteralgoritmen,** ett av de enklaste och mest kända sätten att gruppera data.
Du kan läsa mer om K-Means i [en komplett guide till K-medel klustring algoritm](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmen accepterar två indata: Själva data och ett fördefinierat tal "*k*" som representerar antalet kluster att generera.
Utdata är *k-kluster* med indata som är partitionerade mellan klustren.

Om du vill bestämma antalet kluster som algoritmen ska använda använder du ett område för inomgruppssumman för kvadrater, efter antal kluster som extraherats. Det lämpliga antalet kluster som ska användas är vid böjen eller "armbågen" i området.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Diagram över armbåge](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Baserat på diagrammet, det ser ut som *k = 4* skulle vara ett bra värde att prova. Det *k-värdet* grupperar kunderna i fyra kluster.

## <a name="perform-clustering"></a>Utföra klustring

I följande R-skript ska du använda funktionen **rxKmeans**, som är funktionen K-Means i Paketet RevoScaleR.

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

**Clust-objektet** innehåller resultaten från K-Means-klustring.

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

De fyra klustermedelen anges med hjälp av de variabler som definieras i [del ett:](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)

* *orderRatio* = returorderförhållande (totalt antal order som helt eller delvis returnerats jämfört med det totala antalet order)
* *itemsRatio* = returartikelförhållande (totalt antal returnerade artiklar jämfört med antalet inköpta artiklar)
* *monetaryRatio* = avkastningsbelopp (totalt monetärt belopp för poster som returneras jämfört med det köpta beloppet)
* *frekvens* = returfrekvens

Datautvinning med K-Means kräver ofta ytterligare analys av resultaten och ytterligare steg för att bättre förstå varje kluster, men det kan ge några bra leads.
Här är några sätt du kan tolka dessa resultat:

* Kluster 1 (det största klustret) verkar vara en grupp kunder som inte är aktiva (alla värden är noll).
* Cluster 3 verkar vara en grupp som sticker ut när det gäller returbeteende.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med den ***här självstudien***tar du bort tpcxbb_1gb-databasen från din Azure SQL Database-server.

Gör så här på Azure-portalen:

1. Välj **Alla resurser** eller **SQL-databaser**på menyn till vänster i Azure-portalen .
1. I fältet **Filtrera efter namn...** anger du **tpcxbb_1gb**och väljer din prenumeration.
1. Välj **din tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudieserien har du slutfört följande steg:

* Definiera antalet kluster för en K-Means-algoritm
* Utföra klustring
* Analysera resultaten

Om du vill distribuera den maskininlärningsmodell som du har skapat följer du del tre i den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera en klustermodell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-clustering-model-deploy.md)