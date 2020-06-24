---
title: 'Självstudie: Distribuera en förutsägelse modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del tre av den här självstudien i tre delar ska du distribuera en förutsägelse modell i R med Azure SQL Database Machine Learning Services (för hands version).
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
ROBOTS: NOINDEX
ms.openlocfilehash: 84c0f6564fc9ab8abc43205b58d9445cda82a2da
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253825"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudie: Distribuera en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I del tre av den här självstudien i tre delar ska du distribuera en förutsägelse modell som utvecklats i R till en databas i Azure SQL Database att använda Azure SQL Database Machine Learning Services (för hands version).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Du skapar en lagrad procedur med ett inbäddat R-skript som gör förutsägelser med hjälp av modellen. Eftersom din modell körs i databasen kan den enkelt tränas mot data som lagras i databasen.

I den här artikeln använder du R-skripten som du utvecklade i delar ett och två, så lär du dig att:

> [!div class="checklist"]
>
> * Skapa en lagrad procedur som genererar Machine Learning-modellen
> * Lagra modellen i en databas tabell
> * Skapa en lagrad procedur som gör förutsägelser med modellen
> * Kör modellen med nya data

I [del ett](predictive-model-prepare-data-tutorial.md)har du lärt dig hur du importerar en exempel databas och sedan förbereder de data som ska användas för att träna en förutsägelse modell i R.

I [del två](predictive-model-build-compare-tutorial.md)har du lärt dig hur du skapar och tränar flera Machine Learning-modeller i R och väljer den mest exakta.

## <a name="prerequisites"></a>Krav

* Del tre i den här själv studie serien förutsätter att du har slutfört [**del ett**](predictive-model-prepare-data-tutorial.md) och [**delar två**](predictive-model-build-compare-tutorial.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Skapa en lagrad procedur som genererar modellen

I del två av den här själv studie serien beslutade du att en dtree-modell (besluts träd) var den mest exakta. Nu kan du använda de R-skript som du har utvecklat genom att skapa en lagrad procedur ( `generate_rental_rx_model` ) som tågen och genererar dtree-modellen med rxDTree från RevoScaleR-paketet.

Kör följande kommandon i Azure Data Studio eller SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Lagra modellen i en databas tabell

Skapa en tabell i TutorialDB-databasen och spara sedan modellen i tabellen.

1. Skapa en tabell ( `rental_rx_models` ) för att lagra modellen.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Spara modellen i tabellen som ett binärt objekt med modell namnet "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;

    DECLARE @model VARBINARY(MAX);

    EXECUTE generate_rental_rx_model @model OUTPUT;

    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );

    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Skapa en lagrad procedur som gör förutsägelser

Skapa en lagrad procedur ( `predict_rentalcount_new` ) som gör förutsägelser med hjälp av den tränade modellen och en uppsättning nya data.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Kör modellen med nya data

Nu kan du använda den lagrade proceduren `predict_rentalcount_new` för att förutsäga hyres antalet från nya data.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Du bör se ett resultat som liknar följande.

```results
RentalCount_Predicted
332.571428571429
```

Du har skapat, tränat och distribuerat en modell i en databas i Azure SQL Database. Sedan använder du modellen i en lagrad procedur för att förutsäga värden baserat på nya data.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med TutorialDB-databasen tar du bort den från servern.

Följ de här stegen i Azure Portal:

1. Välj **alla resurser** eller **SQL-databaser**på den vänstra menyn i Azure Portal.
1. I fältet **Filtrera efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del tre i den här själv studie serien slutförde du följande steg:

* Skapa en lagrad procedur som genererar Machine Learning-modellen
* Lagra modellen i en databas tabell
* Skapa en lagrad procedur som gör förutsägelser med modellen
* Kör modellen med nya data

Mer information om hur du använder R i Azure SQL Database Machine Learning Services (för hands version) finns i:

* [Skriv avancerade R-funktioner i Azure SQL Database att använda Machine Learning Services (förhands granskning)](machine-learning-services-functions.md)
* [Arbeta med R-och SQL-data i Azure SQL Database Machine Learning Services (för hands version)](machine-learning-services-data-issues.md)
* [Lägg till ett R-paket i Azure SQL Database Machine Learning Services (förhands granskning)](machine-learning-services-add-r-packages.md)
