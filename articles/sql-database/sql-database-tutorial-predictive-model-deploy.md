---
title: 'Självstudier: Distribuera en förutsägande modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I den tredje delen av den här självstudiekursen i tre delar, ska du distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978693"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I den tredje delen av den här självstudiekursen i tre delar, ska du distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).

Du skapar en lagrad procedur med en inbäddad R-skript som förutsägelser med hjälp av modellen. Eftersom din modell körs i Azure SQL database, tränas den enkelt mot data som lagras i databasen.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Store förutsägande modell i en databastabell.
> * Skapa en lagrad procedur som genererar modellen
> * Skapa en lagrad procedur som gör förutsägelser med modellen
> * Köra modellen med nya data

I [del ett](sql-database-tutorial-predictive-model-prepare-data.md), du har lärt dig hur du importerar en exempeldatabas till en Azure SQL database och sedan förbereda data som ska användas för att träna en förutsägande modell i R.

I [del två](sql-database-tutorial-predictive-model-build-compare.md), du har lärt dig hur du skapar och skapa flera modeller och väljer sedan det mest korrekta.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Del tre i den här självstudien förutsätter att du har slutfört [ **del ett** ](sql-database-tutorial-predictive-model-prepare-data.md) och [ **del två**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Skapa en lagrad procedur som genererar modellen

I del två i den här självstudieserien får du bestämt dig att en modell för beslut trädet (dtree) har det mest korrekta. Skapa nu en lagrad procedur (`generate_rental_rx_model`) som träna och genererar dtree modellen med rxDTree från RevoScaleR-paketet.

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

## <a name="store-the-model-in-a-database-table"></a>Store modellen i en databastabell.

Skapa en tabell i den TutorialDB som databasen och spara modellen till tabellen.

1. Skapa en tabell (`rental_rx_models`) för lagring av modellen.

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

1. Spara modellen till tabellen som ett binärt objekt med modellen namnet ”rxDTree”.

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Skapa en lagrad procedur som förutsägelser

Skapa en lagrad procedur (`predict_rentalcount_new`) som förutsägelser med hjälp av den tränade modellen och en uppsättning nya data.

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

## <a name="execute-the-model-with-new-data"></a>Köra modellen med nya data

Nu kan du använda den lagrade proceduren `predict_rentalcount_new` att förutsäga antalet hyra från nya data.

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

Du har skapat, tränas, och distribuerat en modell i en Azure SQL database. Du använde sedan den modellen i en lagrad procedur för att förutsäga värden baserat på nya data.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med TutorialDB-databasen kan du ta bort den från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **TutorialDB**, och välj din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del tre i den här självstudieserien får slutfört du de här stegen:

* Store förutsägande modell i en databastabell.
* Skapa en lagrad procedur som genererar modellen
* Skapa en lagrad procedur som gör förutsägelser med modellen
* Köra modellen med nya data

Mer information om hur du använder R i Azure SQL Database Machine Learning Services (förhandsversion) finns:

* [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
* [Arbeta med R och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)
* [Lägga till ett R-paket till Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-add-r-packages.md)
