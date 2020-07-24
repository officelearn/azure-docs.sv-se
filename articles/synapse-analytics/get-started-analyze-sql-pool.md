---
title: 'Självstudie: komma igång med att analysera data med SQL-pool'
description: I den här självstudien använder du NYC taxi-exempel data för att utforska SQL-poolens analys funktioner.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 948de6968d336d16554fe0a0c2f7d0c5b7367f15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101840"
---
# <a name="analyze-data-with-sql-pools"></a>Analysera data med SQL-pooler

Azure Synapse Analytics ger dig möjlighet att analysera data med SQL-poolen. I den här självstudien använder du NYC taxi-exempel data för att utforska SQL-poolens analys funktioner.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Läs in NYC taxi-exempelprogrammet i SQLDB1-databasen

1. På den översta blå menyn i Synapse Studio väljer du ikonen frågetecken (**?**).
1. Välj **Kom igång**  >  **kom**igång-hubb.
1. På kortet med etiketten **fråga exempel data**väljer du SQL-poolen med namnet **SQLDB1**.
1. Välj **fråga efter data**. Ett meddelande om att läsa in exempel data visas kort. Ett ljust blått statusfält längst upp i Synapse Studio anger att data läses in i SQLDB1.
1. När statusfältet är grönt stänger du det.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Utforska NYC taxi-data i SQL-poolen

1. Gå till **datahubben** i Synapse Studio.
1. Gå till **SQLDB1**-  >  **tabeller**. Du ser att flera tabeller har lästs in.
1. Högerklicka på **dbo. Rese** tabell och välj **nytt SQL-skript**  >  **Markera de 100 översta raderna**.
1. Vänta medan ett nytt SQL-skript skapas och körs.
1. Observera att överst i SQL-skriptet **Connect to** anges automatiskt till SQL-poolen med namnet **SQLDB1**.
1. Ersätt texten i SQL-skriptet med den här koden och kör den.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Den här frågan visar hur det totala antalet rese avstånd och det genomsnittliga rese avståndet avser antalet passagerare.
1. I resultat fönstret för SQL-skript ändrar du **vyn** till **diagram** för att visa en visualisering av resultaten som ett linje diagram.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med Spark](get-started-analyze-spark.md)

