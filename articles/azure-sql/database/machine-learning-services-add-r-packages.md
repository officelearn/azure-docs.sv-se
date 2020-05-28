---
title: Lägg till ett R-paket i Machine Learning Services (för hands version)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Den här artikeln beskriver hur du installerar ett R-paket som inte redan är installerat i Azure SQL Database Machine Learning Services (för hands version).
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
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 289b1346fce2e79a2c1e546f5e42e98734ef800c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053569"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Lägg till ett R-paket i Azure SQL Database Machine Learning Services (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs hur du lägger till ett R-paket i Azure SQL Database Machine Learning Services (för hands version).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Installera [R](https://www.r-project.org) -och [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) på den lokala datorn. R är tillgängligt för Windows, macOS och Linux. I den här artikeln förutsätter vi att du använder Windows.

- Den här artikeln innehåller ett exempel på hur du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) för att köra ett R-skript i Azure SQL Database. Du kan köra R-skript med hjälp av andra databas hanterings-eller specialverktyg, men det här exemplet förutsätter Azure Data Studio eller SSMS.
   
> [!NOTE]
> Du kan inte installera ett paket genom att köra ett R-skript med **sp_execute_external_script** i Azure Data Studio eller SSMS. Du kan bara installera och ta bort paket med kommando raden R och RStudio enligt beskrivningen i den här artikeln. När paketet har installerats kan du komma åt paket funktionerna i ett R-skript med hjälp av **sp_execute_external_script**.

## <a name="list-r-packages"></a>Lista R-paket

Microsoft tillhandahåller ett antal R-paket som är förinstallerade med Machine Learning Services i din Azure SQL-databas.
Du kan se en lista över installerade R-paket genom att köra följande kommando i Azure Data Studio eller SSMS.

1. Öppna Azure Data Studio eller SSMS och Anslut till din Azure SQL Database.

1. Kör följande kommando:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Utdata bör se ut ungefär så här.

**Gör**

![Installerade paket i R](./media/machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Lägg till ett paket med sqlmlutils

Om du behöver använda ett paket som inte redan har installerats i Azure SQL Database kan du installera det med hjälp av [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** är ett paket utformat för att hjälpa användarna att interagera med Microsoft Azure SQL och SQL Server-databaser och köra R-eller python-kod i SQL från en R-eller python-klient. För närvarande stöds endast R-versionen av **sqlmlutils** i Azure SQL Database.

I följande exempel ska du installera det **[lim](https://cran.r-project.org/web/packages/glue/)** -paket som kan formatera och interpolera strängar. De här stegen installerar **sqlmlutils** och **RODBCext** (ett krav för **sqlmlutils**) och lägger till ett **lim** -paket.

### <a name="install-sqlmlutils"></a>Installera **sqlmlutils**

1. Ladda ned den senaste **sqlmlutils** zip-filen från https://github.com/Microsoft/sqlmlutils/tree/master/R/dist till den lokala datorn. Du behöver inte packa upp filen.

1. Öppna en **kommando tolk** och kör följande kommandon för att installera **RODBCext** och **sqlmlutils** på den lokala datorn. Ersätt den fullständiga sökvägen till den **sqlmlutils** zip-fil som du laddade ned (exemplet förutsätter att filen finns i mappen dokument).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    De utdata du ser ser ut ungefär så här.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Om du får felet "' R ' inte känns igen som ett internt eller externt kommando, fungerande program eller en kommando fil", betyder det förmodligen att sökvägen till R. exe inte ingår i din **Path** -miljövariabel i Windows. Du kan antingen lägga till sökvägen till miljövariabeln eller navigera till mappen i kommando tolken (till exempel `cd C:\Program Files\R\R-3.5.3\bin` ) och sedan köra kommandot igen.

### <a name="add-the-package"></a>Lägg till paketet

1. Öppna RStudio och skapa en ny **R-skriptfil**. 

1. Använd följande R-kod för att installera ett **lim** -paket med **sqlmlutils**. Ersätt din egen Azure SQL Database anslutnings information.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Omfattningen** kan vara antingen **offentlig** eller **privat**. Det offentliga omfånget är användbart för databasadministratören för att installera paket som alla användare kan använda. Privat omfattning gör paketet bara tillgängligt för den användare som installerar det. Om du inte anger omfånget blir standardomfånget **PRIVATE**.

### <a name="verify-the-package"></a>Verifiera paketet

Kontrol lera att **Glue** -paketet har installerats genom att köra följande R-skript i RStudio. Använd samma **anslutning** som du definierade i föregående steg.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Gör**

![Innehållet i tabellen RTestData](./media/machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Använda paketet

När paketet har installerats kan du använda det i ett R-skript via **sp_execute_external_script**.

1. Öppna Azure Data Studio eller SSMS och Anslut till din Azure SQL Database.

1. Kör följande kommando:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
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

    Följande resultat visas på fliken **meddelanden** .

    **Gör**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Ta bort paketet

Om du vill ta bort paketet kör du följande R-skript i RStudio. Använd samma **anslutning** som du definierade tidigare.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Ett annat sätt att installera ett R-paket i Azure SQL Database är att ladda upp R-paketet från en byte-dataström med hjälp av instruktionen **Skapa externt bibliotek** T-SQL. Mer information finns i [skapa ett bibliotek från en byte-dataström](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) i referens dokumentationen för att [skapa externa bibliotek](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) .

## <a name="next-steps"></a>Nästa steg

Mer information om Azure SQL Database Machine Learning Services med R (för hands version) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (för hands version)](machine-learning-services-overview.md)
- [Skriv avancerade R-funktioner i Azure SQL Database att använda Machine Learning Services (förhands granskning)](machine-learning-services-functions.md)
- [Arbeta med R-och SQL-data i Azure SQL Database Machine Learning Services (för hands version)](machine-learning-services-data-issues.md)