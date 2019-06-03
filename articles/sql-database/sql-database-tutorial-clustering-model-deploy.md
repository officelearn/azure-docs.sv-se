---
title: 'Självstudier: Distribuera en klustringsmodell i R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: I den tredje delen av självstudieserien i tre delar, ska du distribuera en klustringsmodell i R med Azure SQL Database Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419757"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Självstudier: Distribuera en klustringsmodell i R med Azure SQL Database Machine Learning Services (förhandsversion)

I den tredje delen av självstudieserien i tre delar, ska du distribuera en klustringsmodell i R med Azure SQL Database Machine Learning Services (förhandsversion).

Med en inbäddad R-skript som utför kluster ska du skapa en lagrad procedur. Eftersom din modell körs i Azure SQL database, tränas den enkelt mot data som lagras i databasen.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en lagrad procedur som genererar modellen
> * Utföra klustring i SQL-databas
> * Använd klustring information

I [del ett](sql-database-tutorial-clustering-model-prepare-data.md), du har lärt dig hur du förbereder data från en Azure SQL-databas för att utföra klustring i R.

I [del två](sql-database-tutorial-clustering-model-build.md), du har lärt dig hur du skapar en K-Means modell för att utföra klustring.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Del tre i den här självstudien förutsätter att du har slutfört [ **del ett** ](sql-database-tutorial-clustering-model-prepare-data.md) och [ **del två**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Skapa en lagrad procedur som genererar modellen

Kör följande T-SQL-skript för att skapa den lagrade proceduren. Proceduren återskapar de steg som utvecklats i delarna 1 och 2 i självstudieserien:

* klassificera kunder baserat på deras köp och returnera historik
* Generera fyra kluster för kunder som använder en algoritm för K-Means

Proceduren lagrar de resulterande kund kluster mappningarna i databastabellen **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Utföra klustring i SQL-databas

Nu när du har skapat den lagrade proceduren, kör du följande skript för att utföra klustring.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Kontrollera att den fungerar och att vi faktiskt har listan över kunder och deras mappningar för klustret.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Använd klustring information

Eftersom du har lagrat klustring proceduren i databasen, kan den utföra klustring effektivt mot kundinformation lagras i samma databas. Du kan köra proceduren när dina kunddata uppdateras och använda den uppdaterade klustring informationen.

Anta att du vill skicka en via e-post till kunder i kluster 3, den grupp som har fler aktiva returnerade beteende (du kan se hur de fyra kluster har beskrivs i [del två](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). Följande kod väljer e-postadresserna för kunder i 3-klustret.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Du kan ändra den **r.cluster** värde som ska returneras e-postadresser för kunder i andra kluster.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien kan du ta bort tpcxbb_1gb databasen från Azure SQL Database-servern.

Följ dessa steg från Azure-portalen:

1. I den vänstra menyn i Azure portal, Välj **alla resurser** eller **SQL-databaser**.
1. I den **filtrera efter namn...**  anger **tpcxbb_1gb**, och välj din prenumeration.
1. Välj din **tpcxbb_1gb** databas.
1. Välj **Ta bort** på sidan **Översikt**.

## <a name="next-steps"></a>Nästa steg

I del tre i den här självstudieserien får slutfört du de här stegen:

* Skapa en lagrad procedur som genererar modellen
* Utföra klustring i SQL-databas
* Använd klustring information

Mer information om hur du använder R i Azure SQL Database Machine Learning Services (förhandsversion) finns:

* [Självstudie: Förbereda data för att träna en förutsägande modell i R med Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
* [Arbeta med R och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-data-issues.md)
* [Lägga till ett R-paket till Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-add-r-packages.md)
