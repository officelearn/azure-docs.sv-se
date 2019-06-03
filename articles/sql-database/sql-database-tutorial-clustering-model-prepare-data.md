---
title: 'Självstudier: Förbereda data för att utföra klustring i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I del ett av självstudieserien i tre delar förbereder du data från en Azure SQL-databasen för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.date: 05/17/2019
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419463"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Förbereda data för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion)

I del ett av självstudieserien i tre delar förbereder du data från en Azure SQL-databasen för att utföra klustring i R med Azure SQL Database Machine Learning Services (förhandsversion).

*Klustring* kan förklaras som att ordna data i grupper där medlemmar i en grupp är liknande på något sätt.
Du kommer att använda den **K-Means** algoritmen för att genomföra klustring av kunder i en datauppsättning för produkten köper och returnerar. Av klustring kunder, kan du fokusera marknadsföringsarbetet effektivare genom att rikta specifika grupper.
K-Means klustring är en *oövervakad inlärning* algoritmen som söker efter mönster i data baserat på likheter.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Importera en exempeldatabas till en Azure SQL database
> * Separata kunder i dimensioner
> * Läs in data från Azure SQL-databas till en dataram med R

I [del två](sql-database-tutorial-clustering-model-build.md), får du lära dig hur du skapar och träna en klustringsmodell för K-Means.

I [del tre](sql-database-tutorial-clustering-model-deploy.md), får du lära dig hur du skapar en lagrad procedur i en Azure SQL-databas som kan utföra klustring baserat på nya data.

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

Exempel-datauppsättningen som används i den här självstudien har sparats till en **.bacpac** säkerhetskopian av databasen som du kan hämta och använda. Den här datauppsättningen härleds från den [tpcx bb](http://www.tpc.org/tpcx-bb/default.asp) datauppsättning som tillhandahålls av den [transaktion bearbetning av prestanda rådet (TPC)](http://www.tpc.org/default.asp).

1. Ladda ned filen [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Följ anvisningarna i [importera en BACPAC-fil för att skapa en Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-import), med hjälp av följande information:

   * Importera från den **tpcxbb_1gb.bacpac** filen du laddade ned
   * Under den offentliga förhandsversionen kan välja den **Gen5/vCore** konfigurationen för den nya databasen
   * Namnge den nya databasen ”tpcxbb_1gb”

## <a name="separate-customers"></a>Separata kunder

Skapa en ny RScript-fil i RStudio och kör följande skript.
Du är att avgränsa kunder i följande dimensioner i SQL-frågan:

* **orderRatio** = batch-förhållande (totalt antal beställningar helt eller delvis returnerade jämfört med det totala antalet beställningar)
* **itemsRatio** = Returnerad artikel-förhållande (totalt antal objekt som returneras jämfört med antal objekt som har köpt)
* **monetaryRatio** = returnerade belopp-förhållande (totala beloppet för objekt som returneras jämfört med den mängd som har köpt)
* **frekvens** = returnerade frekvens

I den **klistra in** fungera, Ersätt **Server**, **UID**, och **PWD** med din egen anslutningsinformation.

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

Nu använda följande skript för att returnera resultat från frågan till en R data frame med hjälp av den **rxSqlServerData** funktion.
Som en del av processen, ska du definiera typ för de markerade kolumnerna (med colClasses) att se till att typerna överförs korrekt till R.

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

***Om du inte planerar att fortsätta med den här självstudien***, ta bort tpcxbb_1gb databasen från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **tpcxbb_1gb**, och välj din prenumeration.
1. Välj din **tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudieserien får slutfört du de här stegen:

* Importera en exempeldatabas till en Azure SQL database
* Separata kunder i dimensioner
* Läs in data från Azure SQL-databas till en dataram med R

Om du vill skapa en machine learning-modell som använder den här kunddata, följer du del två i den här självstudien:

> [!div class="nextstepaction"]
> [Självstudie: Skapa en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-clustering-model-build.md)
