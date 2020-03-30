---
title: Ansluta till Azure Data Explorer med ODBC
description: I den här artikeln får du lära dig hur du konfigurerar en ODBC-anslutning (Open Database Connectivity) till Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034021"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ansluta till Azure Data Explorer med ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) är ett allmänt accepterat api (Application Programming Interface) för databasåtkomst. Använd ODBC för att ansluta till Azure Data Explorer från program som inte har en dedikerad anslutningsapp.

Bakom kulisserna, program samtalsfunktioner i ODBC-gränssnittet, som genomförs i databasspecifika moduler som kallas *drivrutiner*. Azure Data Explorer stöder en delmängd av SQL Server-kommunikationsprotokollet ([MS-TDS](/azure/kusto/api/tds/)), så att den kan använda ODBC-drivrutinen för SQL Server.

Med hjälp av följande video kan du lära dig att skapa en ODBC-anslutning. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Du kan också [konfigurera ODBC-datakällan](#configure-the-odbc-data-source) enligt beskrivningen nedan. 

I artikeln får du lära dig hur du använder SQL Server ODBC-drivrutinen, så att du kan ansluta till Azure Data Explorer från alla program som stöder ODBC. 

## <a name="prerequisites"></a>Krav

Du behöver följande:

* [Microsoft ODBC Driver för SQL Server version 17.2.0.1 eller senare](/sql/connect/odbc/download-odbc-driver-for-sql-server) för operativsystemet.

## <a name="configure-the-odbc-data-source"></a>Konfigurera ODBC-datakällan

Följ dessa steg för att konfigurera en ODBC-datakälla med ODBC-drivrutinen för SQL Server.

1. Sök efter *ODBC-datakällor*i Windows och öppna odbc-appen för datakällor.

1. Välj **Lägg till**.

    ![Lägg till datakälla](media/connect-odbc/add-data-source.png)

1. Välj **ODBC Driver 17 för SQL Server** och **slutför**sedan .

    ![Välj drivrutin](media/connect-odbc/select-driver.png)

1. Ange ett namn och en beskrivning för anslutningen och det kluster som du vill ansluta till och välj sedan **Nästa**. Kluster-URL:en ska finnas i formuläret * \<ClusterName\>.\< Region\>.kusto.windows.net*.

    ![Välj server](media/connect-odbc/select-server.png)

1. Välj **Active Directory Integrated** och sedan **nästa**.

    ![Active Directory integrerad](media/connect-odbc/active-directory-integrated.png)

1. Markera databasen med exempeldata sedan **Nästa**.

    ![Ändra standarddatabas](media/connect-odbc/change-default-database.png)

1. Lämna alla alternativ som standard på nästa skärm och **välj**slutför .

1. Välj **Testdatakälla**.

    ![Testa datakälla](media/connect-odbc/test-data-source.png)

1. Kontrollera att testet lyckades och välj sedan **OK**. Om testet inte lyckades kontrollerar du de värden som du angav i tidigare steg och kontrollerar att du har tillräcklig behörighet för att ansluta till klustret.

    ![Testet lyckades](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Azure Data Explorer från Tableau](tableau.md)