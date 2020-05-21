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
ms.openlocfilehash: 72006879c7181a8cefe56248215099eeb784d816
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658234"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Ansluta till Synapse-SQL med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [SQLCMD](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Du kan använda [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics genom antingen SQL on-demand (för hands version) eller SQL-pool-resurser. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Verktyg som stöds för SQL på begäran (för hands version)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) stöds fullt ut från version 1.18.0. SSMS stöds delvis från och med version 18,5. du kan bara använda den för att ansluta och fråga.

> [!NOTE]
> Om AAD-inloggningen har öppen anslutning i mer än 1 timme vid frågekörningen, kommer alla frågor som förlitar sig på AAD att Miss förväntas. Detta omfattar att fråga lagring med AAD-vidarekoppling och-instruktioner som interagerar med AAD (t. ex. skapa extern PROVIDER). Detta påverkar alla verktyg som håller anslutningen öppen, som i Frågeredigeraren i SSMS och ADS. Verktyg som öppnar ny anslutning för att köra frågor påverkas inte, till exempel Synapse Studio.
> Du kan åtgärda problemet genom att starta om SSMS eller ansluta och frånkoppla i ADS. .
## <a name="prerequisites"></a>Krav

Innan du börjar måste du kontrol lera att du har följande krav:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* För SQL-pool behöver du ett befintligt informations lager. Information om hur du skapar en SQL-pool finns i [skapa en SQL-pool](../quickstart-create-sql-pool-portal.md). För SQL på begäran är en redan etablerad i din arbets yta när du skapar den. 
* Det fullständigt kvalificerade SQL Server namnet. Mer information finns i [ansluta till SYNAPSE SQL](connect-overview.md).

## <a name="connect"></a>Anslut

### <a name="sql-pool"></a>SQL-pool

Följ dessa steg om du vill ansluta till Synapse SQL med SQL-pool: 

1. Öppna SQL Server Management Studio (SSMS). 
1. Fyll i fälten i dialog rutan **Anslut till Server** och välj sedan **Anslut**: 
  
    ![Anslut till server](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Server namn**: Ange det **Server namn** som du identifierade tidigare.
   * **Autentisering**: Välj en autentiseringstyp, till exempel **SQL Server autentisering** eller **Active Directory integrerad autentisering**.
   * **Användar namn** och **lösen ord**: Ange ditt användar namn och lösen ord om SQL Server autentisering valdes ovan.

1. Expandera Azure-SQL Server i **Object Explorer**. Du kan visa databaserna som är kopplade till servern, till exempel AdventureWorksDW-databasen. Du kan expandera databasen för att se tabellerna:
   
    ![Utforska AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL på begäran (för hands version)

Följ dessa steg om du vill ansluta till Synapse SQL med SQL på begäran: 

1. Öppna SQL Server Management Studio (SSMS).
1. Fyll i fälten i dialog rutan **Anslut till Server** och välj sedan **Anslut**: 
   
    ![Anslut till server](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Server namn**: Ange det **Server namn** som du identifierade tidigare.
   * **Autentisering**: Välj en autentiseringstyp, till exempel **SQL Server autentisering** eller **Active Directory integrerad autentisering**:
   * **Användar namn** och **lösen ord**: Ange ditt användar namn och lösen ord om SQL Server autentisering valdes ovan.
   * Klicka på **Anslut**.

4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera *demonstration* för att se innehållet i exempel databasen.
   
    ![Utforska AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Kör en exempelfråga

### <a name="sql-pool"></a>SQL-pool

Nu när en databas anslutning har upprättats kan du fråga efter data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopiera den här T-SQL-frågan till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan. Det gör du genom att klicka på `Execute` eller använda följande genväg: `F5` .
   
    ![Kör frågan](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL på begäran

Nu när du har upprättat en databas anslutning kan du fråga efter data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/get-started-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Kör frågan. Det gör du genom att klicka på `Execute` eller använda följande genväg: `F5` .
   
    ![Kör frågan](./media/get-started-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har usPopulationView-vyn 3664512 rader.
   
    ![Frågeresultat](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](get-started-power-bi-professional.md).

Information om hur du konfigurerar din miljö för Azure Active Directory autentisering finns i [autentisera till SYNAPSE SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

