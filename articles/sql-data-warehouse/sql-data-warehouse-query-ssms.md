---
title: Anslut till Azure SQL Data Warehouse - SSMS | Microsoft Docs
description: Använda SQL Server Management Studio (SSMS) för att ansluta till och fråga Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f4b960115da3d30d355768498ae95270acaef49b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Anslut till SQL Data Warehouse med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Använda SQL Server Management Studio (SSMS) för att ansluta till och fråga Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att använda de här självstudierna:

* Ett befintligt SQL Data Warehouse. Se [Skapa ett SQL Data Warehouse][Create a SQL Data Warehouse] för att se hur man skapar det.
* SQL Server Management Studio (SSMS) installerat. [Installera SSMS] [ Install SSMS] kostnadsfritt om det inte redan har.
* Det fullständigt kvalificerade servernamnet. [Anslut till SQL Data Warehouse][Connect to SQL Data Warehouse] för att hitta detta.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Anslut till din SQL Data Warehouse
1. Öppna SSMS.
2. Öppna Object Explorer. Om du vill göra det, Välj **filen** > **ansluta Object Explorer**.
   
    ![SQL Server Object Explorer][1]
3. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server][2]
   
   * **Servernamn**. Ange det **servernamn** som du identifierade tidigare.
   * **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användarnamn** och **lösenord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
   * Klicka på **anslut**.
4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Kör en exempelfråga
När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga][4]
3. Kopiera den här TSQL-frågan till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan. Gör detta genom att klicka på `Execute` eller använder följande genväg: `F5`.
   
    ![Kör frågan][5]
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat][6]

## <a name="next-steps"></a>Nästa steg
Nu när du anslutit och frågat, kan du [visualisera dina data med PowerBI][visualizing the data with PowerBI].

För att konfigurera din miljö för Azure Active Directory-autentisering, se [Autentisera till SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
