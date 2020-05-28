---
title: 'Självstudie: förbereda data för att träna en förutsägelse modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av den här själv studie serien i tre delar förbereder du data från en Azure SQL-databas för att träna en förutsägelse modell i R med Azure SQL Database Machine Learning Services (för hands version).
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
ms.openlocfilehash: a82467a097c50314e8f26f4a5cc4507f867ad504
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053773"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudie: förbereda data för att träna en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I del ett av den här själv studie serien i tre delar ska du importera och förbereda data från en Azure SQL-databas med R. Senare i den här serien använder du dessa data för att träna och distribuera en förutsägelse maskin inlärnings modell i R med Azure SQL Database Machine Learning Services (för hands version).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

I den här själv studie serien är det dags att du äger en Ski hyra-verksamhet och du vill förutsäga antalet hyror som du har på ett framtida datum. Den här informationen hjälper dig att komma igång med din aktie, personal och dina resurser.

I delar en och två av serien utvecklar du några R-skript i RStudio för att förbereda dina data och träna en maskin inlärnings modell. Sedan kan du i del tre köra dessa R-skript i en SQL-databas med hjälp av lagrade procedurer.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Importera en exempel databas till en Azure SQL-databas med R
> * Läsa in data från Azure SQL-databasen till en R data-ram
> * Förbered data i R genom att identifiera några kolumner som kategoriska

I [del två](predictive-model-build-compare-tutorial.md)får du lära dig hur du skapar och tränar flera Machine Learning-modeller i R och väljer den mest exakta.

I [del tre](predictive-model-deploy-tutorial.md)får du lära dig hur du lagrar modellen i en databas och sedan skapar lagrade procedurer från de R-skript som du utvecklade i delar en och två. De lagrade procedurerna kommer att köras i en SQL-databas för att göra förutsägelser baserade på nya data.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – om du inte har en Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/) innan du börjar.

* [Azure SQL Database med Machine Learning Services (med R)](machine-learning-services-overview.md) aktiverat.

* RevoScaleR-paket – se [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) för alternativ för att installera det här paketet lokalt.

* R IDE – i den här självstudien används [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Verktyget SQL-fråga – den här självstudien förutsätter att du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importera exempel databasen

Exempel data uppsättningen som används i den här självstudien har sparats i en **. bacpac** -databas som du kan hämta och använda.

1. Hämta filen [TutorialDB. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Följ anvisningarna i [Importera en BACPAC-fil för att skapa en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-import)med hjälp av följande uppgifter:

   * Importera från **TutorialDB. bacpac** -filen som du laddade ned
   * Under den allmänt tillgängliga för hands versionen väljer du konfigurationen **Gen5/vCore** för den nya databasen
   * Namnge den nya databasen "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Läsa in data i en data ram

Om du vill använda data i R läser du in data från Azure SQL-databasen i en data ram ( `rentaldata` ).

Skapa en ny RScript-fil i RStudio och kör följande skript. Ersätt **Server**, **UID**och **PWD** med din egen anslutnings information.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
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

I den här exempel databasen har de flesta förberedelser redan gjorts, men du kommer att göra en förberedelse här.
Använd följande R-skript för att identifiera tre kolumner som *Kategorier* genom att ändra data typerna till *faktor*.

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

Nu har data för beretts för utbildning.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta med den här självstudien tar du bort TutorialDB-databasen från servern.

Följ de här stegen i Azure Portal:

1. Välj **alla resurser** eller **SQL-databaser**på den vänstra menyn i Azure Portal.
1. I fältet **Filtrera efter namn...** anger du **TutorialDB**och väljer din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här själv studie serien slutförde du följande steg:

* Importera en exempel databas till en Azure SQL-databas med R
* Läsa in data från Azure SQL-databasen till en R data-ram
* Förbered data i R genom att identifiera några kolumner som kategoriska

Om du vill skapa en maskin inlärnings modell som använder data från TutorialDB-databasen följer du del två i den här själv studie serien:

> [!div class="nextstepaction"]
> [Självstudie: skapa en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)](predictive-model-build-compare-tutorial.md)
