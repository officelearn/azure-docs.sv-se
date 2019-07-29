---
title: 'Självstudier: Träna och jämför förutsägelse modeller i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här själv studie serien i tre delar skapar du två förutsägande modeller i R med Azure SQL Database Machine Learning Services (förhands granskning) och väljer sedan den mest exakta modellen.
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
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596807"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Skapa en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)

I del två av den här själv studie serien i tre delar skapar du två förutsägande modeller i R och väljer den mest exakta modellen. I nästa del av serien distribuerar du den här modellen i en SQL-databas med Azure SQL Database Machine Learning Services (för hands version).

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Träna två Machine Learning-modeller
> * Gör förutsägelser från båda modellerna
> * Jämför resultaten för att välja den mest exakta modellen

I [del ett](sql-database-tutorial-predictive-model-prepare-data.md)har du lärt dig hur du importerar en exempel databas och sedan förbereder de data som ska användas för att träna en förutsägelse modell i R.

I [del tre](sql-database-tutorial-predictive-model-deploy.md)får du lära dig hur du lagrar modellen i en databas och sedan skapar lagrade procedurer från de R-skript som du utvecklade i delar en och två. De lagrade procedurerna kommer att köras i en SQL-databas för att göra förutsägelser baserade på nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Del två i den här självstudien förutsätter att du har slutfört [**del en**](sql-database-tutorial-predictive-model-prepare-data.md) och dess krav.

## <a name="train-two-models"></a>Träna två modeller

Om du vill hitta den bästa modellen för Ski hyr data skapar du två olika modeller (linjär regression och besluts träd) och ser vilken som är mer korrekt. Du använder data ramen `rentaldata` som du skapade i del en av den här serien.

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

## <a name="make-predictions-from-both-models"></a>Gör förutsägelser från båda modellerna

Använd en predict-funktion för att förutsäga hyres antalet med hjälp av varje tränad modell.

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

Nu vill du se vilka modeller som ger bästa förutsägelser. Ett snabbt och enkelt sätt att göra detta är att använda en grundläggande ritnings funktion för att visa skillnaden mellan faktiska värden i tränings data och förväntade värden.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Jämföra de två modellerna](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Det verkar som besluts träd modellen är den mer exakta av de två modellerna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta med den här självstudien tar du bort TutorialDB-databasen från Azure SQL Database-servern.

Följ de här stegen i Azure Portal:

1. Välj **alla resurser** eller **SQL-databaser**på den vänstra menyn i Azure Portal.
1. I fältet **Filtrera efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två av den här själv studie serien slutförde du följande steg:

* Träna två Machine Learning-modeller
* Gör förutsägelser från båda modellerna
* Jämför resultaten för att välja den mest exakta modellen

Om du vill distribuera Machine Learning-modellen som du har skapat följer du del tre i den här själv studie serien:

> [!div class="nextstepaction"]
> [Självstudier: Distribuera en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)](sql-database-tutorial-predictive-model-deploy.md)
