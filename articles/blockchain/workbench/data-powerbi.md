---
title: Använd Azure Blockchain Workbench-data i Microsoft Power BI
description: Lär dig hur du läser in och visar SQL DB-data i Azure Blockchain Workbench i Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 2a5e67a8416c57c1a0cb039733a93608a8919be6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214238"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Använda Azure Blockchain Workbench-data med Microsoft Power BI

Microsoft Power BI ger möjlighet att enkelt generera kraftfulla rapporter från SQL DB-databaser med hjälp av Power BI Desktop och sedan publicera dem till [https://www.powerbi.com](https://www.powerbi.com) .

Den här artikeln beskriver steg för steg hur du ansluter till SQL-databasen i Azure Blockchain Workbench från PowerBI Desktop, skapar en rapport och sedan distribuerar rapporten till powerbi.com.

## <a name="prerequisites"></a>Krav

* Ladda ned [Power BI Desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Ansluta Power BI till data i Azure blockchain Workbench

1.  Öppna Power BI Desktop.
2.  Välj **Hämta data**.

    ![Hämta data](./media/data-powerbi/get-data.png)
3.  Välj **SQL Server** i listan över typer av datakällor.

4.  Ange server- och databasnamnet i dialogrutan. Ange om du vill importera data eller köra en **DirectQuery**. Välj **OK**.

    ![Välj SQL Server](./media/data-powerbi/select-sql.png)

5.  Ange databasens autentiseringsuppgifter för åtkomst till Azure Blockchain Workbench. Välj **Databas** och ange dina autentiseringsuppgifter.

    Om du använder autentiseringsuppgifterna som skapades i samband med Azure Blockchain Workbench-distributionen är användarnamnet **dbadmin** och lösenordet är det lösenord som du angav under distributionen.

    ![SQL DB-inställningar](./media/data-powerbi/db-settings.png)

6.  När anslutningen till databasen har upprättats visas de tabeller och vyer som är tillgängliga i databasen i dialogrutan **Navigatör**. Vyerna är utformade för rapportering och har prefixet **vw**.

    ![Navigatör](./media/data-powerbi/navigator.png)

7.  Välj de vyer som du vill ta med. I demonstrations syfte omfattar vi **vwContractAction**, som innehåller information om de åtgärder som har vidtagits i ett avtal.

    ![Välja vyer](./media/data-powerbi/select-views.png)

Nu kan du skapa och publicera rapporter som vanligt med Power BI.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](database-views.md)