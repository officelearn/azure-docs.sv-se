---
title: Skapa och träna en förutsägelse modell i R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Skapa en enkel förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning) och Förutsäg sedan ett resultat med nya data.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 04054d206d5e30d2de3da5ccd9d018027653cdcf
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760036"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Snabb start: skapa och träna en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)

I den här snabb starten skapar du och tränar en förutsägelse modell med R, sparar modellen i en tabell i databasen och använder sedan modellen för att förutsäga värden från nya data med hjälp av Machine Learning Services (med R) i Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- En [Azure SQL-databas](sql-database-single-database-get-started.md) med en [brand Väggs regel på server nivå](sql-database-server-level-firewall-rule.md)

- [Machine Learning Services](sql-database-machine-learning-services-overview.md) med R aktiverat. [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas.

I det här exemplet används en enkel Regressions modell för att förutsäga det stoppade avståndet för en bil baserat på hastigheten med den datauppsättning för **bilar** som ingår i R.

> [!TIP]
> Många data uppsättningar ingår i R-körningen. om du vill hämta en lista över installerade data uppsättningar skriver du `library(help="datasets")` från R-Kommandotolken.

## <a name="create-and-train-a-predictive-model"></a>Skapa och träna en förutsägelse modell

Bil hastigheten i data uppsättningen **bilar** innehåller två kolumner, både numeriskt: **förd** och **Speed**. Datan innehåller flera stopp observationer vid olika hastigheter. Från dessa data skapar du en linjär Regressions modell som beskriver förhållandet mellan bil hastigheten och det avstånd som krävs för att stoppa en bil.

Kraven i en linjär modell är enkla:
- Definiera en formel som beskriver förhållandet mellan den beroende variabla *hastigheten* och det oberoende variabla *avståndet*.
- Ange indata som ska användas för att träna modellen.

> [!TIP]
> Om du behöver en uppdatering av linjära modeller kan du prova den här självstudien som beskriver processen för att anpassa en modell med rxLinMod: [passa linjära modeller](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

I följande steg ska du ställa in tränings data, skapa en Regressions modell, träna den med tränings data och sedan Spara modellen i en SQL-tabell.

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med att ansluta, se [snabb start: använda SQL Server Management Studio för att ansluta och skicka frågor till en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skapa **CarSpeed** -tabellen för att spara tränings data.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Skapa en Regressions modell med `rxLinMod`. 

   Om du vill skapa modellen definierar du formeln inuti R-koden och skickar sedan **CarSpeed** för träning som en indataparameter.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Det första argumentet till rxLinMod är parametern *formula*, som definierar avståndet som beroende på hastigheten. Indata lagras i variabeln `CarsData`, som fylls av SQL-frågan.

1. Skapa en tabell där du lagrar modellen så att du kan använda den senare för förutsägelse. 

   Utdata från ett R-paket som skapar en modell är vanligt vis ett **binärt objekt**, så tabellen måste ha en kolumn av typen **varbinary (max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Anropa nu den lagrade proceduren, generera modellen och spara den i en tabell.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Observera att du får följande fel om du kör den här koden en andra gång:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Ett alternativ för att undvika det här felet är att uppdatera namnet för varje ny modell. Du kan till exempel ändra namnet till något mer beskrivande och inkludera modelltypen, den dag då du skapade den och så vidare.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Visa tabellen över koefficienter

Generellt är utdata för R från den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) begränsade till en enda dataram. Du kan dock returnera utdata för andra typer, till exempel skalärer, utöver dataramen.

Anta till exempel att du vill träna en modell, men direkt Visa tabellen över koefficienter från modellen. För att göra det skapar du en tabell med koefficienter som huvud resultat uppsättning och utvärderar den tränade modellen i en SQL-variabel. Du kan omedelbart återanvända modellen genom att anropa variabeln, eller så kan du Spara modellen i en tabell som visas här.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Results**

![Tränad modell med ytterligare utdata](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Räkna nya data med hjälp av den tränade modellen

*Poängsättning* är en term som används i data vetenskap för att skapa förutsägelser, sannolikheter eller andra värden baserat på nya data som matas in i en utbildad modell. Du använder den modell som du skapade i föregående avsnitt för att skapa Poäng förutsägelser mot nya data.

Lade du märke till att de ursprungliga träningsdata tog slut vid en hastighet på 40 km/h? Det beror på att de ursprungliga data baserades på ett experiment från 1920. Du kanske undrar hur lång tid tar en bil från de 1920 som ska stoppas om den kan bli så snabb som 60 mph eller 100 mph? Om du vill besvara den här frågan kan du ange några nya hastighets värden för din modell.

1. Skapa en tabell med nya hastighets data.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Förutsäga avståndet från dessa nya hastighets värden.

   Eftersom modellen är baserad på **rxLinMod** -algoritmen som finns som en del av **RevoScaleR** -paketet anropar du funktionen [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) i stället för funktionen Generic R `predict`.

   Det här exempel skriptet:
   - Använder ett SELECT-uttryck för att hämta en enskild modell från tabellen
   - Skickar den som en indataparameter
   - Anropar funktionen `unserialize` på modellen
   - Använder funktionen `rxPredict` med lämpliga argument för modellen
   - Tillhandahåller nya indata

   > [!TIP]
   > I real tids poängsättning, se de [serialiserings funktioner](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) som tillhandahålls av RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Results**

   ![Resultatmängd för att förutsäga bromssträcka](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> I det här exempel skriptet läggs funktionen `str` till under test fasen för att kontrol lera schemat för data som returneras från R. Du kan ta bort instruktionen senare.
>
> De kolumnnamn som använs i R-skriptet skickas inte nödvändigtvis till utdata för den lagrade proceduren. Här är en WITH RESULTs-sats som definierar några nya kolumn namn.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure SQL Database Machine Learning Services med R (för hands version) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (för hands version)](sql-database-machine-learning-services-overview.md)
- [Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhands granskning)](sql-database-quickstart-r-create-script.md)
- [Skriv avancerade R-funktioner i Azure SQL Database att använda Machine Learning Services (förhands granskning)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R-och SQL-data i Azure SQL Database Machine Learning Services (för hands version)](sql-database-machine-learning-services-data-issues.md)