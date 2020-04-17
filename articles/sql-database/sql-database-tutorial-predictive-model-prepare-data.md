---
title: 'Självstudiekurs: Förbereda data för att träna en prediktiv modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av den här självstudieserien i tre delar förbereder du data från en Azure SQL-databas för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bf69d2963c74723cb3fea542e28288e4f136d5c3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458768"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudiekurs: Förbereda data för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del ett av den här självstudieserien i tre delar importerar och förbereder du data från en Azure SQL-databas med R. Senare i den här serien använder du dessa data för att träna och distribuera en modell för automatisk maskininlärning i R med Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

För den här handledningen serien, tänk dig att du äger en skiduthyrning företag och du vill förutsäga antalet hyror som du har på ett framtida datum. Denna information hjälper dig att få ditt lager, personal och faciliteter redo.

I del ett och två i den här serien ska du utveckla några R-skript i RStudio för att förbereda dina data och träna en maskininlärningsmodell. Sedan, i del tre, ska du köra dessa R-skript i en SQL-databas med hjälp av lagrade procedurer.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Importera en exempeldatabas till en Azure SQL-databas med R
> * Läsa in data från Azure SQL-databasen i en R-dataram
> * Förbereda data i R genom att identifiera vissa kolumner som kategoriska

I [del två](sql-database-tutorial-predictive-model-build-compare.md)får du lära dig hur du skapar och tränar flera maskininlärningsmodeller i R och väljer sedan den mest exakta.

I [del tre](sql-database-tutorial-predictive-model-deploy.md)får du lära dig att lagra modellen i en databas och sedan skapa lagrade procedurer från R-skript som du har utvecklat i del ett och två. De lagrade procedurerna körs i en SQL-databas för att göra förutsägelser baserat på nya data.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

* [Azure SQL Database med Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md) aktiverat.

* Paketet RevoScaleR - Se [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) för alternativ för att installera paketet lokalt.

* R IDE - Den här självstudien använder [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-frågeverktyget - Den här självstudien förutsätter att du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importera exempeldatabasen

Exempeldatauppsättningen som används i den här självstudien har sparats i en .bacpac-databassäkerhetskopieringsfil som du kan hämta och använda. **.bacpac**

1. Ladda ner filen [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Följ anvisningarna i [Importera en BACPAC-fil för att skapa en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-import)med hjälp av följande information:

   * Importera från **filen TutorialDB.bacpac** som du hämtade
   * Under den offentliga förhandsversionen väljer du **Gen5/vCore-konfigurationen** för den nya databasen
   * Namnge den nya databasen "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Läsa in data i en dataram

Om du vill använda data i R läser du in data från`rentaldata`Azure SQL-databasen i en dataram ( ).

Skapa en ny RScript-fil i RStudio och kör följande skript. Ersätt **Server,** **UID**och **PWD** med din egen anslutningsinformation.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Du bör se resultat som liknar följande.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Förbereda data

I den här exempeldatabasen har det mesta av preparatet redan gjorts, men du ska göra ytterligare en förberedelse här.
Använd följande R-skript för att identifiera tre kolumner som *kategorier* genom att ändra datatyperna till *faktor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Du bör se resultat som liknar följande.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Uppgifterna förbereds nu för utbildning.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med den här självstudien tar du bort TutorialDB-databasen från din Azure SQL Database-server.

Gör så här på Azure-portalen:

1. Välj **Alla resurser** eller **SQL-databaser**på menyn till vänster i Azure-portalen .
1. I fältet **Filter efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudieserien har du slutfört följande steg:

* Importera en exempeldatabas till en Azure SQL-databas med R
* Läsa in data från Azure SQL-databasen i en R-dataram
* Förbereda data i R genom att identifiera vissa kolumner som kategoriska

Om du vill skapa en maskininlärningsmodell som använder data från TutorialDB-databasen följer du del två i den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-predictive-model-build-compare.md)
