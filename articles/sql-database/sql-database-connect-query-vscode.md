---
title: 'VS-kod: Ansluta till och fråga efter data i Azure SQL Database | Microsoft Docs'
description: Lär dig hur du ansluter till SQL Database på Azure med hjälp av Visual Studio Code. Kör sedan Transact-SQL-uttryck (T-SQL) för att söka i och redigera data.
keywords: ansluta till sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 20d6ccca448d53da54835aad1d6dd85702c7390f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446926"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Snabbstart: Använd Visual Studio Code för att ansluta till och söka i en Azure SQL-databas

[Visual Studio Code](https://code.visualstudio.com/docs) är en grafisk kodredigerare för Linux, macOS och Windows. Den stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för frågor till Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse. I den här snabbstarten får du använda Visual Studio Code för att ansluta till en Azure SQL-databas och därefter köra Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data.

## <a name="prerequisites"></a>Förutsättningar

- En Azure SQL-databas. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  || Enskild databas | Hanterad instans |
  |:--- |:--- |:---|
  | Skapa| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurera | [IP-brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)| [Anslutning från en virtuell dator](sql-database-managed-instance-configure-vm.md)|
  |||[Anslutning från en lokal plats](sql-database-managed-instance-configure-p2s.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Återställa eller importera Adventure Works från [BACPAC](sql-database-import.md) fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en hanterad instans måste du antingen importera Adventure Works-databasen till en instansdatabas eller ändra skripten i den här artikeln om du vill använda Wide World Importers-databasen.

## <a name="install-visual-studio-code"></a>Installera Visual Studio Code

Kontrollera att du har installerat den senaste versionen av [Visual Studio Code](https://code.visualstudio.com/Download) och har läst in [mssql-tillägget](https://aka.ms/mssql-marketplace). Hjälp med att installera mssql-tillägget finns i [Installera VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) och [mssql för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code 

### <a name="mac-os"></a>**Mac OS**

För macOS måste du installera OpenSSL, vilket är en förutsättning för .NET Core som används av mssql-tillägget. Ange följande kommandon för att installera **brew** och **OpenSSL**. 

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

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**.

## <a name="set-language-mode-to-sql"></a>Ange språkläge till SQL

Ställ in språkläget på **SQL** i Visual Studio Code för att aktivera mssql-kommandon och T-SQL IntelliSense.

1. Öppna ett Visual Studio Code-fönster. 

2. Tryck på **Ctrl**+**N**. En ny oformaterad textfil öppnas. 

3. Välj **Oformaterad text** nere till höger i statusfältet.

4. Välj **SQL** i den nedrullningsbara menyn **Välj språkläge** som öppnas. 

## <a name="connect-to-your-database"></a>Ansluta till databasen

Använd Visual Studio Code för att upprätta en anslutning till Azure SQL Database-servern.

> [!IMPORTANT]
> Kontrollera att du har din server- och inloggningsinformation till hands innan du fortsätter. Om du ändrar fokus från Visual Studio Code när du har börjat ange information om anslutningsprofilen måste du börja om med att skapa profilen.
>

1. I Visual Studio Code trycker du på **Ctrl+Shift+P** (eller **F1**) för att öppna kommandopaletten.

2. Välj **MS SQL:Anslut** och välj **Retur**.

3. Välj **Skapa anslutningsprofil**.

4. Följ anvisningarna för att ange anslutningsegenskaper för den nya profilen. När du har angett varje värde så väljer du **Retur** för att fortsätta. 

   | Egenskap        | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Servernamn** | Fullständigt kvalificerat servernamn | Ungefär så här: **mynewserver20170313.database.windows.net**. |
   | **Databasnamn** | mySampleDatabase | Databasen du ska ansluta till. |
   | **Autentisering** | SQL-inloggning| Den här självstudien använder SQL-autentisering. |
   | **Användarnamn** | Användarnamn | Användarnamnet från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord (SQL-inloggning)** | Lösenord | Lösenordet från det serveradministratörskonto som användes när servern skapades. |
   | **Spara lösenordet?** | Ja eller nej | Välj **Ja** om du inte vill ange lösenordet varje gång. |
   | **Ange ett namn för den här profilen** | Ett profilnamn, t.ex. **mySampleProfile** | En sparad profil förbättrar anslutningen på efterföljande inloggningar. | 

   Om det lyckas visas ett meddelande om att din profil har skapats och anslutits.

## <a name="query-data"></a>Söka i data

Kör följande [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-instruktion för att fråga efter de 20 främsta produkterna per kategori.

1. Klistra in följande SQL-fråga i redigeringsfönstret.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Tryck på **Ctrl**+**Skift**+**E** för att köra frågan och visa resultat från tabellerna `Product` och `ProductCategory`.

    ![Fråga för att hämta data från 2 tabeller](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Infoga data

Kör följande [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-instruktion för att lägga till en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den tidigare frågan med denna.

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

2. Tryck på **Ctrl**+**Shift**+**E** för att infoga en ny rad i tabell `Product`.

## <a name="update-data"></a>Uppdatera data

Kör följande [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-instruktion för att uppdatera den tillagda produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **Ctrl**+**Shift**+**E** för att uppdatera den angivna raden i tabellen `Product`.

## <a name="delete-data"></a>Ta bort data

Kör följande [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL-instruktion för att ta bort den nya produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **Ctrl**+**Shift**+**E** för att ta bort den angivna raden i tabellen `Product`.

## <a name="next-steps"></a>Nästa steg

- För att ansluta och köra frågor med SQL Server Management Studio, se [Snabbstart: Använd SQL Server Management Studio för att ansluta till en Azure SQL Database och skicka frågor till data](sql-database-connect-query-ssms.md).
- För att ansluta och skicka frågor med Azure Portal, se [Snabbstart: Använd SQL-frågeredigeraren i Azure Portal för att ansluta och fråga efter data](sql-database-connect-query-portal.md).
- En artikel från MSDN-magazine om hur du använder Visual Studio Code finns i [Skapa en IDE-databas med MSSQL-tillägget blogginlägg](https://msdn.microsoft.com/magazine/mt809115).
