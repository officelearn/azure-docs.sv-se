---
title: Ansluta till Azure Data Explorer med ODBC
description: I den här artikeln får du lära dig hur du ställer in en Open Database Connectivity (ODBC)-anslutning till Datautforskaren i Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 65795b5b4dea8d2cdeecf5f78f9de751f275dac0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537587"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ansluta till Azure Data Explorer med ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) är en accepterad programmeringsgränssnitt (API) för databasåtkomst. Använd ODBC för att ansluta till Azure Data Explorer från program som inte har en dedikerad anslutning.

I bakgrunden program anropa i gränssnittet ODBC-funktioner som är implementerade i databasspecifika moduler som kallas *drivrutiner*. Azure Data Explorer stöder en delmängd av det SQL Server-protokollet ([MS TDS](/azure/kusto/api/tds/)), så att den kan använda ODBC-drivrutinen för SQL Server.

Med hjälp av följande video, du kan lära dig att skapa en ODBC-anslutning. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Du kan också [konfigurera ODBC-datakällan](#configure-the-odbc-data-source) enligt beskrivning nedan. 

I artikeln får du lära dig hur du använder ODBC-drivrutinen så att du kan ansluta till Datautforskaren i Azure från alla program som har stöd för ODBC. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande:

* [Microsoft ODBC-drivrutin för SQL Server version 17.2.0.1 eller senare](/sql/connect/odbc/download-odbc-driver-for-sql-server) för ditt operativsystem.

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

    ![Active Directory Integrated](media/connect-odbc/active-directory-integrated.png)

1. Välj databas med exempeldata sedan **nästa**.

    ![Ändra standard-databas](media/connect-odbc/change-default-database.png)

1. På nästa skärm, lämnar du alla alternativ som standard Välj **Slutför**.

1. Välj **testa datakällan**.

    ![Testa datakällan](media/connect-odbc/test-data-source.png)

1. Kontrollera att testet har utförts Välj **OK**. Om testet inte fungerade, kontrollerar du de värden som du angav i föregående steg och se till att du har tillräcklig behörighet för att ansluta till klustret.

    ![Testet lyckades](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Azure Data Explorer från Tableau](tableau.md)