---
title: 'SSMS: Anslut och fråga Synapse SQL'
description: Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423742"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Ansluta till Synapse SQL med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Du kan använda [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics via antingen SQL on-demand (preview) eller SQL pool resources. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Verktyg som stöds för SQL på begäran (förhandsgranskning)

SSMS stöds delvis från och med version 18.5 med begränsade funktioner som anslutning och frågor. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) stöds fullt ut.

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att du har följande förutsättningar:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* För SQL-pool behöver du ett befintligt informationslager. En skapa en finns i [Skapa en SQL-pool](../quickstart-create-sql-pool.md). För SQL on-demand är en redan etablerad i arbetsytan vid skapande. 
* Det fullständigt kvalificerade SQL Server-namnet. Information om detta finns i [Anslut till Synapse SQL](connect-overview.md).

## <a name="connect"></a>Anslut

### <a name="sql-pool"></a>SQL-pool

Så här ansluter du till Synapse SQL med SQL-pool: 

1. Öppna SQL Server Management Studio (SSMS). 
1. Fyll i fälten i dialogrutan **Anslut till server** och välj sedan **Anslut:** 
  
    ![Anslut till server](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Servernamn**: Ange **det servernamn** som tidigare identifierats.
   * **Autentisering**: Välj en autentiseringstyp, till exempel **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användarnamn** och **lösenord**: Ange ditt användarnamn och lösenord om SQL Server-autentisering valdes ovan.

1. Expandera din Azure SQL Server i **Object Explorer**. Du kan visa de databaser som är associerade med servern, till exempel exempel exempel AdventureWorksDW-databasen. Du kan expandera databasen för att se tabellerna:
   
    ![Utforska AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL on-demand (förhandsgranskning)

Så här ansluter du till Synapse SQL med SQL on-demand: 

1. Öppna SQL Server Management Studio (SSMS).
1. Fyll i fälten i dialogrutan **Anslut till server** och välj sedan **Anslut:** 
   
    ![Anslut till server](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Servernamn**: Ange **det servernamn** som tidigare identifierats.
   * **Autentisering**: Välj en autentiseringstyp, till exempel **SQL Server-autentisering** eller **Active Directory-integrerad autentisering:**
   * **Användarnamn** och **lösenord**: Ange ditt användarnamn och lösenord om SQL Server-autentisering valdes ovan.
   * Klicka på **Anslut**.

4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera *demo* för att se innehållet i exempeldatabasen.
   
    ![Utforska AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Kör en exempelfråga

### <a name="sql-pool"></a>SQL-pool

Nu när en databasanslutning har upprättats kan du fråga efter data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopiera den här T-SQL-frågan till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan. Det gör du `Execute` genom att klicka `F5`eller använda följande genväg: .
   
    ![Kör frågan](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL på begäran

Nu när du har upprättat en databasanslutning kan du fråga efter data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/get-started-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Kör frågan. Det gör du `Execute` genom att klicka `F5`eller använda följande genväg: .
   
    ![Kör frågan](./media/get-started-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har vyn usPopulationView 3664512 rader.
   
    ![Frågeresultat](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](get-started-power-bi-professional.md).

Information om hur du konfigurerar din miljö för Azure Active Directory-autentisering finns [i Autentisera till Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

