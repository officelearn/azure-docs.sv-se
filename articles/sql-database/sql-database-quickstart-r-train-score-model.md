---
title: Skapa och träna en förutsägande modell i R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Skapa en enkel förutsägelsemodell i R med Azure SQL Database Machine Learning Services (förhandsversion) och förutsäga ett resultat med hjälp av nya data.
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
ms.openlocfilehash: 97309a24c0ab12720f968409856a16cab4ff7ac7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013262"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Skapa och träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I den här snabbstarten du ska skapa och tränar en förutsägelsemodell med R, spara modellen till en tabell i SQL-databasen och sedan använda modellen för att förutsäga värden från nya data med hjälp av den offentliga förhandsversionen av [Machine Learning Services (med R) i Azure SQL Database ](sql-database-machine-learning-services-overview.md). 

Modellen som du ska använda i den här snabbstarten är en enkel regressionsmodell som beräknar avståndet stoppas på en bil utifrån hastighet. Du kommer att använda den **bilar** datauppsättning som ingår i R, eftersom den är liten och lätt att förstå.

> [!TIP]
> Många små och stora datamängder ingår i R-körningen. Om du vill hämta en lista över datauppsättningar som installerats med R, Skriv `library(help="datasets")` från en R-kommandotolk.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Du måste ha en Azure SQL database med Machine Learning Services (med R) aktiverad för att köra exempelkoden i den här övningen. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Kontrollera att du har installerat senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men i den här snabbstarten ska du använda SSMS.

- Den här snabbstarten måste du konfigurera en brandväggsregel på servernivå. Information om hur du gör detta finns i [skapa brandväggsregeln på servernivå](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Skapa och tränar en förutsägelsemodell

Bil hastighet data i den **bilar** datauppsättningen innehåller två kolumner, både numeriska: **dist** och **hastighet**. Innehåller flera stoppar observationer med olika hastigheter. I dessa data ska du skapa en linjär regressionsmodell som beskriver relationen mellan bil hastighet och avståndet som krävs för att stoppa en bil.

Kraven i en linjär modell är enkla:
- Definiera en formel som beskriver relationen mellan den beroende variabeln *hastighet* och oberoende variabeln *avståndet*.
- Ange indata som ska användas för att träna modellen.

> [!TIP]
> Om du behöver uppdatera dig om linjära modeller kan prova den här självstudien som beskriver processen för att passa en modell med rxLinMod: [Anpassa linjära modeller](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Skapa en regressionsmodell i följande steg konfigurerar träningsdata, träna upp tjänsten med hjälp av utbildningsdata och sedan spara modellen till en SQL-tabell.

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med anslutningen läser [Snabbstart: Använda SQL Server Management Studio för att ansluta till och köra frågor mot en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skapa den **CarSpeed** tabell för att spara träningsdata.

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

1. Skapa en regression modell med `rxLinMod`. 

   För att bygga modellen du definiera formel i R-kod och sedan skicka träningsdata **CarSpeed** som indataparameter.

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

   Utdata från ett R-paket som skapar en modell är vanligtvis en **binära objekt**, så tabellen måste ha en kolumn med **VARBINARY(max)** typen.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Nu anropar den lagrade proceduren, generera modellen och spara den i en tabell.

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

## <a name="view-the-table-of-coefficients"></a>Visa tabellen över

Generellt är utdata för R från den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) begränsade till en enda dataram. Du kan dock returnera utdata för andra typer, till exempel skalärer, utöver dataramen.

Anta exempelvis att du vill tränar en modell men omedelbart visa tabellen över från modellen. Om du vill göra det, skapa tabellen över som huvudsakliga resultatet ange och mata ut den tränade modellen i en SQL-variabel. Du kan omedelbart återanvända modellen genom att anropa variabeln eller du kan spara modellen till en tabell som visas här.

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

![Tränad modell med ytterligare utdata](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Rangordna nya data med hjälp av den tränade modellen

*Bedömning* är en term som används i datavetenskap för att beskriva genererar förutsägelser, sannolikhet eller andra värden baserat på nya data som matas in en tränad modell. Du använder modellen som du skapade i föregående avsnitt för att bedöma förutsägelser mot nya data.

Lade du märke till att de ursprungliga träningsdata tog slut vid en hastighet på 40 km/h? Det beror på att de ursprungliga data baserades på ett experiment från 1920. Du kanske undrar hur lång tid det tar en bil från 1920s att avbrytas om den kan komma igång så snabbt som 60 mph eller till och med 100 mph? Du kan ange vissa nya hastighetsvärdena i din modell för att besvara den här frågan.

1. Skapa en tabell med nya hastighet data.

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

2. Förutsäga stoppar avståndet från dessa nya värden för hastighet.

   Eftersom din modell som baseras på den **rxLinMod** algoritmen som tillhandahålls som en del av den **RevoScaleR** paketet, som du anropar den [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) fungerar i stället för allmän R `predict` funktion.

   Det här exempelskriptet:
   - Använder en SELECT-instruktion för att hämta en enda modell från tabellen
   - Skickar den som indataparameter
   - Anropar den `unserialize` funktionen på modellen
   - Gäller den `rxPredict` funktion med rätt argument i modellen
   - Innehåller nya indata

   > [!TIP]
   > I realtid bedömning finns i [serialisering funktioner](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) tillhandahålls av RevoScaleR.

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

   ![Resultatmängd för att förutsäga bromssträcka](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> I det här exempelskriptet den `str` funktionen har lagts till under testfasen att kontrollera schemat för data som returneras från R. Du kan ta bort instruktionen igen senare.
>
> De kolumnnamn som använs i R-skriptet skickas inte nödvändigtvis till utdata för den lagrade proceduren. Här definierar med resultat-satsen vissa nya kolumnnamn.

## <a name="next-steps"></a>Nästa steg

Mer information om Machine Learning Services finns i artiklarna nedan. En del av de här artiklarna handlar om SQL Server, men det mesta av informationen gäller även för Machine Learning Services (med R) i Azure SQL Database.

- [Azure SQL Database Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Självstudier: Lär dig databasanalys med hjälp av R i SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Datavetenskapsgenomgång med slutpunkt till slutpunkt för R och SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Självstudier: Använda RevoScaleR R-funktioner med SQL Server-data](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
