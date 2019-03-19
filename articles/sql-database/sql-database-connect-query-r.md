---
title: Snabbstart för att använda Machine Learning Services (med R) i Azure SQL Database (förhandsversion) | Microsoft Docs
description: Det här avsnittet visar hur du använder Machine Learning Services i Azure SQL Database och hur du kör R-skript för att leverera avancerad analys i stor skala samt möjligheten att ta beräkningar och bearbetningen till det ställe där data finns, vilket eliminerar behovet av att hämta data över nätverket.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: e15cf93514f921223fea37aa480730bba46dd195
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864957"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Snabbstart: Använda Machine Learning Services (med R) i Azure SQL Database (förhandsversion)

Den här artikeln förklarar hur du kan använda den offentliga förhandsversionen av [Machine Learning-tjänster (med R) i Azure SQL Database](sql-database-machine-learning-services-overview.md). Den går igenom grunderna för att flytta data mellan en SQL-databas och R. Den förklarar även hur du omsluter välstrukturerad R-kod i den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) för att skapa, träna och använda maskininlärningsmodeller i en SQL-databas.

Använd kraften hos R-språket för att leverera avancerad analys och maskininlärning i databasen. Den här funktionen ger beräkning och bearbetning där data finns, vilket eliminerar behovet av att hämta data över nätverket. Dessutom kan du dra nytta av R-företagspaket och tillhandahålla avancerad analys i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R med överlägg med R-företagspaket från Microsoft. Microsofts R-funktioner och algoritmer är utformade för både stor skala och nytta. De levererar förutsägelseanalys, statistisk modellering, datavisualiseringar och ledande algoritmer för maskininlärning.

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Förutsättningar

För att köra exempelkoden i de här övningarna behöver du först ha en SQL-databas med Machine Learning Services (med R) aktiverat. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

Du kan ansluta till SQL-databasen och köra R-skripten med valfritt databashanterings- eller frågeverktyg, så länge det kan ansluta till en SQL-databas och köra en T-SQL-fråga eller en lagrad procedur. Den här snabbstarten använder [SQL Server Management Studio](sql-database-connect-query-ssms.md).

För övningen [lägga till ett paket](#add-package) behöver du även installera [R](https://www.r-project.org/) och [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) på den lokala datorn.

Den här snabbstarten kräver även att du konfigurerar en brandväggsregel på servernivå. En snabbstart som visar hur du gör detta finns i [Skapa brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md).

## <a name="verify-r-exists"></a>Kontrollera att R finns

Du kan bekräfta att Machine Learning Services (med R) är aktiverat för SQL-databasen. Följ stegen nedan.

1. Öppna SQL Server Management Studio och anslut till din SQL-databas. Mer information om hur du ansluter finns i [Snabbstart: Använda SQL Server Management Studio för att ansluta till och köra frågor mot en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Kör koden nedan. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Om allt är korrekt bör du se ett resultatmeddelande som det här.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Om det uppstår några fel kan det bero på att den offentliga förhandsversionen av Machine Learning Services (med R) inte har aktiverats för din SQL-databas. Information om hur du registrerar dig för den allmänna förhandsversionen finns ovan.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du är administratör kan du köra extern kod automatiskt. Alla andra måste beviljas behörighet.

Ersätt `<username>` med en giltig databasanvändarinloggning innan du kör kommandot.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Grundläggande R-interaktion

Det finns två sätt att köra R-kod i SQL-databasen:

+ Lägg till ett R-skript som ett argument för den systemlagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Från en [R-fjärrklient](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client) ansluter du till din SQL-databas och kör kod med hjälp av SQL-databasen som beräkningskontext.

Följande övning fokuserar på den första interaktionsmodellen: hur du skickar R-kod till en lagrad procedur.

1. Kör ett enkelt skript om du vill se hur ett R-skript körs i din SQL-databas.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. Förutsatt att du har konfigurerat allt på rätt sätt beräknas det korrekt resultatet, och `print`-funktionen för R returnerar resultatet till fönstret **Meddelanden**.

    **Results**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Att hämta **stdout**-meddelanden är användbart när du testar din kod, men det är vanligare att du behöver returnera resultatet i tabellformat så att du kan använda det i ett program eller skriva det till en tabell. Mer information finns i avsnittet om indata och utdata nedan.

Kom ihåg att allt i argumentet `@script` måste vara giltig R-kod.

## <a name="inputs-and-outputs"></a>Indata och utdata

Som standard accepterar [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) en enda indatamängd som du vanligtvis tillhandahåller i form av en giltig SQL-fråga. Andra typer av indata kan skickas som SQL-variabler.

Den lagrade proceduren returnerar en enda R-dataram som utdata, men du kan även mata ut skalärer och modeller som variabler. Du kan till exempel mata ut en tränad modell som binär variabel och skicka den till en T-SQL INSERT-instruktion för att skriva den modellen till en tabell. Du kan även generera kurvor (i binärt format) eller skalärer (enskilda värden, till exempel datum och tid, tidsåtgången för att träna modellen och så vidare).

Just nu tittar vi bara på standardvariablerna för indata och utdata i sp_execute_external_script: `InputDataSet` och `OutputDataSet`.

1. Skapa en liten tabell med testdata genom att köra följande T-SQL-instruktion:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    När tabellen har skapats använder du följande instruktion för att köra frågor mot tabellen:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Results**

    ![Innehållet i tabellen RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Du kan hämta data från tabellen som indata till R-skriptet. Kör instruktionen nedan. Den hämtar data från tabellen, går tur och retur genom R-körningen och returnerar värdena med kolumnnamnet *NewColName*.

    De data som returneras av frågan skickas till R-körningen, som returnerar data till SQL Database som en dataram. Satsen WITH RESULT SETS definierar schemat för den returnerade datatabellen för SQL Database.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Utdata från R-skript som returnerar data från en tabell](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Nu ändrar vi namnet på indata- eller utdatavariablerna. Skriptet ovan använder standardvariabelnamnen för indata och utdata, _InputDataSet_ och _OutputDataSet_. Att definiera indata som är associerade med _InputDatSet_, du använder den  *\@input_data_1* variabeln.

    I det här skriptet har namnen på utdata- och indatavariablerna för den lagrade proceduren ändrats till *SQL_out* och *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Observera att R är skiftlägeskänsligt, så skiftläget för indata- och utdatavariablerna i `@input_data_1_name` och `@output_data_1_name` måste stämma överens med dem som finns i R-koden i `@script`. 

    Dessutom är ordningen på parametrarna viktig. Du måste ange de obligatoriska parametrarna  *\@input_data_1* och  *\@output_data_1* först för att kunna använda följande valfria parametrar  *\@ input_data_1_name* och  *\@output_data_1_name*.

    Endast en indatamängd kan skickas som parameter, och du kan returnera endast en datamängd. Dock kan du anropa andra datamängder i R-koden och returnera utdata för andra typer utöver datamängden. Du kan även lägga till nyckelordet OUTPUT till valfri parameter så att den returneras med resultatet. 

    Instruktionen `WITH RESULT SETS` definierar schemat för de data som används i SQL Database. Du måste ange SQL-kompatibla datatyper för varje kolumn som du returnerar från R. Du kan använda schemadefinitionen för att tillhandahålla nya kolumnnamnen också, eftersom du inte behöver använda kolumnnamnen från R-dataramen.

4. Du kan även generera värden med hjälp av R-skriptet och lämna indatafrågesträngen i _@input_data_1_ tom.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Köra frågor mot resultat med hjälp av @script som indata](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrollera R-version

Om du vill se vilken version av R som är installerad i din SQL-databas gör du följande:

1. Kör skriptet nedan på din SQL-databas.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. R-funktionen `print` returnerar versionen till fönstret **Meddelanden**. I exempelutdata nedan ser du att SQL-databasen i det här fallet har R-version 3.4.4 installerad.

    **Results**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>Lista R-paket

Microsoft tillhandahåller ett antal R-paket som är förinstallerade med Machine Learning Services i SQL-databasen. Om du vill se en lista över vilka R-paket som är installerade, däribland version, beroenden, licens och information om bibliotekssökväg, följer du stegen nedan. Om du vill lägga till ytterligare paket kan du läsa avsnittet [lägga till ett paket](#add-package).

1. Kör skriptet nedan på din SQL-databas.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. Utdata kommer från `installed.packages()` i R och returneras som en resultatuppsättning.

    **Results**

    ![Installerade paket i R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Skapa en förutsägelsemodell

Du kan träna en modell med hjälp av R och spara modellen till en tabell i SQL-databasen. I den här övningen tränar du en enkel regressionsmodell som förutser bromssträckan för en bil baserat på hastigheten. Du använder datamängden `cars`, som ingår i R, eftersom den är liten och lätt att förstå.

1. Skapa först en tabell för att spara träningsdata.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Många små och stora datamängder ingår i R-körningen. Om du vill hämta en lista över datamängder som installerats med R skriver du `library(help="datasets")` i en R-kommandotolk.

2. Skapa en regressionsmodell. Data om bilens hastighet innehåller två kolumner som båda är numeriska, `dist` och `speed`. Det finns flera observationer för vissa hastigheter. Från dessa data skapar du en linjär regressionsmodell som beskriver ett visst förhållande mellan bilens hastighet och det avstånd som krävs för bilen ska bromsas.

    Kraven i en linjär modell är enkla:

   - Definiera en formel som beskriver relationen mellan den beroende variabeln `speed` och den oberoende variabeln `distance`.

   - Ange indata som ska användas för att träna modellen.

     > [!TIP]
     > Om du behöver uppdatera dig om linjära modeller rekommenderar vi den här självstudien, som beskriver processen för att passa en modell med hjälp av rxLinMod: [Anpassa linjära modeller](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

     För att skapa modellen definierar du formeln i R-koden och skickar data som en indataparameter.

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     Det första argumentet till rxLinMod är parametern *formula*, som definierar avståndet som beroende på hastigheten. Indata lagras i variabeln `CarsData`, som fylls av SQL-frågan. Om du inte anger ett specifikt namn för dina indata blir standardvariabelnamnet _InputDataSet_.

2. Sedan skapar du en tabell där du lagrar modellen så att du kan träna om eller använda den för förutsägelse. Utdata från ett R-paket som skapar en modell är vanligtvis ett **binärt objekt**. Därför tabellen måste ange en kolumn med typen **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Om du vill spara modellen kör du följande Transact-SQL-instruktion för att anropa den lagrade proceduren, generera modellen och spara den i en tabell.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Observera att du får följande fel om du kör den här koden en andra gång:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Ett alternativ för att undvika det här felet är att uppdatera namnet för varje ny modell. Du kan till exempel ändra namnet till något mer beskrivande och inkludera modelltypen, den dag då du skapade den och så vidare.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Generellt är utdata för R från den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) begränsade till en enda dataram.

    Du kan dock returnera utdata för andra typer, till exempel skalärer, utöver dataramen.

    Anta exempelvis att du vill träna en modell men omedelbart visa en tabell med koefficienter från modellen. Du kan skapa tabellen med koefficienter som den resultatuppsättningen och mata ut den tränade modellen i en SQL-variabel. Du kan omedelbart återanvända modellen genom att anropa variabeln, eller så kan du spara modellen till en tabell på det sätt som visas här.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Results**

    ![Tränad modell med ytterligare utdata](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Förutse

Använd den modell som du skapade i föregående avsnitt för att bedöma förutsägelser mot nya data. För att utföra _bedömning_ med hjälp av nya data hämtar du en av de tränade modellerna från tabellen och anropar sedan en ny datamängd som du vill basera förutsägelser på. Bedömning är en term som ibland används inom datavetenskap för referera till generering av förutsägelser, sannolikheter eller andra värden baserat på nya data som matas in i en tränad modell.

1. Skapa först en tabell med nya hastighetsdata. Lade du märke till att de ursprungliga träningsdata tog slut vid en hastighet på 40 km/h? Det beror på att de ursprungliga data baserades på ett experiment från 1920. Då undrar du kanske hur lång tid det skulle ta för en bil från 1920-talet att bromsa om den skulle kunna köra i 95 eller rentav 160 km/h? För att besvara den här frågan behöver du ange nya hastighetsvärden.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Eftersom modellen baseras den **rxLinMod**-algoritm som tillhandahålls som en del av **RevoScaleR**-paketet anropar du i det här exemplet [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict)-funktionen i stället för den generiska R-funktionen `predict`.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    Skriptet ovan utför följande steg:

   + Använd en SELECT-instruktion för att hämta en enda modell från tabellen och skicka den som en indataparameter.

   + När du har hämtat modellen från tabellen anropar du funktionen `unserialize` på modellen.

       > [!TIP] 
       > Se även de nya [serialiseringsfunktionerna](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) som tillhandahålls av RevoScaleR, som stöder bedömning i realtid.
   + Tillämpa funktionen `rxPredict` med lämpliga argument på modellen och ange nya indata.

   + I det här exemplet läggs funktionen `str` till under testfasen för att kontrollera schemat för data som returneras från R. Du kan ta bort instruktionen senare.

   + De kolumnnamn som använs i R-skriptet skickas inte nödvändigtvis till utdata för den lagrade proceduren. Här har vi använt satsen WITH RESULTS för att definiera några nya kolumnnamn.

     **Results**

     ![Resultatmängd för att förutsäga bromssträcka](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     Det är även möjligt att använda [PREDICT i Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) för att generera ett förutsagt värde eller poäng baserat på en lagrad modell.

<a name="add-package"></a>

## <a name="add-a-package"></a>Lägga till ett paket

Om du behöver använda ett paket som inte redan är installerat i din SQL-databas kan du installera det med hjälp av [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Installera paketet genom att följa stegen nedan.

1. Ladda ned zip-filen med det senaste **sqlmlutils** från [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) till den lokala datorn. Du behöver inte packa upp filen.

1. Om du inte har R installerat laddar du ned R från [www.r-project.org](https://www.r-project.org/) och installerar det på din lokala dator. R är tillgängligt för Windows, macOS och Linux. I det här exemplet använder vi Windows.

1. Installera först paketet **RODBCext**, som är en förutsättning för **sqlmlutils**. **RODBCext** installerar även beroendet för **RODBC**-paketet. Öppna en **kommandotolk** och kör följande kommando:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Om du får följande fel, ”'R' is not recognized as an internal or external command, operable program or batch file” (R känns inte igen som internt eller externt kommando, körbart program eller batchfil) innebär det antagligen att sökvägen till R.exe inte ingår i din **PATH**-miljövariabel i Windows. Du kan antingen lägga till katalogen i miljövariabeln eller gå till katalogen i kommandotolken (till exempel `cd C:\Program Files\R\R-3.5.1\bin`) innan du kör kommandot.

1. Använd kommando **R CMD INSTALL** för att installera **sqlmlutils**. Ange sökvägen till den katalog dit du laddade ned zip-filen till och namnet på zip-filen. Exempel:

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    De utdata du får bör likna följande:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. I det här exemplet använder du RStudio Desktop som IDE. Du kan använda en annan IDE om du föredrar det. Ladda ned och installera RStudio Desktop från [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/) om du inte redan har installerat RStudio.

1. Öppna **RStudio** och skapa en ny **R-skriptfil**. Använd följande R-kod för att installera ett paket med hjälp av sqlmlutils. I exemplet nedan installerar du [glue](https://cran.r-project.org/web/packages/glue/)-paketet, som kan formatera och interpolera en sträng.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **Omfånget** kan antingen vara **PUBLIC** (offentligt) eller **PRIVATE** (privat). Det offentliga omfånget är användbart för databasadministratören för att installera paket som alla användare kan använda. Det privata omfånget gör att paketet endast blir tillgängligt för den användare som installerar det. Om du inte anger omfånget blir standardomfånget **PRIVATE**.

1. Kontrollera nu att **glue**-paketet har installerats.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Results**

    ![Innehållet i tabellen RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. När paketet har installerats kan du använda det i ditt R-skript via **sp_execute_external_script**. Öppna **SQL Server Management Studio** och anslut till din SQL-databas. Kör följande skript:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Följande resultat visas på fliken Meddelanden.

    **Results**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Om du vill ta bort paketet kör du följande R-skript i **RStudio** på den lokala datorn.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Ett annat sätt att installera R-paket till din SQL-databas är att ladda upp R-paketet från byteströmmen med [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Nästa steg

Mer information om Machine Learning Services finns i artiklarna nedan. En del av de här artiklarna handlar om SQL Server, men det mesta av informationen gäller även för Machine Learning Services (med R) i Azure SQL Database.

- [Azure SQL Database Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Självstudier: Lär dig databasanalys med hjälp av R i SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Datavetenskapsgenomgång med slutpunkt till slutpunkt för R och SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Självstudier: Använda RevoScaleR R-funktioner med SQL Server-data](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
