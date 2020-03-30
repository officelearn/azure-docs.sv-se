---
title: Anslut med SSMS
description: Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 12f8240d254b2d393734604928a809a2d9f1e14e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351634"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Ansluta till Azure Synapse Analytics med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS (SSMS)](sql-data-warehouse-query-ssms.md)
> 
> 

Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga ett informationslager i Azure Synapse. 

## <a name="prerequisites"></a>Krav
Du behöver följande för att använda de här självstudierna:

* En befintlig SQL-pool. En skapa en finns i [Skapa en SQL-pool](create-data-warehouse-portal.md).
* SQL Server Management Studio (SSMS) installerat. [Installera SSMS](https://msdn.microsoft.com/library/hh213248.aspx) gratis om du inte redan har det.
* Det fullständigt kvalificerade servernamnet. Information om hur du hittar den här informationen finns i [Anslut till SQL-poolen](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Anslut till din SQL-pool
1. Öppna SSMS.
2. Öppna Objektutforskaren genom att markera **Utforskaren för** > **Filanslutningsobjekt**.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Servernamn**. Ange det **servernamn** som du identifierade tidigare.
   * **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användarnamn** och **lösenord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
   * Klicka på **Anslut**.
4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Kör en exempelfråga
När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan genom `Execute` att klicka eller `F5`använda följande genväg: .
   
    ![Kör frågan](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md ).
Information om hur du konfigurerar din miljö för Azure Active Directory-autentisering finns [i Autentisera till SQL-pool](sql-data-warehouse-authentication.md).
