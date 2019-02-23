---
title: Ansluta till Azure Data Explorer med ODBC
description: I den här anvisningen att lära dig hur du skapar en ODBC-anslutning till Datautforskaren i Azure och sedan använda anslutningen för att visualisera data med Tableau.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: ad00ad247b047d4acf97eb5d0e96229949181ecf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739910"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ansluta till Azure Data Explorer med ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) är en accepterad programmeringsgränssnitt (API) för databasåtkomst. Använd ODBC för att ansluta till Azure Data Explorer från program som inte har en dedikerad anslutning.

I bakgrunden program anropa i gränssnittet ODBC-funktioner som är implementerade i databasspecifika moduler som kallas *drivrutiner*. Azure Data Explorer stöder en delmängd av det SQL Server-protokollet ([MS TDS](/azure/kusto/api/tds/)); därför kan den använda ODBC-drivrutinen för SQL Server.

I den här artikeln får du lära dig hur du använder ODBC-drivrutinen så att du kan ansluta till Datautforskaren i Azure från alla program som har stöd för ODBC. Du kan också ansluta till Azure Data Explorer från Tableau, och importera data från en exempel-kluster.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här så här:

* [Microsoft ODBC-drivrutin för SQL Server version 17.2.0.1 eller senare](/sql/connect/odbc/download-odbc-driver-for-sql-server) för ditt operativsystem.

* Om du vill följa vårt Tableau exempel behöver du:

  * Tableau Desktop fullständig eller [utvärderingsversion](https://www.tableau.com/products/desktop/download) version.

  * Ett kluster som innehåller exempeldata StormEvents. Mer information finns i [snabbstarten: Skapa ett Azure Data Explorer-kluster och databasen](create-cluster-database-portal.md) och [mata in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Konfigurera ODBC-datakälla

Följ dessa steg om du vill konfigurera en ODBC-datakälla med hjälp av ODBC-drivrutinen för SQL Server.

1. I Windows, söker du efter *ODBC-datakällor*, och öppna skrivbordsappen ODBC-datakällor.

1. Välj **Lägg till**.

    ![Lägg till datakälla](media/connect-odbc/add-data-source.png)

1. Välj **ODBC Driver 17 för SQLServer** sedan **Slutför**.

    ![Välj drivrutin](media/connect-odbc/select-driver.png)

1. Ange ett namn och beskrivning för anslutningen och det kluster som du vill ansluta till, välj sedan **nästa**. Klustret URL: en ska vara i formatet  *\<ClusterName\>.\< Region\>. kusto.windows.net*.

    ![Välj server](media/connect-odbc/select-server.png)

1. Välj **Active Directory-integrerad** sedan **nästa**.

    ![Active Directory-integrerad](media/connect-odbc/active-directory-integrated.png)

1. Välj databas med exempeldata sedan **nästa**.

    ![Ändra standard-databas](media/connect-odbc/change-default-database.png)

1. På nästa skärm, lämnar du alla alternativ som standard Välj **Slutför**.

1. Välj **testa datakällan**.

    ![Testa datakällan](media/connect-odbc/test-data-source.png)

1. Kontrollera att testet har utförts Välj **OK**. Om testet inte fungerade, kontrollerar du de värden som du angav i föregående steg och se till att du har tillräcklig behörighet för att ansluta till klustret.

    ![Testet lyckades](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Visualisera data i Tableau (valfritt)

Nu du har konfigurerat ODBC kan du sätta exempeldata i Tableau.

1. I Tableau Desktop på den vänstra menyn väljer **andra databaser (ODBC)**.

    ![Ansluta till ODBC](media/connect-odbc/connect-odbc.png)

1. För **DSN**, Välj den datakälla som du skapade för ODBC och välj sedan **logga In**.

    ![ODBC-inloggning](media/connect-odbc/odbc-sign-in.png)

1. För **databasen**, välja databasen på exemplet klustret, till exempel *TestDatabase*. För **schemat**väljer *dbo*, och för **tabell**väljer den *StormEvents* exempeltabell.

    ![Välj databas och tabell](media/connect-odbc/select-database-table.png)

1. Tableau visas nu att schemat innehåller exempeldata. Välj **Uppdatera nu** att ta med data i Tableau.

    ![Uppdatera data](media/connect-odbc/update-data.png)

    När data importeras visar Tableau datarader liknar följande bild.

    ![Resultatuppsättningen](media/connect-odbc/result-set.png)

1. Nu kan du skapa visualiseringar i Tableau baserat på data som du har med Datautforskaren i Azure. Mer information finns i [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Nästa steg

[Skriva frågor för Azure Data Explorer](write-queries.md)

[Självstudier: Visualisera data från Azure Data Explorer i Power BI](visualize-power-bi.md)