---
title: Skapa och kör enkelt R-skript
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Kör enkelt R-skript i Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013268"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Skapa och kör enkelt R-skript i Azure SQL Database Machine Learning Services (förhandsversion)

I den här snabbstarten får du skapa och köra en uppsättning enkla R-skript med hjälp av den offentliga förhandsversionen av [Machine Learning Services (med R) i Azure SQL Database](sql-database-machine-learning-services-overview.md). Du får lära dig hur du omsluter en korrekt strukturerad R-skript i den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) och kör skriptet i en SQL-databas.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Du måste ha en Azure SQL database med Machine Learning Services (med R) aktiverad för att köra exempelkoden i den här övningen. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Kontrollera att du har installerat senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men i den här snabbstarten ska du använda SSMS.

- Den här snabbstarten måste du konfigurera en brandväggsregel på servernivå. Information om hur du gör detta finns i [skapa brandväggsregeln på servernivå](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Kör ett enkelt skript

Om du vill köra ett R-skript, du kan ange som ett argument till den systemlagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

I följande steg ska du köra det här exempelskriptet R i SQL-databasen:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med anslutningen läser [Snabbstart: Använda SQL Server Management Studio för att ansluta till och köra frågor mot en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till den [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

   Skriptet skickas via den `@script` argumentet. Allt i den `@script` argumentet måste vara giltig R-kod.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Om det uppstår några fel kan det bero på att den offentliga förhandsversionen av Machine Learning Services (med R) inte har aktiverats för din SQL-databas. Se [krav](#prerequisites) ovan.

   > [!NOTE]
   > Om du är administratör kan du köra externa kod automatiskt. Du kan bevilja behörighet till andra användare med hjälp av kommandot:
   <br>**BEVILJA köra några externa skriptet till**  *\<användarnamn\>*.

2. Rätt resultat beräknas och R- `print` funktionen returnerar resultatet till den **meddelanden** fönster.

   Det bör se ut ungefär så här.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Köra ett Hello World-skript

Ett typexempel skript är ett som bara visar strängen ”Hello World”. Kör följande kommando.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Indata till den här lagrade proceduren är:

| | |
|-|-|
|*@language* | definierar tillägget språk för att anropa, i det här fallet R |
|*@script* | definierar de kommandon som skickas till körningsmiljön för r. Hela R-skriptet måste stå inom det här argumentet som Unicode-text. Du kan också lägga till texten i en variabel av typen **nvarchar** och sedan anropa variabeln |
|*@input_data_1* | data som returneras av frågan, skickas till körningsmiljön för r., som returnerar data till SQL Server som en dataram |
|MED RESULTATMÄNGDER | satsen definierar schemat för tabellen returnerade data för SQL Server, att lägga till ”Hello World” som kolumnnamnet **int** för datatypen |

Kommandot returnerar följande text:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Använd indata och utdata

Som standard [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) accepterar en enda datauppsättning som indata som du tillhandahåller ofta i form av en giltig SQL-fråga. Den returnerar en enda R dataram som utdata.

Endast en indatamängd kan skickas som parameter, och du kan returnera endast en datamängd. Dock kan du anropa andra datamängder i R-koden och returnera utdata för andra typer utöver datamängden. Du kan även lägga till nyckelordet OUTPUT till valfri parameter så att den returneras med resultatet.

Nu ska vi använda standard-indata och utdata variabler för [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** och **OutputDataSet**.

1. Skapa en liten tabell med testdata.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Använd den `SELECT` -uttrycket för att fråga tabellen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Innehållet i tabellen RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Kör följande R-skript. Data hämtas från en tabell med hjälp av den `SELECT` -instruktionen, skickar det till R-körning och returnerar data som en dataram. Den `WITH RESULT SETS` satsen definierar schemat för tabellen returnerade data för SQL Database, lägger till kolumnnamnet *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Utdata från R-skript som returnerar data från en tabell](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Nu ska vi ändra namnen på de indata- och variablerna. Standard indata och utdata variabelnamn är **InputDataSet** och **OutputDataSet**, det här skriptet ändrar namnen till **SQL_in** och **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observera att R är skiftlägeskänsliga. Indata- och variabler som används i R-skript (**SQL_out**, **SQL_in**) måste matcha de värden som definieras med `@input_data_1_name` och `@output_data_1_name`, inklusive fallet.

   > [!TIP]
   > Endast en indatamängd kan skickas som parameter, och du kan returnera endast en datamängd. Dock kan du anropa andra datamängder i R-koden och returnera utdata för andra typer utöver datamängden. Du kan även lägga till nyckelordet OUTPUT till valfri parameter så att den returneras med resultatet.

1. Du kan också generera värden bara med hjälp av R-skriptet med inga indata (`@input_data_1` anges som tomma).

   Följande skript visar texten ”hello” och ”world”.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Köra frågor mot resultat med hjälp av @script som indata](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrollera R-version

Kör följande skript om du vill se vilken version av R har installerats i din SQL-databas.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R-funktionen `print` returnerar versionen till fönstret **Meddelanden**. I de exempel på utdata nedan ser du att SQL-databas i det här fallet har R version 3.4.4 installerad.

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

Microsoft tillhandahåller ett antal R-paket som är förinstallerade med Machine Learning Services i SQL-databasen.

Kör följande skript för att se en lista över vilka r-paket installeras, inklusive version, beroenden, licens och informationen om biblioteket.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Utdata som genereras från `installed.packages()` i R och returneras som ett resultat set.

**Results**

![Installerade paket i R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Nästa steg

Skapa en modell för maskininlärning med R i SQL-databas genom att följa den här snabbstarten:

> [!div class="nextstepaction"]
> [Skapa och träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-train-score-model.md)

Mer information om Machine Learning Services finns i artiklarna nedan. En del av de här artiklarna handlar om SQL Server, men det mesta av informationen gäller även för Machine Learning Services (med R) i Azure SQL Database.

- [Azure SQL Database Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Självstudier: Lär dig databasanalys med hjälp av R i SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Datavetenskapsgenomgång med slutpunkt till slutpunkt för R och SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Självstudier: Använda RevoScaleR R-funktioner med SQL Server-data](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
