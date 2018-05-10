---
title: Använda Azure Blockchain arbetsstationen data i Microsoft Power BI
description: Lär dig mer om att läsa in och visa Azure Blockchain arbetsstationen SQL DB-data i Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 2b909c0a8441010b87c913e5937d25c8127058f1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Med hjälp av Azure Blockchain arbetsstationen data med Microsoft Power BI

Microsoft Power BI ger dig möjlighet att enkelt skapa kraftfulla rapporter från SQL DB-databaser med hjälp av Power BI Desktop och publicera dem till [ https://www.powerbi.com ](http://www.powerbi.com).

Den här artikeln innehåller en steg-för-steg-genomgång av hur du ansluter till Azure Blockchain arbetsstationen SQL-databas från PowerBI desktop, skapa en rapport och distribuera rapporten till powerbi.com.

## <a name="prerequisites"></a>Förutsättningar

* Hämta [PowerBI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>PowerBI att ansluta till data i Azure Blockchain arbetsstationen

1.  Öppna Power BI Desktop.
2.  Välj **hämta Data**.

    ![Hämta data](media/blockchain-workbench-data-powerbi/get-data.png)
3.  Välj **SQL Server** från listan över typer av datakällor.

4.  Ange namnet på servern och databasen i dialogrutan. Ange om du vill importera data eller utföra en **DirectQuery**. Välj **OK**.

    ![Välj SQL Server](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  Ange autentiseringsuppgifterna på databasen för att komma åt Azure Blockchain arbetsstationen. Välj **databasen** och ange dina autentiseringsuppgifter.

    Om du använder autentiseringsuppgifter som skapats av distributionsprocessen Azure Blockchain arbetsstationen användarnamnet är **dbadmin** och lösenordet är det du angav under distributionen.

    ![SQL DB-inställningar](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  En gång ansluten till databasen, den **Navigator** visar de tabeller och vyer som är tillgängliga i databasen. Vyerna som är utformade för rapportering och är alla prefixet **vw**.

    ![Navigator](media/blockchain-workbench-data-powerbi/navigator.png)

7.  Markera de vyer som du vill inkludera. I demonstrationssyfte, vi inkluderar **vwContractAction**, som innehåller information om alla åtgärder som har vidtagits i ett kontrakt.

    ![Välj vyer](media/blockchain-workbench-data-powerbi/select-views.png)

Du kan nu skapa och publicera rapporter som vanligt med Power BI.

## <a name="next-steps"></a>Nästa steg

* [Databasvyer i Azure Blockchain arbetsstationen](blockchain-workbench-database-views.md)