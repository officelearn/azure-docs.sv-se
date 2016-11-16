---
title: "Anslut till SQL Database med en C#-fråga | Microsoft Docs"
description: "Skriv ett program i C# för att fråga och ansluta till SQL Database. Information om IP-adresser, anslutningssträngar, säker inloggning och kostnadsfri Visual Studio."
services: sql-database
keywords: "c# databasfråga, c#, ansluta till databasen, SQL C#"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: stevestein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 333babec567a4700ca0882c883e4460442d844e1


---
# <a name="connect-to-a-sql-database-with-visual-studio"></a>Anslut till SQL Database med Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Lär dig hur man ansluter till en Azure SQL-databas i Visual Studio. 

## <a name="prerequisites"></a>Krav
För att ansluta till SQL-databas med Visual Studio behöver du följande: 

* En SQL-databas att ansluta till. Den här artikeln använder exempeldatabasen **AdventureWorks**. Läs i [Skapa demodatabasen](sql-database-get-started.md) för att få tillgång till AdventureWorks-exempeldatabasen.
* Visual Studio 2013 uppdatering 4 (eller senare). Microsoft erbjuder nu Visual Studio Community helt *kostnadsfritt*.
  
  * [Visual Studio Community, hämta](http://www.visualstudio.com/products/visual-studio-community-vs)
  * [Fler alternativ för kostnadsfri Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)

## <a name="open-visual-studio-from-the-azure-portal"></a>Öppna Visual Studio från Azure Portal
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Fler tjänster** > **SQL-databaser**
3. Öppna **AdventureWorks**-databasbladet genom att klicka på *AdventureWorks*-databasen.
4. Klicka på knappen **Verktyg** längst upp på databasbladet:
   
    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)
5. Klicka på **Öppna i Visual Studio** (om du behöver Visual Studio kan du klicka på hämtningslänken):
   
    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)
6. Visual Studio öppnas med fönstret **Anslut till server** redan inställt för att ansluta till den server och databas som du valde i portalen.  (Klicka på **Alternativ** att kontrollera att anslutningen har ställts in på rätt databas.) Ange ditt serveradministratörslösenord och klicka på **Anslut**.

    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


1. Om du inte har skapat en brandväggsregel för datorns IP-adress visas meddelandet *Det går inte att ansluta*. Om du vill skapa en brandväggsregel kan du läsa [Konfigurera en brandväggsregel på servernivå för Azure SQL Database](sql-database-configure-firewall-settings.md).
2. När du har anslutit öppnas fönstret **SQL Server Object Explorer** med en anslutning till databasen.
   
    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)

## <a name="run-a-sample-query"></a>Kör en exempelfråga
Nu när vi har anslutit till databasen visar följande steg hur du kör en enkel fråga:

1. Högerklicka på databasen och välj **Ny fråga**.
   
    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)
2. Kopiera och klistra in följande kod i frågefönstret.
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
3. Klicka på knappen **Kör** för att köra frågan:
   
    ![Lyckades. Anslut till SQL Database-server: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Nästa steg
* Öppna SQL-databaser i Visual Studio med SQL Server Data Tools. Mer information finns i [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
* Om du vill ansluta till en SQL-databas med hjälp av kod kan du läsa [Ansluta till SQL Database med hjälp av .NET (C#)](sql-database-develop-dotnet-simple.md).




<!--HONumber=Nov16_HO2-->


