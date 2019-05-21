---
title: 'Självstudier: Förbereda data för att träna en förutsägande modell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av självstudieserien i tre delar förbereder du data från en Azure SQL-databas för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.date: 05/02/2019
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978736"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Förbereda data för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del ett av självstudieserien i tre delar förbereder du data från en Azure SQL-databas för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion).

I den här kursen, anta att du äger ett ski hyres-företag och du vill förutsäga antalet cyklar som du har på ett datum i framtiden. Den här informationen hjälper dig att förbereda din lager, personal och verksamhet.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Importera en exempeldatabas till en Azure SQL database
> * Läs in data från Azure SQL-databas till en dataram med R
> * Förbereda data genom att identifiera vissa kolumner som kategoriska

I [del två](sql-database-tutorial-predictive-model-build-compare.md), du får lära dig hur du skapar och skapa flera modeller och väljer sedan det mest korrekta.

I [del tre](sql-database-tutorial-predictive-model-deploy.md), du får lära dig hur du lagrar modellen i en databas och sedan skapa en lagrad procedur som kan göra förutsägelser utifrån nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration – om du inte har någon Azure-prenumeration [skapa ett konto](https://azure.microsoft.com/free/) innan du börjar.

* Azure SQL Database-Server med Machine Learning-tjänster aktiveras - under den offentliga förhandsversionen kan integrera Microsoft kommer du och aktivera machine learning för dina befintliga eller nya databaser. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

* RevoScaleR paket - Se [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) om alternativ för att installera det här paketet lokalt.

* R-IDE - den här självstudien används [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Verktyget för SQL-fråga – den här självstudiekursen förutsätter vi att du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importera exempeldatabasen

Exempel-datauppsättningen som används i den här självstudien har sparats till en **.bacpac** säkerhetskopian av databasen som du kan hämta och använda.

1. Ladda ned filen [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Följ anvisningarna i [importera en BACPAC-fil för att skapa en Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-import), med hjälp av följande information:

   * Importera från den **TutorialDB.bacpac** filen du laddade ned
   * Under den offentliga förhandsversionen kan välja den **Gen5/vCore** konfigurationen för den nya databasen
   * Namnge den nya databasen ”TutorialDB”

## <a name="load-the-data-into-a-data-frame"></a>Läsa in data i en dataram

Om du vill använda data i R, ska du läsa in data från Azure SQL-databas till en dataram (`rentaldata`).

Skapa en ny RScript-fil i RStudio och kör följande skript. Ersätt **Server**, **UID**, och **PWD** med din egen anslutningsinformation.

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

De flesta av förberedelserna redan har gjorts i den här exempeldatabasen, men gör du en mer förberedelser här.
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

Data är nu förberedd för utbildning.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta med den här självstudiekursen, kan du ta bort TutorialDB databasen från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **TutorialDB**, och välj din prenumeration.
1. Välj din TutorialDB-databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudieserien får slutfört du de här stegen:

* Importera en databassäkerhetskopia till en Azure SQL database
* Läs in data från Azure SQL-databas till en dataram med R
* Förbereda data genom att identifiera vissa kolumner som kategorier

Om du vill skapa en machine learning-modell som använder data från databasen TutorialDB, följer du del två i den här självstudien:

> [!div class="nextstepaction"]
> [Självstudie: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-predictive-model-build-compare.md)
