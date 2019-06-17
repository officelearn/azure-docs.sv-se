---
title: Använda Open Database Connectivity (ODBC) anslutning till Azure Data Explorer för att visualisera data med Tableau
description: I den här artikeln får du lära dig hur du använder en Open Database Connectivity (ODBC)-anslutning till Azure Data Explorer-anslutning för att visualisera data med Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499091"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualisera data från Azure Data Explorer i Tableau

 [Tableau](https://www.tableau.com/) är en visual analytics plattform för business intelligence. För att ansluta till Azure Data Explorer från Tableau och hämta data från ett kluster i exemplet, använder du drivrutinen för SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra den här artikeln:

* [Ansluta till Azure Data Explorer med ODBC](connect-odbc.md) med hjälp av ODBC-drivrutinen för att ansluta till Azure Data Explorer från Tableau. 

* Tableau Desktop, full, eller [utvärderingsversion](https://www.tableau.com/products/desktop/download) version.

* Ett kluster som innehåller exempeldata StormEvents. Mer information finns i [skapa ett Azure Data Explorer-kluster och databasen](create-cluster-database-portal.md) och [mata in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualisera data i Tableau 

När du har konfigurerat ODBC kan du sätta exempeldata i Tableau.

1. I Tableau Desktop på den vänstra menyn väljer **andra databaser (ODBC)** .

    ![Ansluta med ODBC](media/tableau/connect-odbc.png)

1. För **DSN**, Välj den datakälla som du skapade för ODBC och välj sedan **logga In**.

    ![ODBC-inloggning](media/tableau/odbc-sign-in.png)

1. För **databasen**, välja databasen på exemplet klustret, till exempel *TestDatabase*. För **schemat**väljer *dbo*, och för **tabell**väljer den *StormEvents* exempeltabell.

    ![Välj databas och tabell](media/tableau/select-database-table.png)

1. Tableau visas nu att schemat innehåller exempeldata. Välj **Uppdatera nu** att ta med data i Tableau.

    ![Uppdatera data](media/tableau/update-data.png)

    När data importeras visar Tableau datarader liknar följande bild.

    ![Resultatuppsättningen](media/tableau/result-set.png)

1. Nu kan du skapa visualiseringar i Tableau baserat på data som du har med Datautforskaren i Azure. Mer information finns i [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)