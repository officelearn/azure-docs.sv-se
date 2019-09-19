---
title: Skapa och kör enkla R-skript
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Kör enkla R-skript i Azure SQL Database Machine Learning Services (för hands version).
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
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123197"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhands granskning)

I den här snabb starten ska du skapa och köra en uppsättning enkla R-skript med hjälp av den offentliga för hands versionen av [Machine Learning Services (med R) i Azure SQL Database](sql-database-machine-learning-services-overview.md). Du får lära dig att figursätta ett välformulerat R-skript i den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) och köra skriptet i en SQL-databas.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Om du vill köra exempel koden i de här övningarna måste du först ha en Azure SQL-databas med Machine Learning Services (med R) aktiverat. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Kontrol lera att du har installerat den senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databas hanterings-eller specialverktyg, men i den här snabb starten ska du använda SSMS.

- Den här snabb starten kräver att du konfigurerar en brand Väggs regel på server nivå. Information om hur du gör detta finns i [skapa brand Väggs regel på server nivå](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Kör ett enkelt skript

Om du vill köra ett R-skript skickar du det som ett argument till systemets lagrade procedur, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

I följande steg ska du köra det här exemplet R-skriptet i SQL-databasen:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med att ansluta [, se snabb start: Använda SQL Server Management Studio för att ansluta till och köra frågor mot en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till den lagrade proceduren [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skriptet skickas genom `@script` argumentet. Allting i `@script` argumentet måste vara en giltig R-kod.

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
   > Om du är administratör kan du köra extern kod automatiskt. Du kan bevilja behörighet till andra användare med hjälp av kommandot:
   <br>**bevilja körning av ett externt skript till** *användarnamn\>. \<*

2. Rätt resultat beräknas och funktionen R `print` returnerar resultatet till fönstret **meddelanden** .

   Det bör se ut ungefär så här.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Köra ett Hello World-skript

Ett typiskt exempel skript är en som bara matar ut strängen "Hello World". Kör följande kommando.

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
| @language | definierar det språk tillägg som ska anropas, i det här fallet R |
| @script | definierar de kommandon som skickas till R-körningsmiljön. Hela R-skriptet måste omges av det här argumentet, som Unicode-text. Du kan också lägga till texten i en variabel av typen **nvarchar** och sedan anropa variabeln |
| @input_data_1 | data som returneras av frågan skickas till R-körningen, som returnerar data till SQL Server som en data ram |
|MED RESULTAT UPPSÄTTNINGAR | -satsen definierar schemat för den returnerade data tabellen för SQL Server, Lägg till "Hello World" som kolumn namn, **int** för data typen |

Kommandot matar ut följande text:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Använda indata och utdata

Som standard godkänner [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) en enda data uppsättning som indata, som vanligt vis anges i form av en giltig SQL-fråga. Den returnerar sedan en enskild R data-ram som utdata.

Nu ska vi använda standardvariablerna indata och utdata för [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** och **OutputDataSet**.

1. Skapa en liten tabell med test data.

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

1. `SELECT` Använd instruktionen för att fråga tabellen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Innehållet i tabellen RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Kör följande R-skript. Data hämtas från tabellen med `SELECT` instruktionen, skickas via R-körningen och returnerar data som en data ram. Satsen definierar schemat för den returnerade data tabellen för SQL Database och lägger till kolumn namnet *NewColName.* `WITH RESULT SETS`

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Utdata från R-skript som returnerar data från en tabell](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Nu ska vi ändra namnen på indata-och utdata-variablerna. Standardvärdena för in-och utdata-variabel är **InputDataSet** och **OutputDataSet**, men det här skriptet ändrar namnen till **SQL_in** och **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observera att R är Skift läges känsligt. Variablerna indata och utdata som används i R-skriptet (**SQL_out**, **SQL_in**) måste matcha värdena som definieras `@input_data_1_name` med `@output_data_1_name`och, inklusive Case.

   > [!TIP]
   > Endast en indatamängd kan skickas som parameter, och du kan returnera endast en datamängd. Dock kan du anropa andra datamängder i R-koden och returnera utdata för andra typer utöver datamängden. Du kan även lägga till nyckelordet OUTPUT till valfri parameter så att den returneras med resultatet.

1. Du kan också generera värden som bara använder R-skriptet utan indata (`@input_data_1` är inställt på tomt).

   Följande skript skriver ut texten "Hello" och "World".

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

    ![Köra frågor mot resultat med hjälp av @script som indata](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrollera R-version

Om du vill se vilken version av R som är installerad i SQL-databasen kör du följande skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R-funktionen `print` returnerar versionen till fönstret **Meddelanden**. I exemplet nedan kan du se att SQL Database i det här fallet har R version 3.4.4 installerat.

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

Om du vill se en lista över vilka R-paket som är installerade, inklusive version, beroenden, licens och sökväg till biblioteks Sök vägar, kör du följande skript.

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

Utdata kommer från `installed.packages()` i R och returneras som en resultat uppsättning.

**Results**

![Installerade paket i R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Nästa steg

Följ den här snabb starten om du vill skapa en maskin inlärnings modell med R i SQL Database:

> [!div class="nextstepaction"]
> [Skapa och träna en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)](sql-database-quickstart-r-train-score-model.md)

Mer information om Azure SQL Database Machine Learning Services med R (för hands version) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (för hands version)](sql-database-machine-learning-services-overview.md)
- [Skriv avancerade R-funktioner i Azure SQL Database att använda Machine Learning Services (förhands granskning)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R-och SQL-data i Azure SQL Database Machine Learning Services (för hands version)](sql-database-machine-learning-services-data-issues.md)
