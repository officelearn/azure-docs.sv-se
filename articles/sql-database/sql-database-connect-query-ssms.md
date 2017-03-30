---
title: "SSMS: Ansluta och läsa data i Azure SQL Database | Microsoft Docs"
description: "Lär dig hur du ansluter till SQL Database på Azure med hjälp av SQL Server Management Studio (SSMS). Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor och redigera data."
metacanonical: 
keywords: anslut till sql database, sql server management studio
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: ba56eaa154116edbe1dd8962049535cfa57551ac
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: Använd SQL Server Management Studio för att ansluta och skicka frågor till data

Använd [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) för att skapa och hantera SQL Server-resurser från användargränssnittet eller i skript. Den här guiden beskriver hur man använder SSMS för att ansluta till en Azure SQL Database och sedan köra uttryck för fråga, infoga, uppdatera och ta bort.

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)

Innan du börjar bör du kontrollera att du har installerat den senaste versionen av [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta det fullständigt kvalificerade servernamnet för Azure SQL Database-servern i Azure Portal. Du kan använda det fullständigt kvalificerade servernamnet för att ansluta till servern med hjälp av SQL Server Management Studio.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Ansluta till servern

Använd SQL Server Management Studio för att upprätta en anslutning till Azure SQL Database-servern.

1. Skriv **SSMS** i sökrutan i Windows och klicka sedan på **Enter** för att öppna SSMS.

2. I dialogrutan **Anslut till server** anger du följande information:
   - **Servertyp**: Ange databasmotor
   - **Servernamn**: Ange det fullständigt kvalificerade servernamnet som **mynewserver20170313.database.windows.net**
   - **Autentisering**: Ange SQL Server-autentisering
   - **Logga in**: Ange serveradministratörskontot
   - **Lösenord**: Ange lösenordet för serveradministratörskontot
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. I Object Explorer expanderar du **Databaser** och sedan **mySampleDatabase** för att visa objekten i exempeldatabasen.

## <a name="query-data"></a>Frågedata

Använd [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-uttrycket för att skicka frågor till data i Azure SQL Database.

1. I Object Explorer högerklickar du på **mySampleDatabase** och klickar sedan på **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.
2. I frågefönstret anger du följande fråga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Klicka på **Execute** (Kör) för att hämta data från Product- och ProductCategory-tabeller.

    <img src="./media/sql-database-connect-query-ssms/query.png" alt="query" style="width: 780px;" />

## <a name="insert-data"></a>Infoga data

Använd [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-uttrycket för att infoga data i Azure SQL Database.

1. Klicka på **Ny fråga** i verktygsfältet. Ett tomt frågefönster öppnas som är anslutet till databasen.
2. I frågefönstret anger du följande fråga i frågefönstret:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

3. I verktygsfältet klickar du på **Execute** (Kör) för att infoga en ny rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Uppdatera data

Använd [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-uttrycket för att uppdatera data i Azure SQL Database.

1. Klicka på **Ny fråga** i verktygsfältet. Ett tomt frågefönster öppnas som är anslutet till databasen.
2. I frågefönstret anger du följande fråga i frågefönstret:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. I verktygsfältet trycker du på **Execute** (Kör) för att uppdatera angiven rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Ta bort data

Använd [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-uttrycket för att ta bort data i Azure SQL Database.

1. Klicka på **Ny fråga** i verktygsfältet. Ett tomt frågefönster öppnas som är anslutet till databasen.
2. I frågefönstret anger du följande fråga i frågefönstret:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. I verktygsfältet trycker du på **Kör** för att ta bort angiven rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Nästa steg

- Mer information om SSMS finns i [Använda SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Information om att skicka frågor till och redigera data med hjälp av använda Visual Studio Code finns [Visual Studio Code](https://code.visualstudio.com/docs)

