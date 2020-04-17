---
title: 'Självstudiekurs: Förbereda data för att utföra klustring i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av den här självstudieserien i tre delar förbereder du data från en Azure SQL-databas för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452884"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudiekurs: Förbereda data för att utföra kluster i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del ett av den här självstudieserien i tre delar importerar och förbereder du data från en Azure SQL-databas med R. Senare i den här serien ska du använda dessa data för att träna och distribuera en klustermodell i R med Azure SQL Database Machine Learning Services (förhandsversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Klustring* kan förklaras som att organisera data i grupper där medlemmar i en grupp är likartade på något sätt.
Du använder **K-Means-algoritmen** för att utföra klustring av kunder i en datauppsättning av produktinköp och returer. Genom att samla kunder kan du fokusera dina marknadsföringsinsatser mer effektivt genom att rikta in dig på specifika grupper.
K-Means-klustring är en *oövervakad inlärningsalgoritm* som söker efter mönster i data baserat på likheter.

I del ett och två i den här serien ska du utveckla några R-skript i RStudio för att förbereda dina data och träna en maskininlärningsmodell. Sedan, i del tre, ska du köra dessa R-skript i en SQL-databas med hjälp av lagrade procedurer.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Importera en exempeldatabas till en Azure SQL-databas
> * Separata kunder längs olika dimensioner med R
> * Läsa in data från Azure SQL-databasen i en R-dataram

I [del två](sql-database-tutorial-clustering-model-build.md)får du lära dig hur du skapar och tränar en K-Means-klustermodell i R.

I [del tre](sql-database-tutorial-clustering-model-deploy.md)får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring i R baserat på nya data.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

* [Azure SQL Database med Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md) aktiverat.

* Paketet RevoScaleR - Se [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) för alternativ för att installera paketet lokalt.

* R IDE - Den här självstudien använder [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-frågeverktyget - Den här självstudien förutsätter att du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importera exempeldatabasen

Exempeldatauppsättningen som används i den här självstudien har sparats i en .bacpac-databassäkerhetskopieringsfil som du kan hämta och använda. **.bacpac** Den här datauppsättningen härleds från [datauppsättningen tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) som tillhandahålls av [TPC (Transaction Processing Performance Council).](http://www.tpc.org/default.asp)

1. Ladda ner filen [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Följ anvisningarna i [Importera en BACPAC-fil för att skapa en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-import)med hjälp av följande information:

   * Importera från **filen tpcxbb_1gb.bacpac** som du hämtade
   * Under den offentliga förhandsversionen väljer du **Gen5/vCore-konfigurationen** för den nya databasen
   * Namnge den nya databasen "tpcxbb_1gb"

## <a name="separate-customers"></a>Separata kunder

Skapa en ny RScript-fil i RStudio och kör följande skript.
I SQL-frågan separerar du kunder efter följande dimensioner:

* **orderRatio** = returorderförhållande (totalt antal order som helt eller delvis returnerats jämfört med det totala antalet order)
* **itemsRatio** = returartikelförhållande (totalt antal returnerade artiklar jämfört med antalet inköpta artiklar)
* **monetaryRatio** = avkastningsbelopp (totalt monetärt belopp för poster som returneras jämfört med det köpta beloppet)
* **frekvens** = returfrekvens

I **inklistringsfunktionen** ersätter **du Server,** **UID**och **PWD** med din egen anslutningsinformation.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Läsa in data i en dataram

Använd nu följande skript för att returnera resultaten från frågan till en R-dataram med hjälp av funktionen **rxSqlServerData.**
Som en del av processen definierar du typen för de markerade kolumnerna (med colClasses) för att se till att typerna överförs korrekt till R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Du bör se resultat som liknar följande.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med den ***här självstudien***tar du bort tpcxbb_1gb-databasen från din Azure SQL Database-server.

Gör så här på Azure-portalen:

1. Välj **Alla resurser** eller **SQL-databaser**på menyn till vänster i Azure-portalen .
1. I fältet **Filtrera efter namn...** anger du **tpcxbb_1gb**och väljer din prenumeration.
1. Välj **din tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudieserien har du slutfört följande steg:

* Importera en exempeldatabas till en Azure SQL-databas
* Separata kunder längs olika dimensioner med R
* Läsa in data från Azure SQL-databasen i en R-dataram

Om du vill skapa en maskininlärningsmodell som använder dessa kunddata följer du del två i den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-clustering-model-build.md)
