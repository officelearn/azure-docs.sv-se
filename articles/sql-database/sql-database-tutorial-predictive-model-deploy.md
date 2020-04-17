---
title: 'Självstudiekurs: Distribuera en prediktiv modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del tre av den här självstudien i tre delar distribuerar du en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: efcb73866c83dcc03b5db8b7b97f438fb3010511
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452816"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudiekurs: Distribuera en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del tre av den här självstudiekursen i tre delar distribuerar du en förutsägande modell, utvecklad i R, till en SQL-databas med Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Du skapar en lagrad procedur med ett inbäddat R-skript som gör förutsägelser med hjälp av modellen. Eftersom din modell körs i Azure SQL-databasen kan den enkelt tränas mot data som lagras i databasen.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en lagrad procedur som genererar maskininlärningsmodellen
> * Lagra modellen i en databastabell
> * Skapa en lagrad procedur som gör förutsägelser med hjälp av modellen
> * Köra modellen med nya data

I [del ett](sql-database-tutorial-predictive-model-prepare-data.md)lärde du dig hur du importerar en exempeldatabas och förbereder sedan de data som ska användas för att träna en prediktiv modell i R.

I [del två](sql-database-tutorial-predictive-model-build-compare.md)lärde du dig hur du skapar och tränar flera maskininlärningsmodeller i R och väljer sedan den mest exakta.

## <a name="prerequisites"></a>Krav

* Del tre av den här självstudieserien förutsätter att du har slutfört [**del ett**](sql-database-tutorial-predictive-model-prepare-data.md) och [**del två**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Skapa en lagrad procedur som genererar modellen

I del två av den här självstudieserien bestämde du dig för att en beslutsträdsmodell (dtree) var den mest exakta. Nu, med hjälp av R-skript du`generate_rental_rx_model`utvecklat, skapa en lagrad procedur ( ) som tränar och genererar dtree-modellen med rxDTree från RevoScaleR-paketet.

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

## <a name="store-the-model-in-a-database-table"></a>Lagra modellen i en databastabell

Skapa en tabell i TutorialDB-databasen och spara sedan modellen i tabellen.

1. Skapa en`rental_rx_models`tabell ( ) för lagring av modellen.

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

1. Spara modellen till tabellen som ett binärt objekt, med modellnamnet "rxDTree".

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

Skapa en lagrad procedur (`predict_rentalcount_new`) som gör förutsägelser med den tränade modellen och en uppsättning nya data.

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

Nu kan du använda `predict_rentalcount_new` den lagrade proceduren för att förutsäga antalet hyror från nya data.

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

Du har skapat, tränat och distribuerat en modell i en Azure SQL-databas. Du använde sedan den modellen i en lagrad procedur för att förutsäga värden baserat på nya data.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med databasen TutorialDB tar du bort den från din Azure SQL Database-server.

Gör så här på Azure-portalen:

1. Välj **Alla resurser** eller **SQL-databaser**på menyn till vänster i Azure-portalen .
1. I fältet **Filter efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del tre av den här självstudieserien har du slutfört följande steg:

* Skapa en lagrad procedur som genererar maskininlärningsmodellen
* Lagra modellen i en databastabell
* Skapa en lagrad procedur som gör förutsägelser med hjälp av modellen
* Köra modellen med nya data

Mer information om hur du använder R i Azure SQL Database Machine Learning Services (förhandsversion) finns i:

* [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
* [Arbeta med R- och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)
* [Lägga till ett R-paket i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-add-r-packages.md)
