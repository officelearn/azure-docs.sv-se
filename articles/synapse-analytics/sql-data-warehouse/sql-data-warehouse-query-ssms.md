---
title: Anslut med SSMS
description: Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga en dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c2fab8f02f31897a2e3d38094b9bd365f8e921e9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325330"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Ansluta till en dedikerad SQL-pool i Azure Synapse Analytics med SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga ett informations lager i Azure Synapse.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att använda de här självstudierna:

* En befintlig dedikerad SQL-pool. Om du vill skapa en, se [skapa en dedikerad SQL-pool](create-data-warehouse-portal.md).
* SQL Server Management Studio (SSMS) har installerats. [Hämta SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kostnads fritt om du inte redan har det.
* Det fullständigt kvalificerade servernamnet. Information om hur du hittar den här informationen finns i [Anslut till en dedikerad SQL-pool](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool"></a>1. Anslut till din dedikerade SQL-pool

1. Öppna SSMS.
2. Öppna Object Explorer genom att välja **fil**  >  **Connect Object Explorer**.

    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Fyll i fälten i fönstret anslut till server.

   ![Anslut till server](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Server namn**. Ange det **servernamn** som du identifierade tidigare.
   * **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användar namn** och **lösen ord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
   * Klicka på **Anslut**.
4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.

   ![Utforska AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. kör en exempel fråga

När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **Ny fråga**. Ett nytt frågefönster öppnas.

   ![Ny fråga](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Kör frågan genom att klicka på `Execute` eller Använd följande genväg: `F5` .

   ![Kör frågan](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.

   ![Frågeresultat](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Nästa steg

Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Information om hur du konfigurerar din miljö för Azure Active Directory autentisering finns i [autentisera till dedikerad SQL-pool](sql-data-warehouse-authentication.md).
