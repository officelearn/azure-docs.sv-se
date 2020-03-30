---
title: Skapa och träna en prediktiv modell i R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Skapa en enkel förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion) och förutse sedan ett resultat med hjälp av nya data.
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
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768469"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Snabbstart: Skapa och träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I den här snabbstarten skapar och tränar du en förutsägande modell med R, sparar modellen i en tabell i databasen och använder sedan modellen för att förutsäga värden från nya data med Hjälp av Machine Learning Services (med R) i Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL-databas](sql-database-single-database-get-started.md) med [en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) med R aktiverat. [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas.

I det här exemplet används en enkel regressionsmodell för att förutsäga en bils stoppsträcka baserat på hastigheten med hjälp av **den datauppsättning** för bilar som ingår i R.

> [!TIP]
> Många datauppsättningar ingår i R-körningen för att få en `library(help="datasets")` lista över installerade datauppsättningar skriver du från kommandotolken R.

## <a name="create-and-train-a-predictive-model"></a>Skapa och träna en prediktiv modell

Bilens hastighetsdata i **bilens** datauppsättning innehåller två kolumner, både **numeriska: dist** och **hastighet**. Data inkluderar flera stoppa observationer med olika hastigheter. Från dessa data ska du skapa en linjär regressionsmodell som beskriver förhållandet mellan bilens hastighet och det avstånd som krävs för att stoppa en bil.

Kraven i en linjär modell är enkla:
- Definiera en formel som beskriver förhållandet mellan den beroende *variabelhastigheten* och det oberoende *variabelt avståndet*.
- Ange indata som ska användas för att träna modellen.

> [!TIP]
> Om du behöver en repetitionskurs på linjära modeller kan du prova den här självstudien som beskriver processen att montera en modell med rxLinMod: [Montering av linjära modeller](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

I följande steg ska du ställa in träningsdata, skapa en regressionsmodell, träna den med hjälp av träningsdata och sedan spara modellen i en SQL-tabell.

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med att ansluta läser du [Snabbstart: Använd SQL Server Management Studio för att ansluta och fråga en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skapa **tabellen CarSpeed** för att spara träningsdata.

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

1. Skapa en regressionsmodell med `rxLinMod`. 

   Om du vill skapa modellen definierar du formeln i R-koden och skickar sedan träningsdata **CarSpeed** som en indataparameter.

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

   Utdata för ett R-paket som skapar en modell är vanligtvis ett **binärt objekt**, så tabellen måste ha en kolumn med **VARBINARY(max)** typ.

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

## <a name="view-the-table-of-coefficients"></a>Visa tabellen med koefficienter

Generellt är utdata för R från den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) begränsade till en enda dataram. Du kan dock returnera utdata för andra typer, till exempel skalärer, utöver dataramen.

Anta till exempel att du vill träna en modell men omedelbart visa tabellen med koefficienter från modellen. För att göra det skapar du tabellen med koefficienter som huvudresultatuppsättning och matar ut den tränade modellen i en SQL-variabel. Du kan omedelbart återanvända modellen genom att anropa variabeln, eller så kan du spara modellen i en tabell som visas här.

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

## <a name="score-new-data-using-the-trained-model"></a>Poängsätta nya data med den tränade modellen

*Poängsättning* är en term som används i datavetenskap för att generera förutsägelser, sannolikheter eller andra värden baserat på nya data som matas in i en utbildad modell. Du ska använda modellen som du skapade i föregående avsnitt för att få förutsägelser mot nya data.

Lade du märke till att de ursprungliga träningsdata tog slut vid en hastighet på 40 km/h? Det beror på att de ursprungliga data baserades på ett experiment från 1920. Du kanske undrar, hur lång tid skulle det ta en bil från 1920-talet att sluta om det kunde komma igång så fort som 60 mph eller ens 100 mph? Om du vill besvara den här frågan kan du ange några nya hastighetsvärden till din modell.

1. Skapa en tabell med nya hastighetsdata.

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

2. Förutsäg stoppavstånd från dessa nya hastighetsvärden.

   Eftersom din modell är baserad på **rxLinMod-algoritmen** som tillhandahålls som en del av **RevoScaleR-paketet anropar** du [funktionen rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) i stället för den generiska R-funktionen. `predict`

   Det här exempelskriptet:
   - Använder en SELECT-sats för att hämta en enda modell från tabellen
   - Skickar den som en indataparameter
   - Anropar `unserialize` funktionen på modellen
   - Använder `rxPredict` funktionen med lämpliga argument på modellen
   - Tillhandahåller nya indata

   > [!TIP]
   > För bedömning i realtid finns i [Serialiseringsfunktioner](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) som tillhandahålls av RevoScaleR.

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
> I det här `str` exempelskriptet läggs funktionen till under testfasen för att kontrollera schemat för data som returneras från R. Du kan ta bort satsen senare.
>
> De kolumnnamn som använs i R-skriptet skickas inte nödvändigtvis till utdata för den lagrade proceduren. Här definierar satsen MED RESULTAT några nya kolumnnamn.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure SQL Database Machine Learning Services med R (förhandsversion) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (förhandsgranskning)](sql-database-machine-learning-services-overview.md)
- [Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-create-script.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R- och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)
