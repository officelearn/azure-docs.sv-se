---
title: Ansluta till Azure Datautforskaren med ODBC
description: I den här artikeln får du lära dig hur du konfigurerar en Open Database Connectivity (ODBC) anslutning till Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034021"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ansluta till Azure Datautforskaren med ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) är ett allmänt accepterat Application Programming Interface (API) för databas åtkomst. Använd ODBC för att ansluta till Azure Datautforskaren från program som inte har en särskild koppling.

Programmen anropar funktioner i ODBC-gränssnittet i bakgrunden, som implementeras i de databasbaserade moduler som kallas *driv rutiner*. Azure Datautforskaren stöder en delmängd av SQL Server Communication Protocol ([MS-TDS](/azure/kusto/api/tds/)), så det kan använda ODBC-drivrutinen för SQL Server.

Med hjälp av följande video kan du lära dig hur du skapar en ODBC-anslutning. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Du kan också [konfigurera ODBC-datakällan](#configure-the-odbc-data-source) enligt beskrivningen nedan. 

I artikeln får du lära dig hur du använder SQL Server ODBC-drivrutinen så att du kan ansluta till Azure Datautforskaren från alla program som stöder ODBC. 

## <a name="prerequisites"></a>Krav

Du behöver följande:

* [Microsoft ODBC driver för SQL Server version 17.2.0.1 eller senare](/sql/connect/odbc/download-odbc-driver-for-sql-server) för ditt operativ system.

## <a name="configure-the-odbc-data-source"></a>Konfigurera ODBC-datakällan

Följ dessa steg om du vill konfigurera en ODBC-datakälla med ODBC-drivrutinen för SQL Server.

1. I Windows kan du söka efter *ODBC-datakällor*och öppna Desktop-appen ODBC-datakällor.

1. Välj **Lägg till**.

    ![Lägg till datakälla](media/connect-odbc/add-data-source.png)

1. Välj **ODBC-drivrutin 17 för SQL Server** sedan **Slutför**.

    ![Välj driv rutin](media/connect-odbc/select-driver.png)

1. Ange ett namn och en beskrivning för anslutningen och klustret som du vill ansluta till och välj sedan **Nästa**. Kluster-URL: en ska vara i formatet *\<kluster namn\>.\<Region\>. kusto.Windows.net*.

    ![Välj server](media/connect-odbc/select-server.png)

1. Välj **Active Directory integrerad** och sedan på **Nästa**.

    ![Active Directory integrerad](media/connect-odbc/active-directory-integrated.png)

1. Välj databasen med exempel data och sedan **Nästa**.

    ![Ändra standard databas](media/connect-odbc/change-default-database.png)

1. På nästa skärm lämnar du alla alternativ som standard och väljer sedan **Slutför**.

1. Välj **test data källa**.

    ![Test data Källa](media/connect-odbc/test-data-source.png)

1. Kontrol lera att testet lyckades och välj **OK**. Om testet inte lyckades kontrollerar du de värden som du angav i föregående steg och kontrollerar att du har tillräcklig behörighet för att ansluta till klustret.

    ![Testet lyckades](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Azure Datautforskaren från Tableau](tableau.md)