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
ms.openlocfilehash: 363f2934bbeec266c16711572620e03e69785f94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007204"
---
# <a name="analyze-data-with-sql-pools"></a>Analysera data med SQL-pooler

Azure Synapse Analytics ger dig möjlighet att analysera data med SQL-poolen. I den här självstudien använder du NYC taxi-exempel data för att utforska SQL-poolens analys funktioner.

## <a name="link-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Länka NYC taxi-exempel data till SQLDB1-databasen

1. I Synapse Studio navigerar du till **data** hubben till vänster.
1. Klicka på **+** och välj sedan **Bläddra bland exempel**. Då öppnas **exempel centret** och fliken **data uppsättningar** öppnas.
1. Välj **NYC taxi & limousine provision-Yellow taxi rese poster**. Den här data uppsättningen innehåller över 1 500 000 000 rader.
1. Klicka på **Lägg till data uppsättning**
1. I **data** hubben under **länkad** visas en ny data uppsättning på den här platsen **Azure Blob Storage > exempel data uppsättningar > nyc_tlc_yellow**   
1. På kortet med etiketten **fråga exempel data**väljer du SQL-poolen med namnet **SQLDB1**.


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

