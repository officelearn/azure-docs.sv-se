---
title: 'Självstudie: komma igång med att analysera data med SQL på begäran'
description: I den här självstudien får du lära dig hur du analyserar data med SQL på begäran med hjälp av data som finns i Spark-databaser.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 93ebc1c5e89e54f4813f270b9f8b7b13f672fbe3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016120"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analysera data med SQL på begäran

I den här självstudien får du lära dig hur du analyserar data med SQL på begäran med hjälp av data som finns i Spark-databaser. 

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-sql-on-demand"></a>Analysera NYC taxi-data i Blob Storage med SQL på begäran

1. I **data** hubben under **länkad**högerklickar du på **Azure Blob Storage > exempel data uppsättningar > nyc_tlc_yellow** och väljer **Välj de översta 100 raderna**
1. Då skapas ett nytt SQL-skript med följande kod:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Klicka på **Kör**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analysera NYC taxi-data i Spark-databaser med SQL på begäran

Tabeller i Spark-databaser visas automatiskt och de kan frågas efter SQL på begäran.

1. Gå till **utveckla** hubben i Synapse Studio och skapa ett nytt SQL-skript.
1. Ange **Anslut till** **SQL på begäran**.
1. Klistra in följande text i skriptet och kör skriptet.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Första gången du kör en fråga som använder SQL på begäran tar det cirka 10 sekunder för SQL på begäran att samla in de SQL-resurser som krävs för att köra dina frågor. Efterföljande frågor blir mycket snabbare.
  


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med Spark](get-started-analyze-spark.md)
