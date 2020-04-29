---
title: Ansluta till och fråga Synapse SQL med Visual Studio och SSDT
description: Använd Visual Studio för att fråga SQL-pool med Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428568"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Ansluta till Synapse SQL med Visual Studio och SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [SQLCMD](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Använd Visual Studio för att fråga SQL-pool med Azure Synapse Analytics. Den här metoden använder tillägget SQL Server Data Tools (SSDT) i Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Verktyg som stöds för SQL på begäran (för hands version)

Visual Studio stöds för närvarande inte för SQL på begäran (för hands version). Azure Data Studi (för hands version) o är dock ett verktyg som stöds fullt ut. SQL Server Management Studio stöds delvis från version 18,4 och har begränsade funktioner som att ansluta och fråga.

## <a name="prerequisites"></a>Krav
Om du vill använda den här självstudien måste du ha följande komponenter:

* En befintlig SQL-pool. Om du inte har en sådan, se [skapa en SQL-pool](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att slutföra det här kravet.
* SSDT för Visual Studio. Om du har Visual Studio har du förmodligen redan den här komponenten. Installationsinstruktioner och alternativ finns i [Installera Visual Studio och SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Det fullständigt kvalificerade servernamnet. Information om hur du hittar detta finns i [Anslut till SQL-pool](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Anslut till SQL-pool
1. Öppna Visual Studio 2019.
2. Öppna SQL Server Object Explorer. Om du vill göra det väljer du **Visa** > **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Klicka på ikonen **lägg till SQL Server**.
   
    ![Lägg till SQL Server](./media/get-started-visual-studio/add-server.png)
4. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Server namn**: Ange det **Server namn** som du identifierade tidigare.
   * **Autentisering**: Välj **SQL Server autentisering** eller **Active Directory integrerad autentisering**:
   * **Användar namn** och **lösen ord**: Ange ditt användar namn och lösen ord om SQL Server autentisering valdes ovan.
   * Klicka på **Anslut**.
5. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. kör en exempel fråga
Nu när en anslutning har upprättats till databasen skriver du en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/get-started-visual-studio/new-query2.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan. För att göra det, klickar du på den gröna pilen eller använder följande genväg: `CTRL`+`SHIFT`+`E`.
   
    ![Kör frågan](./media/get-started-visual-studio/run-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](get-started-power-bi-professional.md).
Information om hur du konfigurerar din miljö för Azure Active Directory autentisering finns i [autentisera till SQL-pool](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 