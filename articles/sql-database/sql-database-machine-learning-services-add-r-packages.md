---
title: Lägga till ett R-paket till Azure SQL Database Machine Learning Services (förhandsversion)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Den här artikeln beskriver hur du installerar ett R-paket som inte är installerad i Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927102"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Lägga till ett R-paket till Azure SQL Database Machine Learning Services (förhandsversion)

Den här artikeln förklarar hur du lägger till ett R-paket till Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Installera [R](https://www.r-project.org) och [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) på den lokala datorn. R är tillgängligt för Windows, macOS och Linux. Den här artikeln förutsätter att du använder Windows.

- Den här artikeln innehåller ett exempel på hur du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) för att köra ett R-skript i Azure SQL Database. Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men det här exemplet förutsätter Azure Data Studio eller SSMS.
   
> [!NOTE]
> Du kan inte installera ett paket genom att köra ett R-skript med **sp_execute_external_script** i Azure Data Studio eller SSMS. Du kan bara installera och ta bort paket med hjälp av kommandoraden för R och RStudio enligt beskrivningen i den här artikeln. När paketet har installerats kan du komma åt paketet funktionerna i ett R-skript med **sp_execute_external_script**.

## <a name="list-r-packages"></a>Lista R-paket

Microsoft tillhandahåller ett antal R-paket som är förinstallerade med Machine Learning-tjänster i din Azure SQL-databas.
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

**Results**

![Installerade paket i R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Lägg till ett paket med sqlmlutils

Om du behöver använda ett paket som inte är installerad i din Azure SQL Database kan du installera den med hjälp av [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** är ett paket som har utformats för att hjälpa användarna att interagera med SQL-databaser (SQL Server och Azure SQL Database) och köra R eller Python-kod i SQL på en R eller Python-klient. För närvarande bara den R versionen av **sqlmlutils** stöds i Azure SQL Database.

I följande exempel installerar du den **[sammanlänkande](https://cran.r-project.org/web/packages/glue/)** paket som kan formatera och interpolera strängar. Installera de här stegen **sqlmlutils** och **RODBCext** (en förutsättning för **sqlmlutils**), och Lägg till den **sammanlänkande** paketet.

### <a name="install-sqlmlutils"></a>Installera **sqlmlutils**

1. Hämta senaste **sqlmlutils** zip-filen från https://github.com/Microsoft/sqlmlutils/tree/master/R/dist till din lokala dator. Du behöver inte packa upp filen.

1. Öppna en **kommandotolk** och kör följande kommandon för att installera **RODBCext** och **sqlmlutils** på den lokala datorn. Ersätt den fullständiga sökvägen till den **sqlmlutils** zip-filen som du hämtade (i exemplet förutsätter att filen finns i mappen dokument).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Du ser utdata ska se ut ungefär så här.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Om du får felet ”” R ”identifieras inte som ett internt eller externt kommando, ett körbart program eller en batchfil”, innebär det sannolikt att sökvägen till R.exe inte ingår i din **sökväg** miljövariabeln på Windows. Du kan lägga till sökvägen i miljövariabeln eller navigera till mappen i Kommandotolken (till exempel `cd C:\Program Files\R\R-3.5.3\bin`) och försök sedan igen.

### <a name="add-the-package"></a>Lägga till paketet

1. Öppna RStudio och skapa en ny **R-skriptet** fil. 

1. Använd följande R-kod för att installera den **sammanlänkande** paketet med hjälp av **sqlmlutils**. Ersätt information om din egen Azure SQL Database.

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
    > Den **omfång** kan vara antingen **offentliga** eller **privata**. Det offentliga omfånget är användbart för databasadministratören för att installera paket som alla användare kan använda. Privata gör paketet tillgänglig endast för den användare som installerar den. Om du inte anger omfånget blir standardomfånget **PRIVATE**.

### <a name="verify-the-package"></a>Verifiera paketet

Kontrollera att den **sammanlänkande** paketet har installerats genom att köra följande R-skriptet i RStudio. Använd samma **anslutning** du definierade i föregående steg.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Innehållet i tabellen RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Använda paketet

När paketet har installerats kan du använda den i ett R-skript via **sp_execute_external_script**.

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

    Du ser följande resultat i den **meddelanden** fliken.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Ta bort paketet

Om du vill ta bort paketet, kör du följande R-skript i RStudio. Använd samma **anslutning** du angav tidigare.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Ett annat sätt att installera ett R-paket till Azure SQL database är att ladda upp R-paket från en byte-dataström med den **CREATE EXTERNAL LIBRARY** T-SQL-instruktionen. Se [skapa ett bibliotek från en dataström med byte](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) i den [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) referensdokumentation.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure SQL Database Machine Learning Services med R (förhandsversion) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (förhandsversion)](sql-database-machine-learning-services-overview.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)