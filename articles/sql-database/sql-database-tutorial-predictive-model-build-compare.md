---
title: 'Handledning: Träna och jämföra prediktiva modeller i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här självstudieserien i tre delar skapar du två förutsägande modeller i R med Azure SQL Database Machine Learning Services (förhandsversion) och väljer sedan den mest exakta modellen.
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
ms.date: 07/26/2019
ms.openlocfilehash: 52f4347fa545df88f11878709b4061662d5faffe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345740"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudiekurs: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del två av den här självstudieserien i tre delar skapar du två prediktiva modeller i R och väljer den mest exakta modellen. I nästa del av den här serien distribuerar du den här modellen i en SQL-databas med Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Träna två maskininlärningsmodeller
> * Göra förutsägelser från båda modellerna
> * Jämför resultaten för att välja den mest exakta modellen

I [del ett](sql-database-tutorial-predictive-model-prepare-data.md)lärde du dig hur du importerar en exempeldatabas och förbereder sedan de data som ska användas för att träna en prediktiv modell i R.

I [del tre](sql-database-tutorial-predictive-model-deploy.md)får du lära dig att lagra modellen i en databas och sedan skapa lagrade procedurer från R-skript som du har utvecklat i del ett och två. De lagrade procedurerna körs i en SQL-databas för att göra förutsägelser baserat på nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

* Del två av den här självstudien förutsätter att du har slutfört [**del ett**](sql-database-tutorial-predictive-model-prepare-data.md) och dess förutsättningar.

## <a name="train-two-models"></a>Träna två modeller

För att hitta den bästa modellen för skiduthyrningsdata, skapa två olika modeller (linjär regression och beslutsträd) och se vilken som förutspår mer exakt. Du ska använda dataramen `rentaldata` som du skapade i del ett av den här serien.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Göra förutsägelser från båda modellerna

Använd en prediktningsfunktion för att förutsäga antalet hyror med hjälp av varje tränad modell.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Jämför resultaten

Nu vill du se vilka av modellerna som ger de bästa förutsägelserna. Ett snabbt och enkelt sätt att göra detta är att använda en grundläggande plottningsfunktion för att visa skillnaden mellan de faktiska värdena i dina träningsdata och de förväntade värdena.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Jämföra de två modellerna](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Det ser ut som beslutet trädmodellen är mer exakt av de två modellerna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med den här självstudien tar du bort TutorialDB-databasen från din Azure SQL Database-server.

Gör så här på Azure-portalen:

1. Välj **Alla resurser** eller **SQL-databaser**på menyn till vänster i Azure-portalen .
1. I fältet **Filter efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudieserien har du slutfört följande steg:

* Träna två maskininlärningsmodeller
* Göra förutsägelser från båda modellerna
* Jämför resultaten för att välja den mest exakta modellen

Om du vill distribuera den maskininlärningsmodell som du har skapat följer du del tre i den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-predictive-model-deploy.md)
