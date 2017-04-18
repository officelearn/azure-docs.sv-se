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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: c173f1b6937739f662eb41aa1886e66cb06ed729
ms.lasthandoff: 04/12/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: Använd SQL Server Management Studio för att ansluta och skicka frågor till data

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) är ett hanteringsverktyg som används för att skapa och hantera SQL Server-resurser från användargränssnittet eller i skript. Den här snabbstarten visar hur du använder SSMS för att ansluta till en Azure SQL-databas och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen. 

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)

Innan du börjar bör du kontrollera att du har installerat den senaste versionen av [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta det fullständigt kvalificerade servernamnet för Azure SQL Database-servern i Azure Portal. Du kan använda det fullständigt kvalificerade servernamnet för att ansluta till servern med hjälp av SQL Server Management Studio.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**.

   ![anslutningsinformation](./media/sql-database-connect-query-ssms/connection-information.png) 


## <a name="connect-to-the-server-and-your-new-database"></a>Ansluta till servern och den nya databasen

Använd SQL Server Management Studio för att upprätta en anslutning till Azure SQL Database-servern. 

> [!IMPORTANT]
> En logisk Azure SQL Database-server avlyssnar port 1433. Om du försöker ansluta till en logisk Azure SQL Database-server inifrån en företagsbrandvägg, måste den porten vara öppen i företagsbrandväggen för att du ska kunna ansluta.
>

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:
   - **Servertyp**: Ange databasmotor
   - **Servernamn**: Ange det fullständigt kvalificerade servernamnet som **mynewserver20170313.database.windows.net**
   - **Autentisering**: Ange SQL Server-autentisering
   - **Logga in**: Ange serveradministratörskontot
   - **Lösenord**: Ange lösenordet för serveradministratörskontot

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)  

3. Klicka på **Alternativ**. I avsnittet **Anslut till databas** anger du **mySampleDatabase** för att ansluta till den här databasen som du skapade tidigare.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

   ![Ansluten till server](./media/sql-database-connect-query-ssms/connected.png)  

4. I Object Explorer expanderar du **Databaser** och sedan **mySampleDatabase** för att visa objekten i exempeldatabasen.

## <a name="query-data"></a>Frågedata

Använd [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-uttrycket för att skicka frågor till data i Azure SQL Database.

1. I Object Explorer högerklickar du på **mySampleDatabase** och klickar sedan på **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.
2. Skriv följande fråga i frågefönstret:

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

1. I frågefönstret ersätter du den föregående frågan med följande fråga:

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

2. I verktygsfältet klickar du på **Execute** (Kör) för att infoga en ny rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Uppdatera data

Använd [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-uttrycket för att uppdatera data i Azure SQL Database.

1. I frågefönstret ersätter du den föregående frågan med följande fråga:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. I verktygsfältet trycker du på **Execute** (Kör) för att uppdatera angiven rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Ta bort data

Använd [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-uttrycket för att ta bort data i Azure SQL Database.

1. I frågefönstret ersätter du den föregående frågan med följande fråga:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. I verktygsfältet trycker du på **Kör** för att ta bort angiven rad i Product-tabellen.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Nästa steg

- Mer information om SSMS finns i [Använda SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Mer information om att ansluta och ställa frågor med Visual Studio Code finns i [Ansluta och fråga med Visual Studio Code](sql-database-connect-query-vscode.md).
- Mer information om att ansluta och ställa frågor med .NET finns i [Ansluta och fråga med .NET](sql-database-connect-query-dotnet.md).
- Mer information om att ansluta och ställa frågor med PHP finns i [Ansluta och fråga med PHP](sql-database-connect-query-php.md).
- Mer information om att ansluta och ställa frågor med Node.js finns i [Ansluta och fråga med Node.js](sql-database-connect-query-nodejs.md).
- Mer information om att ansluta och ställa frågor med Java finns i [Ansluta och fråga med Java](sql-database-connect-query-java.md).
- Mer information om att ansluta och ställa frågor med Python finns i [Ansluta och fråga med Python](sql-database-connect-query-python.md).
- Mer information om att ansluta och ställa frågor med Ruby finns i [Ansluta och fråga med Ruby](sql-database-connect-query-ruby.md).

