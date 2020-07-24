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
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101813"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analysera data med SQL på begäran

I den här självstudien får du lära dig hur du analyserar data med SQL på begäran med hjälp av data som finns i Spark-databaser. 

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
