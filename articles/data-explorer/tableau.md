---
title: Använda Azure Data Explorer ODBC-anslutning för att visualisera tabelldata
description: I den här artikeln får du lära dig hur du använder en ODBC-anslutning (Open Database Connectivity) till Azure Data Explorer-anslutning för att visualisera data med Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562453"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualisera data från Azure Data Explorer i Tableau

 [Tableau](https://www.tableau.com/) är en visuell analysplattform för business intelligence. Om du vill ansluta till Azure Data Explorer från Tableau och ta in data från ett exempelkluster använder du ODBC-drivrutinen (SQL Server Open Database Connectivity). 

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra den här artikeln:

* [Anslut till Azure Data Explorer med ODBC](connect-odbc.md) med SQL Server ODBC-drivrutinen för att ansluta till Azure Data Explorer från Tableau. 

* Tableau Desktop, full, eller [testversion.](https://www.tableau.com/products/desktop/download)

* Ett kluster som innehåller Exempeldata för StormEvents. Mer information finns i [Skapa ett Azure Data Explorer-kluster och databas](create-cluster-database-portal.md) och [inta exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualisera data i Tablå 

När du har konfigurerat ODBC kan du hämta exempeldata till Tableau.

1. Välj **Andra databaser (ODBC)** i Tableau Desktop på den vänstra menyn .

    ![Ansluta med ODBC](media/tableau/connect-odbc.png)

1. För **DSN**väljer du den datakälla som du skapade för ODBC och väljer sedan **Logga in**.

    ![ODBC-inloggning](media/tableau/odbc-sign-in.png)

1. För **Databas**väljer du databasen i exempelklustret, till exempel *TestDatabase*. För **Schema**väljer du *dbo*och för **Tabell**väljer du exempeltabellen *StormEvents.*

    ![Välj databas och tabell](media/tableau/select-database-table.png)

1. Tableau visar nu schemat för exempeldata. Välj **Uppdatera nu** om du vill föra in data i Tableau.

    ![Uppdatera data](media/tableau/update-data.png)

    När data importeras visar Tableau rader med data som liknar följande bild.

    ![Resultatuppsättning](media/tableau/result-set.png)

1. Nu kan du skapa visualiseringar i Tableau baserat på de data som du tog in från Azure Data Explorer. Mer information finns i [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)