---
title: Anslut till Azure SQL Data Warehouse-SSMS | Microsoft Docs
description: Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b96896c0366c6fba2548480b166bdb9cf6ca692c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575341"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Ansluta till SQL Data Warehouse med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att använda de här självstudierna:

* En befintlig SQL Data Warehouse. Se [Skapa ett SQL Data Warehouse][Create a SQL Data Warehouse] för att se hur man skapar det.
* SQL Server Management Studio (SSMS) har installerats. [Installera SSMS][Install SSMS] kostnads fritt om du inte redan har det.
* Det fullständigt kvalificerade servernamnet. [Anslut till SQL Data Warehouse][Connect to SQL Data Warehouse] för att hitta detta.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Anslut till din SQL Data Warehouse
1. Öppna SSMS.
2. Öppna Object Explorer. Det gör du genom att välja **fil** > **anslutning Object Explorer**.
   
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
4. Kör frågan. Det gör du genom att `Execute` Klicka på eller använda följande genväg `F5`:.
   
    ![Kör fråga][5]
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
