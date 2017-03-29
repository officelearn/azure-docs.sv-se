---
title: "VS Code: Ansluta och läsa data i Azure SQL Database | Microsoft Docs"
description: "Lär dig hur du ansluter till SQL Database på Azure med hjälp av Visual Studio Code. Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor och redigera data."
metacanonical: 
keywords: ansluta till sql database
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 86471fe29bbc9076624d96b83f7001d8755363bc
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Använd Visual Studio Code för att ansluta och skicka frågor till data

[Visual Studio Code](https://code.visualstudio.com/docs) är en grafisk kodredigerare för Linux, macOS och Windows som stöder tillägg. Använd Visual Studio Code med [mssql-tillägget](https://aka.ms/mssql-marketplace) för att ansluta och skicka frågor till en Azure SQL Database. Den här guiden beskriver hur man använder Visual Studio Code för att ansluta till en Azure SQL Database och sedan köra uttryck för fråga, infoga, uppdatera och ta bort.

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)

Innan du börjar bör du kontrollera att du har installerat den senaste versionen av [Visual Studio Code](https://code.visualstudio.com/Download) och har läst in [mssql-tillägget](https://aka.ms/mssql-marketplace). Installationsanvisningar för mssql-tillägget finns i [Installera VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta det fullständigt kvalificerade servernamnet för Azure SQL Database-servern i Azure Portal. Du kan använda det fullständigt kvalificerade servernamnet för att ansluta till servern med hjälp av Visual Studio Code.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet** som ska användas längre fram i denna snabbstart.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>Ange språkläge till SQL

Ställ in språkläget på **SQL** i Visual Studio Code för att aktivera mssql-kommandon och T-SQL IntelliSense.

1. Öppna ett Visual Studio Code-fönster. 

2. Tryck på **CTRL+K,M**, skriv in **SQL** och tryck på **RETUR** för att ställa in språkläget på SQL. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Ansluta till servern

Använd Visual Studio Code för att upprätta en anslutning till Azure SQL Database-servern.

1. I VS Code trycker du på **CTRL+SHIFT+P** (eller **F1**) för att öppna kommandopaletten.

2. Skriv in **sqlcon** och tryck på **RETUR**.

3. Klicka på **Ja** för att ställa in språket på **SQL**.

4. Tryck på **RETUR** för att välja **Create Connection Profile** (Skapa anslutningsprofil). Detta skapar en anslutningsprofil för SQL Server-instansen.

5. Följ anvisningarna för att ange anslutningsegenskaper för den nya anslutningsprofilen. När du har angett ett värde trycker du på **RETUR** för att fortsätta. 

   I följande tabell beskrivs egenskaperna för anslutningsprofilen.

   | Inställning | Beskrivning |
   |-----|-----|
   | **Servernamn** | Ange det fullständigt kvalificerade servernamnet som **mynewserver20170313.database.windows.net** |
   | **Databasnamn** | Ange databasnamnet, t.ex. **mySampleDatabase** |
   | **Autentisering** | Välj SQL-inloggning |
   | **Användarnamn** | Ange serveradministratörskontot |
   | **Lösenord (SQL-inloggning)** | Ange lösenordet för serveradministratörskontot | 
   | **Spara lösenordet?** | Välj **Ja** eller **Nej** |
   | **[Valfritt] Ange ett namn för den här profilen** | Ange ett anslutningsprofilnamn, t.ex. **mySampleDatabase**. 

6. Tryck på tangenten **ESC** för att stänga meddelandet som informerar om att profilen har skapats och anslutits.

7. Kontrollera anslutningen i statusfältet.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Frågedata

Använd [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-uttrycket för att skicka frågor till data i Azure SQL Database.

1. I fönstret **Editor** anger du följande fråga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Tryck på **CTRL+SKIFT+E** att hämta data från Product- och ProductCategory-tabeller.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Infoga data

Använd [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-uttrycket för att infoga data i Azure SQL Database.

1. I fönstret **Editor** tar du bort föregående fråga och skriver in följande fråga:

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

3. Tryck på **CTRL+SKIFT+E** för att infoga en ny rad i Product-tabellen.

## <a name="update-data"></a>Uppdatera data

Använd [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-uttrycket för att uppdatera data i Azure SQL Database.

1.  I fönstret **Editor** tar du bort föregående fråga och skriver in följande fråga:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Tryck på **CTRL+SKIFT+E** för att uppdatera angiven rad i Product-tabellen.

## <a name="delete-data"></a>Ta bort data

Använd [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-uttrycket för att ta bort data i Azure SQL Database.

1. I fönstret **Editor** tar du bort föregående fråga och skriver in följande fråga:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Tryck på **CTRL+SHIFT+E** för att ta bort angiven rad i Product-tabellen.

## <a name="next-steps"></a>Nästa steg

- Information om Visual Studio Code finns i [Visual Studio Code](https://code.visualstudio.com/docs)
- Information om att skicka frågor till och redigera data med hjälp av SQL Server Management Studio finns i [SSMS](https://msdn.microsoft.com/library/ms174173.aspx).

