---
title: 'SSMS: Ansluta till och fråga efter data i Azure SQL Database | Microsoft Docs'
description: Lär dig hur du ansluter till SQL Database på Azure med hjälp av SQL Server Management Studio (SSMS). Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor mot och redigera data.
keywords: anslut till sql database, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b3342164aec49967e819c316827dca9a65f2674f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098965"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snabbstart: Använda SQL Server Management Studio för att ansluta till och fråga i en Azure SQL-databas

Du kan använda [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) till att hantera all SQL-infrastruktur, från SQL Server till SQL Database för Microsoft Windows. I den här snabbstarten ser du hur du använder SSMS för att ansluta till en Azure SQL-databas. Därefter kan du köra Transact-SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data. 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

* En konfigurerad brandväggsregel på servernivå. Mer information finns i [Skapa brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md).

#### <a name="install-the-latest-ssms"></a>Installera den senaste SSMS

Innan du börjar bör du kontrollera att du har installerat senaste [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Ansluta till databasen

Anslut till din Azure SQL Database-server i SMSS. 

> [!IMPORTANT]
> En logisk Azure SQL Database-server avlyssnar port 1433. Brandväggen måste ha den här porten öppen för att man ska kunna ansluta till en logisk server bakom företagets brandvägg.
>

1. Öppna SSMS. Dialogrutan **Anslut till server** visas.

2. Ange följande information:

   | Inställning      | Föreslaget värde    | Beskrivning | 
   | ------------ | ------------------ | ----------- | 
   | **Servertyp** | Databasmotor | Obligatoriskt värde. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Ungefär så här: **mynewserver20170313.database.windows.net**. |
   | **Autentisering** | SQL Server-autentisering | Den här självstudien använder SQL-autentisering. |
   | **Inloggning** | Serveradministratörskontots användar-ID | Användar-ID från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord** | Serveradministratörskontots lösenord | Lösenord från det serveradministratörskonto som användes när servern skapades. |
   ||||

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)  

3. Välj **Alternativ** i dialogrutan **Anslut till server**. I den nedrullningsbara menyn **Anslut till databas** väljer du **mySampleDatabase**.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Välj **Anslut**. Fönstret Object Explorer öppnas. 

5. Om du vill se databasens objekt expanderar du **Databaser** och **mySampleDatabase**.

   ![visa databasobjekt](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Söka i data

Använd följande [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-kod till att fråga efter de 20 främsta produkterna per kategori.

1. I Object Explorer högerklickar du på **mySampleDatabase**. Välj sedan **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

1. Klistra in den här SQL-frågan i frågefönstret.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. I verktygsfältet väljer du **Kör** för att hämta data från tabellerna `Product` och `ProductCategory`.

    ![fråga för att hämta data från 2 tabeller](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Infoga data

Använd följande [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-kod till att skapa en ny produkt i tabellen `SalesLT.Product`.

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

2. Välj **Kör** för att infoga en ny rad i produkttabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

## <a name="view-the-result"></a>Visa resultatet

1. Ersätt den tidigare frågan med denna.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Use the following [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify the new product you just added.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i produkttabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

## <a name="delete-data"></a>Ta bort data

Använd följande [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kod till att ta bort den nya produkt som du tidigare lade till.

1. Ersätt den tidigare frågan med denna.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i produkttabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

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


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

