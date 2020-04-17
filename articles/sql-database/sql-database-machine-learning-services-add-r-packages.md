---
title: Lägga till ett R-paket i Machine Learning Services (förhandsversion)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I den här artikeln beskrivs hur du installerar ett R-paket som inte redan är installerat i Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453344"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Lägga till ett R-paket i Azure SQL Database Machine Learning Services (förhandsversion)

I den här artikeln beskrivs hur du lägger till ett R-paket i Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Installera [R-](https://www.r-project.org) och [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) på den lokala datorn. R är tillgängligt för Windows, macOS och Linux. Den här artikeln förutsätter att du använder Windows.

- Den här artikeln innehåller ett exempel på hur du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) för att köra ett R-skript i Azure SQL Database. Du kan köra R-skript med andra databashanterings- eller frågeverktyg, men det här exemplet förutsätter Azure Data Studio eller SSMS.
   
> [!NOTE]
> Du kan inte installera ett paket genom att köra ett R-skript med **sp_execute_external_script** i Azure Data Studio eller SSMS. Du kan bara installera och ta bort paket med kommandot line och RStudio enligt beskrivningen i den här artikeln. När paketet har installerats kan du komma åt paketfunktionerna i ett R-skript med **sp_execute_external_script**.

## <a name="list-r-packages"></a>Lista R-paket

Microsoft tillhandahåller ett antal R-paket förinstallerade med Machine Learning Services i din Azure SQL-databas.
Du kan se en lista över installerade R-paket genom att köra följande kommando i Azure Data Studio eller SSMS.

1. Öppna Azure Data Studio eller SSMS och anslut till din Azure SQL-databas.

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

Utdata ska se ut ungefär så här.

**Resultat**

![Installerade paket i R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Lägga till ett paket med sqlmlutils

Om du behöver använda ett paket som inte redan är installerat i din Azure SQL-databas kan du installera det med [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** är ett paket som utformats för att hjälpa användare att interagera med SQL-databaser (SQL Server och Azure SQL Database) och köra R- eller Python-kod i SQL från en R- eller Python-klient. För närvarande stöds endast **R-versionen av sqlmlutils** i Azure SQL Database.

I följande exempel ska du installera **[limpaketet](https://cran.r-project.org/web/packages/glue/)** som kan formatera och interpolera strängar. Dessa steg installerar **sqlmlutils** och **RODBCext** (en förutsättning för **sqlmlutils**) och lägger till **limpaketet.**

### <a name="install-sqlmlutils"></a>Installera **sqlmlutils**

1. Ladda ner den senaste **zip-filen från sqlmlutils** till https://github.com/Microsoft/sqlmlutils/tree/master/R/dist din lokala dator. Du behöver inte packa upp filen.

1. Öppna en **kommandotolk** och kör följande kommandon för att installera **RODBCext** och **sqlmlutils** på din lokala dator. Ersätt den fullständiga sökvägen till **zip-filen sqlmlutils** som du hämtade (exemplet förutsätter att filen finns i mappen Dokument).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Utdata som visas bör likna följande.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Om du får felmeddelandet " 'R' känns inte igen som ett internt eller externt kommando, funktionsdugligt program eller kommandofil", betyder det troligen att sökvägen till R.exe inte ingår i din **PATH-miljövariabel** i Windows. Du kan antingen lägga till sökvägen i miljövariabeln eller `cd C:\Program Files\R\R-3.5.3\bin`navigera till mappen i kommandotolken (till exempel) och sedan försöka igen kommandot.

### <a name="add-the-package"></a>Lägg till paketet

1. Öppna RStudio och skapa en ny **R-skriptfil**. 

1. Använd följande R-kod för att installera **limpaketet** med **sqlmlutils**. Ersätt din egen Azure SQL Database-anslutningsinformation.

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
    > **Omfattningen** kan vara antingen **offentlig** eller **privat**. Det offentliga omfånget är användbart för databasadministratören för att installera paket som alla användare kan använda. Privat scope gör paketet endast tillgängligt för den användare som installerar det. Om du inte anger omfånget blir standardomfånget **PRIVATE**.

### <a name="verify-the-package"></a>Verifiera paketet

Kontrollera att **limpaketet** har installerats genom att köra följande R-skript i RStudio. Använd samma **anslutning** som du definierade i föregående steg.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultat**

![Innehållet i tabellen RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Använd paketet

När paketet har installerats kan du använda det i ett R-skript via **sp_execute_external_script**.

1. Öppna Azure Data Studio eller SSMS och anslut till din Azure SQL-databas.

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

    Följande resultat visas på fliken **Meddelanden.**

    **Resultat**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Ta bort paketet

Om du vill ta bort paketet kör du följande R-skript i RStudio. Använd samma **anslutning** som du definierade tidigare.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Ett annat sätt att installera ett R-paket till din Azure SQL-databas är att ladda upp R-paketet från en byteström med hjälp av **CREATE EXTERNAL LIBRARY** T-SQL-uttrycket. Se [Skapa ett bibliotek från en byteström](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) i referensdokumentationen SKAPA [EXTERNT BIBLIOTEK.](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure SQL Database Machine Learning Services med R (förhandsversion) finns i följande artiklar.

- [Azure SQL Database Machine Learning Services med R (förhandsgranskning)](sql-database-machine-learning-services-overview.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
- [Arbeta med R- och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)