---
title: 'Självstudier: Finjustera och jämföra förutsägande modeller i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del två av den här självstudieserien med tre delar får du skapa två förutsägande modeller i R med Azure SQL Database Machine Learning Services (förhandsversion) och välj sedan den bästa modellen.
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
ms.date: 05/02/2019
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957612"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del två av den här självstudieserien med tre delar får du skapa två förutsägande modeller i R med Azure SQL Database Machine Learning Services (förhandsversion) och välj sedan den bästa modellen.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Träna två machine learning-modeller
> * Göra förutsägelser från båda modellerna
> * Jämför resultatet för att välja den bästa modellen

I [del ett](sql-database-tutorial-predictive-model-prepare-data.md), du har lärt dig hur du importerar en exempeldatabas till en Azure SQL database och sedan förbereda data som ska användas för att träna en förutsägande modell i R.

I [del tre](sql-database-tutorial-predictive-model-deploy.md), du får lära dig hur du lagrar modellen i en databas och sedan skapa en lagrad procedur som kan göra förutsägelser utifrån nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Del två av den här självstudien förutsätter att du har slutfört [ **del ett** ](sql-database-tutorial-predictive-model-prepare-data.md) och dess krav.

## <a name="train-two-models"></a>Skapa två modeller

Skapa två olika modeller (linjär regression och beslutsträd) för att hitta den bästa modellen för uci ski, och se vilken som är att förutsäga mer exakt. Du kommer att använda dataramen `rentaldata` som du skapade i del ett i den här serien.

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

Funktionen predict till predict hyra räknar med varje tränade modellen.

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

## <a name="compare-the-results"></a>Jämför resultatet

Nu vill du se vilka av modeller ger bästa förutsägelser. Ett snabbt och enkelt sätt att göra detta är att använda en grundläggande ritområdet funktion för att visa skillnaden mellan de faktiska värdena i dina utbildningsdata och de förväntade värdena.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Jämföra två modeller](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Det verkar som beslut trädet modellen är desto mer exakt de två modellerna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta med den här självstudiekursen, kan du ta bort TutorialDB databasen från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **TutorialDB**, och välj din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del två i den här självstudieserien får slutfört du de här stegen:

* Träna två machine learning-modeller
* Göra förutsägelser från båda modellerna
* Jämför resultatet för att välja den bästa modellen

Följ del tre i självstudieserien om du vill distribuera machine learning-modell som du har skapat:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-predictive-model-deploy.md)
