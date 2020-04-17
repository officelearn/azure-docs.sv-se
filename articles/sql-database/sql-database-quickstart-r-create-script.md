---
title: Skapa och kör enkla R-skript
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Kör enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion).
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
ROBOTS: NOINDEX
ms.openlocfilehash: bb6cb6d86933166d2427788d697d9cd38cf04bf0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460179"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Snabbstart: Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion)

I den här snabbstarten skapar och kör du en uppsättning R-skript med Machine Learning Services (med R) i Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL-databas](sql-database-single-database-get-started.md) med [en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) med R aktiverat.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

I det här exemplet används den lagrade proceduren [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) för att radbrytas med ett välformat R-skript.

## <a name="run-a-simple-script"></a>Kör ett enkelt skript

Om du vill köra ett R-skript skickar du det som ett argument till den systemlagd lagrade [proceduren, sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

I följande steg ska du köra det här exempel-R-skriptet i SQL-databasen:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med att ansluta läser du [Snabbstart: Använd SQL Server Management Studio för att ansluta och fråga en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till [den sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

   Skriptet skickas genom `@script` argumentet. Allt i `@script` argumentet måste vara giltig R-kod.

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

   Om det uppstår några fel kan det bero på att den offentliga förhandsversionen av Machine Learning Services (med R) inte har aktiverats för din SQL-databas. Se [Förkunskaper](#prerequisites) ovan.

   > [!NOTE]
   > Om du är administratör kan du köra extern kod automatiskt. Du kan bevilja behörighet till andra användare med kommandot:
   <br>**BEVILJA KÖR ALLA EXTERNA SKRIPT TILL** * \<ANVÄNDARNAMN\>*.

2. Rätt resultat beräknas och `print` R-funktionen returnerar resultatet till fönstret **Meddelanden.**

   Det borde se ut ungefär så här.

    **Resultat**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Kör ett Hello World-skript

Ett typiskt exempel skript är en som bara utdata strängen "Hello World". Kör följande kommando.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Indata till den här lagrade proceduren inkluderar:

| | |
|-|-|
| @language | definierar språktillägget för att ringa, i det här fallet R |
| @script | definierar de kommandon som skickas till R-körningen. Hela ditt R-skript måste vara inneslutet i det här argumentet, som Unicode-text. Du kan också lägga till texten i en variabel av typen **nvarchar** och sedan anropa variabeln |
| @input_data_1 | data som returneras av frågan, som skickas till R-körningen, som returnerar data till SQL Server som en dataram |
|MED RESULTATUPPSÄTTNINGAR | satsen definierar schemat för den returnerade datatabellen för SQL Server, lägger till "Hello World" som kolumnnamn, **int** för datatypen |

Kommandot matar ut följande text:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Använda indata och utdata

Som standard accepterar [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) en enda datauppsättning som indata, som du vanligtvis tillhandahåller i form av en giltig SQL-fråga. Den returnerar sedan en enda R-dataram som utdata.

För tillfället ska vi använda standardindata- och utdatavariablerna [för sp_execute_external_script:](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) **InputDataSet** och **OutputDataSet**.

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

1. Använd `SELECT` uttrycket för att fråga tabellen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultat**

    ![Innehållet i tabellen RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Kör följande R-skript. Data hämtas från tabellen med `SELECT` hjälp av uttrycket, skickar dem genom R-körningen och returnerar data som en dataram. Satsen `WITH RESULT SETS` definierar schemat för den returnerade datatabellen för SQL Database och lägger till kolumnnamnet *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultat**

    ![Utdata från R-skript som returnerar data från en tabell](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Nu ska vi ändra namnen på indata- och utdatavariablerna. Standardnamnen för indata och utdata är **InputDataSet** och **OutputDataSet,** det här skriptet ändrar namnen till **SQL_in** och **SQL_out:**

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observera att R är skiftlägeskänsligt. In- och utdatavariablerna som används i R-skriptet (**SQL_out**, `@input_data_1_name` **SQL_in**) måste matcha de värden som definierats med och `@output_data_1_name`, inklusive skiftläge.

   > [!TIP]
   > Endast en indatamängd kan skickas som parameter, och du kan returnera endast en datamängd. Dock kan du anropa andra datamängder i R-koden och returnera utdata för andra typer utöver datamängden. Du kan även lägga till nyckelordet OUTPUT till valfri parameter så att den returneras med resultatet.

1. Du kan också generera värden som bara använder`@input_data_1` R-skriptet utan indata (är inställd på tom).

   Följande skript matar ut texten "hello" och "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Resultat**

    ![Köra frågor mot resultat med hjälp av @script som indata](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrollera R-version

Om du vill se vilken version av R som är installerad i SQL-databasen kör du följande skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R-funktionen `print` returnerar versionen till fönstret **Meddelanden**. I exempelutdatan nedan kan du se att SQL Database i det här fallet har R version 3.4.4 installerat.

**Resultat**

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

Om du vill se en lista över vilka R-paket som är installerade, inklusive information om versions-, beroenden, licens och bibliotekssökväg, kör du följande skript.

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

Utdata kommer `installed.packages()` från i R och returneras som en resultatuppsättning.

**Resultat**

![Installerade paket i R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Nästa steg

Så här skapar du en maskininlärningsmodell med R i SQL Database:

> [!div class="nextstepaction"]
> [Skapa och träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-train-score-model.md)

Mer information om Azure SQL Database Machine Learning Services med R (förhandsversion) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (förhandsgranskning)](sql-database-machine-learning-services-overview.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R- och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)
