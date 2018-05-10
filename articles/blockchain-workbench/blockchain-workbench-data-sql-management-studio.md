---
title: Använda Azure Blockchain arbetsstationen Data med SQL Server Management Studio
description: Lär dig hur du ansluter till Azure Blockchain arbetsstationen SQL-databas från SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1c9ee1e94ba1195db027c3edfab2e2a96f181ca3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Med hjälp av Azure Blockchain arbetsstationen data med SQL Server Management Studio

Microsoft SQL Server Management Studio ger möjlighet att snabbt skriva och testa frågor mot Azure Blockhain arbetsstationen SQL-databas. Det här avsnittet innehåller en steg-för-steg-genomgång av hur du ansluter till Azure Blockchain arbetsstationen SQL-databas från SQL Server Management Studio.

## <a name="prerequisites"></a>Förutsättningar

* Hämta [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio att ansluta till data i Azure Blockchain arbetsstationen

1. Öppna SQL Server Management Studio och välj **Anslut**.
2. Välj **Database Engine**.

    ![Databasmotor](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. I den **Anslut till Server** dialogrutan, ange servernamnet och autentiseringsuppgifterna för databasen.

    Om du använder autentiseringsuppgifter som skapats av distributionsprocessen Azure Blockchain arbetsstationen användarnamnet kommer att **dbadmin** och lösenordet blir det du angav under distributionen.

    ![Ange autentiseringsuppgifterna för SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio visar listan över databaser, databasvyer och lagrade procedurer i databasen Azure Blockchain arbetsstationen.

    ![Databaslistan](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Du kan automatiskt skapa en select-instruktion med följande steg om du vill visa data som är associerade med någon av vyerna som databas.
6. Högerklicka på databasen vyer i Object Explorer.
7. Välj **skript som**.
8. Välj **väljer att**.
9. Välj **nya Query Editor-fönstret**.
10. En ny fråga kan skapas genom att välja **ny fråga**.

## <a name="next-steps"></a>Nästa steg

* [Databasvyer i Azure Blockchain arbetsstationen](blockchain-workbench-database-views.md)