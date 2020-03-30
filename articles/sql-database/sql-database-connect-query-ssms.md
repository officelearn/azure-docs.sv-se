---
title: 'SSMS: Anslut och fråga data'
description: Lär dig hur du ansluter till SQL Database på Azure med hjälp av SQL Server Management Studio (SSMS). Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor mot och redigera data.
keywords: anslut till sql database, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299302"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snabbstart: Använda SQL Server Management Studio för att ansluta och fråga en Azure SQL-databas

I den här snabbstarten får du lära dig hur du använder SQL Server Management Studio (SSMS) för att ansluta till en Azure SQL-databas och köra vissa frågor.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten krävs följande:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Exempeldatabasen AdventureWorksLT. Om du behöver en fungerande kopia av AdventureWorksLT-databasen skapar du en genom att slutföra snabbstarten [Skapa en Azure SQL-databas.](sql-database-single-database-get-started.md)
    - Skripten i den här artikeln är skrivna för att använda AdventureWorksLT-databasen. Om du använder en hanterad instans måste du antingen importera AdventureWorks-databasen till en instansdatabas eller ändra skripten i den här artikeln för att kunna använda wide world importers-databasen.

Om du bara vill köra några ad hoc-frågor utan att installera SSMS läser du [Snabbstart: Använd Azure-portalens frågeredigerare för att fråga en SQL-databas](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Hämta den anslutningsinformation du behöver för att ansluta till databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för att slutföra den här snabbstarten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till den **SQL-databas** eller **SQL-hanterade instans** som du vill fråga.

3. Kopiera det fullständigt kvalificerade servernamnet på sidan **Översikt.** Det är bredvid **servernamn** för en enskild databas, eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Det fullständigt kvalificerade namnet ser ut som: *servername.database.windows.net*, förutom att det har ditt faktiska servernamn.

## <a name="connect-to-your-database"></a>Ansluta till databasen

Anslut till din Azure SQL Database-server i SSMS.

> [!IMPORTANT]
> En Azure SQL Database-server avlyssnar port 1433. För att anslutning ska kunna ske till en SQL Database-server inifrån en företagsbrandvägg måste brandväggen ha den här porten öppen.

1. Öppna SSMS.

2. Dialogrutan **Anslut till server** visas. Ange följande information:

   | Inställning      | Föreslaget värde    | Beskrivning |
   | ------------ | ------------------ | ----------- |
   | **Servertyp** | Databasmotor | Obligatoriskt värde. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Något i stil med: **servername.database.windows.net**. |
   | **Autentisering** | SQL Server-autentisering | Den här självstudien använder SQL-autentisering. |
   | **Logga in** | Serveradministratörskontots användar-ID | Användar-ID från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord** | Serveradministratörskontots lösenord | Lösenord från det serveradministratörskonto som användes när servern skapades. |
   ||||

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)  

3. Välj **Alternativ** i dialogrutan **Anslut till server**. I den nedrullningsbara menyn **Anslut till databas** väljer du **mySampleDatabase**. Genom att slutföra snabbstarten i [avsnittet Förutsättningar](#prerequisites) skapas en AdventureWorksLT-databas med namnet mySampleDatabase. Om ditt arbetskopia av AdventureWorks-databasen har ett annat namn än mySampleDatabase väljer du det i stället.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Välj **Anslut**. Fönstret Object Explorer öppnas.

5. Om du vill visa databasens objekt expanderar du **Databaser** och expanderar sedan databasnoden.

   ![mySampleDatabase-objekt](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Söka i data

Kör den här [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-koden för att fråga efter de 20 viktigaste produkterna efter kategori.

1. I Object Explorer högerklickar du på **mySampleDatabase**. Välj sedan **Ny fråga**. Ett nytt frågefönster öppnas som är anslutet till din databas.

2. Klistra in följande SQL-fråga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Välj **Kör** i verktygsfältet och hämta data `Product` `ProductCategory` från tabellerna och tabeller.

    ![fråga för att hämta data från tabellen Product and ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Infoga data

Kör den här [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-koden för att skapa en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den tidigare frågan med denna.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Välj **Kör** för att infoga en ny rad i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

#### <a name="view-the-result"></a>Visa resultatet

1. Ersätt den tidigare frågan med denna.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Välj **Kör**. Följande resultat visas.

   ![resultatet av produkttabellfråga](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Uppdatera data

Kör den här [UPPDATERING](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-koden för att ändra den nya produkten.

1. Ersätt föregående fråga med den här som returnerar den nya posten som skapats tidigare:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

### <a name="delete-data"></a>Ta bort data

Kör den här [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-koden för att ta bort din nya produkt.

1. Ersätt den tidigare frågan med denna.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

## <a name="next-steps"></a>Nästa steg

- Mer information om SSMS finns i [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Information om hur du ansluter och frågar med hjälp av Azure Portal finns i [Ansluta och fråga med Azure Portal SQL-frågeredigeraren](sql-database-connect-query-portal.md).
- Mer information om att ansluta och ställa frågor med Visual Studio Code finns i [Ansluta och fråga med Visual Studio Code](sql-database-connect-query-vscode.md).
- Mer information om att ansluta och ställa frågor med .NET finns i [Ansluta och fråga med .NET](sql-database-connect-query-dotnet.md).
- Mer information om att ansluta och ställa frågor med PHP finns i [Ansluta och fråga med PHP](sql-database-connect-query-php.md).
- Mer information om att ansluta och ställa frågor med Node.js finns i [Ansluta och fråga med Node.js](sql-database-connect-query-nodejs.md).
- Mer information om att ansluta och ställa frågor med Java finns i [Ansluta och fråga med Java](sql-database-connect-query-java.md).
- Mer information om att ansluta och ställa frågor med Python finns i [Ansluta och fråga med Python](sql-database-connect-query-python.md).
- Mer information om att ansluta och ställa frågor med Ruby finns i [Ansluta och fråga med Ruby](sql-database-connect-query-ruby.md).
