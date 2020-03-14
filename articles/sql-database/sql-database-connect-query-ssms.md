---
title: 'SSMS: Anslut och fråga efter data'
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
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299302"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snabb start: använda SQL Server Management Studio för att ansluta och skicka frågor till en Azure SQL-databas

I den här snabb starten får du lära dig hur du använder SQL Server Management Studio (SSMS) för att ansluta till en Azure SQL-databas och köra vissa frågor.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten krävs följande objekt:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Exempel databasen AdventureWorksLT. Om du behöver en fungerande kopia av AdventureWorksLT-databasen skapar du en genom att slutföra snabb starten för att [skapa en Azure SQL Database](sql-database-single-database-get-started.md) .
    - Skripten i den här artikeln är skrivna för att använda AdventureWorksLT-databasen. Om du använder en hanterad instans måste du antingen importera AdventureWorks-databasen till en instans databas eller ändra skripten i den här artikeln för att använda Wide World Importers-databasen.

Om du bara vill köra vissa ad hoc-frågor utan att installera SSMS, se [snabb start: använd Azure portalens Frågeredigeraren för att fråga en SQL-databas](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Hämta anslutnings informationen du behöver för att ansluta till databasen. Du behöver det fullständigt kvalificerade Server namnet eller värd namnet, databas namnet och inloggnings informationen för att slutföra den här snabb starten.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Gå till den **SQL-databas** eller **SQL-hanterade instans** som du vill fråga.

3. På sidan **Översikt** kopierar du det fullständigt kvalificerade Server namnet. Den finns bredvid **Server namnet** för en enskild databas, eller det fullständigt kvalificerade Server namnet bredvid **värd** för en hanterad instans. Det fullständigt kvalificerade namnet ser ut så här: *servername.Database.Windows.net*, förutom att det har det faktiska Server namnet.

## <a name="connect-to-your-database"></a>Ansluta till databasen

I SSMS ansluter du till din Azure SQL Database-Server.

> [!IMPORTANT]
> En Azure SQL Database-server avlyssnar port 1433. För att anslutning ska kunna ske till en SQL Database-server inifrån en företagsbrandvägg måste brandväggen ha den här porten öppen.

1. Öppna SSMS.

2. Dialogrutan **Anslut till server** visas. Ange följande information:

   | Inställning      | Föreslaget värde    | Beskrivning |
   | ------------ | ------------------ | ----------- |
   | **Servertyp** | Databasmotor | Obligatoriskt värde. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Något som liknar: **servername.Database.Windows.net**. |
   | **Autentisering** | SQL Server-autentisering | Den här självstudien använder SQL-autentisering. |
   | **Inloggning** | Serveradministratörskontots användar-ID | Användar-ID från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord** | Serveradministratörskontots lösenord | Lösenord från det serveradministratörskonto som användes när servern skapades. |
   ||||

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)  

3. Välj **Alternativ** i dialogrutan **Anslut till server**. I den nedrullningsbara menyn **Anslut till databas** väljer du **mySampleDatabase**. När du har slutfört snabb starten i [avsnittet krav](#prerequisites) skapas en AdventureWorksLT-databas med namnet mySampleDatabase. Om din arbets kopia av AdventureWorks-databasen har ett annat namn än mySampleDatabase, väljer du den i stället.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Välj **Anslut**. Fönstret Object Explorer öppnas.

5. Om du vill visa databasens objekt expanderar du **databaser** och expanderar sedan noden databas.

   ![mySampleDatabase-objekt](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Söka i data

Kör den här [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-koden för att fråga efter de 20 viktigaste produkterna efter kategori.

1. I Object Explorer högerklickar du på **mySampleDatabase**. Välj sedan **Ny fråga**. Ett nytt frågefönster öppnas som är anslutet till din databas.

2. Klistra in följande SQL-fr åga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. I verktygsfältet väljer du **Kör** för att köra frågan och hämta data från `Product` och `ProductCategory` tabeller.

    ![fråga för att hämta data från tabell produkter och ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Infoga data

Kör den här [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-koden för att skapa en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den föregående frågan med denna.

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

2. Välj **Kör** för att infoga en ny rad i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)** .

#### <a name="view-the-result"></a>Visa resultatet

1. Ersätt den föregående frågan med denna.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Välj **Kör**. Följande resultat visas.

   ![resultat av produkt tabell fråga](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Uppdatera data

Kör den här [uppdateringen](https://msdn.microsoft.com/library/ms177523.aspx) av Transact-SQL-koden för att ändra din nya produkt.

1. Ersätt föregående fråga med denna som returnerar den nya posten som skapades tidigare:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)** .

### <a name="delete-data"></a>Ta bort data

Kör den här [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-koden för att ta bort din nya produkt.

1. Ersätt den föregående frågan med denna.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)** .

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
