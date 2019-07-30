---
title: 'Självstudier: Förbereda data för att utföra klustring i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av den här själv studie serien i tre delar förbereder du data från en Azure SQL-databas för att utföra klustring i R med Azure SQL Database Machine Learning Services (för hands version).
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
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639968"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Förbereda data för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhands granskning)

I del ett av den här själv studie serien i tre delar ska du importera och förbereda data från en Azure SQL-databas med R. Senare i den här serien använder du dessa data för att träna och distribuera en kluster modell i R med Azure SQL Database Machine Learning Services (för hands version).

*Klustring* kan förklaras som att organisera data i grupper där medlemmarna i en grupp liknar varandra på något sätt.
Du använder algoritmen **K-=** för att utföra klustringen av kunder i en data uppsättning av produkt inköp och returer. Genom att klustra kunderna kan du fokusera dina marknadsförings ansträngningar mer effektivt genom att rikta in dig på specifika grupper.
K-betyder klustring är en *övervakad utbildningskurs* som söker efter mönster i data baserat på likheter.

I delar en och två av serien utvecklar du några R-skript i RStudio för att förbereda dina data och träna en maskin inlärnings modell. Sedan kan du i del tre köra dessa R-skript i en SQL-databas med hjälp av lagrade procedurer.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Importera en exempel databas till en Azure SQL-databas
> * Separera kunder med olika dimensioner med R
> * Läsa in data från Azure SQL-databasen till en R data-ram

I [del två](sql-database-tutorial-clustering-model-build.md)får du lära dig hur du skapar och tränar en K-metod kluster modell i R.

I [del tre](sql-database-tutorial-clustering-model-deploy.md)får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring i R baserat på nya data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – om du inte har en Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/) innan du börjar.

* Azure SQL Database Server med Machine Learning Services aktive rad – under den offentliga för hands versionen kommer Microsoft att publicera dig och aktivera maskin inlärning för befintliga eller nya databaser. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

* RevoScaleR-paket – se [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) för alternativ för att installera det här paketet lokalt.

* R IDE – i den här självstudien används [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Verktyget SQL-fråga – den här självstudien förutsätter att du använder [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importera exempel databasen

Exempel data uppsättningen som används i den här självstudien har sparats i en **. bacpac** -databas som du kan hämta och använda. Den här data uppsättningen härleds från [tpcx-BB-](http://www.tpc.org/tpcx-bb/default.asp) datauppsättningen som tillhandahålls av tjänsten [Transaction Processing Performance (TPC)](http://www.tpc.org/default.asp).

1. Hämta filen [tpcxbb_1gb. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Följ anvisningarna i [Importera en BACPAC-fil för att skapa en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-import)med hjälp av följande uppgifter:

   * Importera från **tpcxbb_1gb. bacpac** -filen som du laddade ned
   * Under den allmänt tillgängliga för hands versionen väljer du konfigurationen **Gen5/vCore** för den nya databasen
   * Namnge den nya databasen "tpcxbb_1gb"

## <a name="separate-customers"></a>Separata kunder

Skapa en ny RScript-fil i RStudio och kör följande skript.
I SQL-frågan ska du åtskilja kunderna i följande dimensioner:

* **orderRatio** = retur order kvot (totalt antal beställningar som delvis eller helt returnerade jämfört med det totala antalet beställningar)
* **itemsRatio** = returnera objekts kvot (totalt antal returnerade objekt jämfört med antalet köpta objekt)
* **monetaryRatio** = retur belopps kvot (total belopps summa för artiklar som returneras jämfört med det belopp som köpts)
* **frekvens** = retur frekvens

I funktionen **Klistra in** ersätter du **Server**, **UID**och **PWD** med din egen anslutnings information.

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

## <a name="load-the-data-into-a-data-frame"></a>Läsa in data i en data ram

Använd nu följande skript för att returnera resultatet från frågan till en R data-ram med funktionen **rxSqlServerData** .
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

***Om du inte kommer att fortsätta med den här***självstudien tar du bort tpcxbb_1gb-databasen från Azure SQL Database-servern.

Följ de här stegen i Azure Portal:

1. Välj **alla resurser** eller **SQL-databaser**på den vänstra menyn i Azure Portal.
1. I fältet **Filtrera efter namn...** anger du **tpcxbb_1gb**och väljer din prenumeration.
1. Välj din **tpcxbb_1gb** -databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här själv studie serien slutförde du följande steg:

* Importera en exempel databas till en Azure SQL-databas
* Separera kunder med olika dimensioner med R
* Läsa in data från Azure SQL-databasen till en R data-ram

Om du vill skapa en maskin inlärnings modell som använder den här kund informationen följer du del två i den här själv studie serien:

> [!div class="nextstepaction"]
> [Självstudier: Skapa en förutsägelse modell i R med Azure SQL Database Machine Learning Services (förhands granskning)](sql-database-tutorial-clustering-model-build.md)
