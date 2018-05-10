---
title: Använda Azure Blockchain arbetsstationen data i Microsoft Excel
description: Lär dig mer om att läsa in och visa Azure Blockchain arbetsstationen SQL DB-data i Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 70297bd0af6322d0f3ac2c719d1827e4bc5898cd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Visa Azure Blockchain arbetsstationen data med Microsoft Excel

Du kan använda Microsoft Excel för att visa data i Azure Blockchain arbetsstationen SQL-databas. Den här artikeln innehåller steg som du behöver:

* Ansluta till databasen Blockchain arbetsstationen från Microsoft Excel
* Titta på Blockchain arbetsstationen databastabeller och vyer
* Läsa in Blockchain arbetsstationen visa data i Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till databasen Blockchain arbetsstationen

Att ansluta till en databas för Blockchain arbetsstationen:

1. Öppna Microsoft Excel.
2. På den **Data** , Välj **hämta Data**.
3. Välj **från Azure** och välj sedan **från Azure SQL Database**.

   ![Ansluta till Azure SQL-databas](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. I den **SQL Server-databas** dialogrutan:

    * För **Server**, ange namnet på servern Blockchain arbetsstationen.
    * För **databasen (valfritt)**, ange namnet på databasen.

   ![Ange databasservern och databasen](media/blockchain-workbench-data-excel/provide-server-db.png)

5. I den **SQL Server-databas** dialogrutan navigeringsfältet väljer **databasen**. Ange din **användarnamn** och **lösenord**, och välj sedan **Anslut**.

    > [!NOTE]
    > Om du använder autentiseringsuppgifter som skapades under distributionsprocessen Azure Blockchain arbetsstationen i **användarnamn** är `dbadmin`. Den **lösenord** är den du skapade när du distribuerade Blockchain arbetsstationen.
    
   ![Ange autentiseringsuppgifter för att komma åt databasen](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Titta på databastabeller och vyer

Dialogrutan Excel Navigator öppnas när du ansluter till databasen. Du kan använda Navigatören titta på de tabeller och vyer i databasen. Vyerna som är utformade för rapportering och deras namn med prefixet **vw**.

   ![Excel Navigator förhandsgranskning av en vy](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Läs in visa data i en Excel-arbetsbok

I nästa exempel visas hur du kan läsa in data från en vy i en Excel-arbetsbok.

1. I den **Navigator** rullningslist, Välj den **vwContractAction** vyn. Den **vwContractAction** förhandsgranskning visar alla åtgärder som rör ett kontrakt i databasen Blockchain arbetsstationen.
2. Välj **belastningen** att hämta alla data i vyn och placera den i din Excel-arbetsbok.

   ![Data läses in från en vy](media/blockchain-workbench-data-excel/view-data.png)

Nu när du har data som lästs in, kan du använda Excel-funktioner för att skapa egna rapporter med metadata och transaktionen data från Azure Blockchain arbetsstationen-databasen.

## <a name="next-steps"></a>Nästa steg

* [Databasvyer i Azure Blockchain arbetsstationen](blockchain-workbench-database-views.md)