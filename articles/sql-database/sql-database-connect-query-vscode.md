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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 45405c7bb9993d1fd529b25b599c3cd7f459843c
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Använd Visual Studio Code för att ansluta och skicka frågor till data

[Visual Studio Code](https://code.visualstudio.com/docs) är en grafisk kodredigerare för Linux, macOS och Windows som stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för frågor till Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse. Den här snabbstarten visar hur du använder Visual Studio Code för att ansluta till en Azure SQL-databas och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)

Innan du börjar bör du kontrollera att du har installerat den senaste versionen av [Visual Studio Code](https://code.visualstudio.com/Download) och har läst in [mssql-tillägget](https://aka.ms/mssql-marketplace). Installationsanvisningar finns i [Installera VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) för mssql-tillägget och [mssql för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). 

## <a name="configure-vs-code"></a>Konfigurera VS-kod 

### <a name="mac-os"></a>**Mac OS**
För macOS måste du installera OpenSSL, som är ett förhandskrav för den DotNet Core som används i mssql-tillägget. Ange följande kommandon för att installera **brew** och **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Ingen särskild konfiguration behövs.

### <a name="windows"></a>**Windows**

Ingen särskild konfiguration behövs.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**.

   ![anslutningsinformation](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Om du har glömt inloggningsinformationen för Azure SQL Database-server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov. 

## <a name="set-language-mode-to-sql"></a>Ange språkläge till SQL

Ställ in språkläget på **SQL** i Visual Studio Code för att aktivera mssql-kommandon och T-SQL IntelliSense.

1. Öppna ett Visual Studio Code-fönster. 

2. Klicka på **Oformaterad text** nere till höger i statusfältet.
3. Listrutan **Select language mode** (Välj språkläge) öppnas. Skriv **SQL**, tryck på **ENTER** och ange språkläge för SQL. 

   ![Språkläge för SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Ansluta till databasen på den logiska SQL Database-servern

Använd Visual Studio Code för att upprätta en anslutning till Azure SQL Database-servern.

> [!IMPORTANT]
> Kontrollera att du har din server, databas och inloggningsinformation redo innan du fortsätter. Om du ändrar fokus från Visual Studio-koden när du har börjat ange information om anslutningsprofilen måste du börja om med att skapa anslutningsprofilen.
>

1. I VS Code trycker du på **CTRL+SHIFT+P** (eller **F1**) för att öppna kommandopaletten.

2. Skriv in **sqlcon** och tryck på **RETUR**.

3. Tryck på **RETUR** för att välja **Create Connection Profile** (Skapa anslutningsprofil). Detta skapar en anslutningsprofil för SQL Server-instansen.

4. Följ anvisningarna för att ange anslutningsegenskaper för den nya anslutningsprofilen. När du har angett ett värde trycker du på **RETUR** för att fortsätta. 

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

5. Tryck på tangenten **ESC** för att stänga meddelandet som informerar om att profilen har skapats och anslutits.

6. Kontrollera anslutningen i statusfältet.

   ![Anslutningsstatus](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Frågedata

Använd följande kod för att söka efter de 20 främsta produkterna med Transact-SQL-instruktionen [SELECT](https://msdn.microsoft.com/library/ms189499.aspx).

1. I fönstret **Editor** anger du följande fråga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Tryck på **CTRL+SKIFT+E** att hämta data från Product- och ProductCategory-tabeller.

    ![Fråga](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Infoga data

Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med Transact-SQL-instruktionen [INSERT](https://msdn.microsoft.com/library/ms174335.aspx).

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

2. Tryck på **CTRL+SKIFT+E** för att infoga en ny rad i Product-tabellen.

## <a name="update-data"></a>Uppdatera data

Med följande kod uppdaterar du den nya produkt du tidigare lade till med Transact-SQL-instruktionen [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx).

1.  I fönstret **Editor** tar du bort föregående fråga och skriver in följande fråga:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **CTRL+SKIFT+E** för att uppdatera angiven rad i Product-tabellen.

## <a name="delete-data"></a>Ta bort data

Med följande kod tar du bort den nya produkt du tidigare lade till med Transact-SQL-instruktionen [DELETE](https://msdn.microsoft.com/library/ms189835.aspx).

1. I fönstret **Editor** tar du bort föregående fråga och skriver in följande fråga:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **CTRL+SHIFT+E** för att ta bort angiven rad i Product-tabellen.

## <a name="next-steps"></a>Nästa steg

- Om du vill ansluta och fråga med SQL Server Management Studio kan du läsa [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- Mer information om att ansluta och ställa frågor med .NET finns i [Ansluta och fråga med .NET](sql-database-connect-query-dotnet.md).
- Mer information om att ansluta och ställa frågor med PHP finns i [Ansluta och fråga med PHP](sql-database-connect-query-php.md).
- Mer information om att ansluta och ställa frågor med Node.js finns i [Ansluta och fråga med Node.js](sql-database-connect-query-nodejs.md).
- Mer information om att ansluta och ställa frågor med Java finns i [Ansluta och fråga med Java](sql-database-connect-query-java.md).
- Mer information om att ansluta och ställa frågor med Python finns i [Ansluta och fråga med Python](sql-database-connect-query-python.md).
- Mer information om att ansluta och ställa frågor med Ruby finns i [Ansluta och fråga med Ruby](sql-database-connect-query-ruby.md).

