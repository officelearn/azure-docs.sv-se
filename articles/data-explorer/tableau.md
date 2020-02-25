---
title: Visualisera Tableau-data med hjälp av Azure Datautforskaren ODBC-anslutning
description: I den här artikeln får du lära dig hur du använder en Open Database Connectivity (ODBC) anslutning till Azure Datautforskaren-anslutningen för att visualisera data med Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562453"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualisera data från Azure Datautforskaren i Tableau

 [Tableau](https://www.tableau.com/) är en Visual Analytics-plattform för Business Intelligence. Om du vill ansluta till Azure Datautforskaren från Tableau och hämta data från ett exempel kluster använder du driv rutinen SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här artikeln:

* [Anslut till azure datautforskaren med ODBC](connect-odbc.md) med SQL Server ODBC-drivrutinen för att ansluta till Azure datautforskaren från Tableau. 

* Tableau Desktop, full eller [utvärderings](https://www.tableau.com/products/desktop/download) version.

* Ett kluster som innehåller exempel data för StormEvents. Mer information finns i [skapa ett azure datautforskaren-kluster och databas](create-cluster-database-portal.md) och mata [in exempel Data i Azure datautforskaren](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualisera data i Tableau 

När du har konfigurerat ODBC kan du hämta exempel data till Tableau.

1. I Tableau Desktop väljer du **andra databaser (ODBC)** på den vänstra menyn.

    ![Ansluta med ODBC](media/tableau/connect-odbc.png)

1. För **DSN**väljer du den data källa som du skapade för ODBC och väljer sedan **Logga**in.

    ![ODBC-inloggning](media/tableau/odbc-sign-in.png)

1. För **databas**väljer du databasen i exempel klustret, till exempel *TestDatabase*. För **schema**väljer du *dbo*, och för **tabell**väljer du exempel tabellen *StormEvents* .

    ![Välj databas och tabell](media/tableau/select-database-table.png)

1. Tableau visar nu schemat för exempel data. Välj **Uppdatera nu** för att hämta data till Tableau.

    ![Uppdatera data](media/tableau/update-data.png)

    När data importeras visar Tableau rader med data som liknar följande bild.

    ![Resultat uppsättning](media/tableau/result-set.png)

1. Nu kan du skapa visualiseringar i Tableau baserat på de data du fick från Azure Datautforskaren. Mer information finns i [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)