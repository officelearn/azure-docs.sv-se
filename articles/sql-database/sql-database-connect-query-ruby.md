---
title: Fråga Azure SQL Database med Ruby | Microsoft Docs
description: Det här avsnittet visar hur du använder Ruby för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: bea32acf4028a4ab43fcc8cd967a31ea397babf3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234228"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Snabbstart: Använd Ruby för att fråga en Azure SQL-databas

Den här snabbstarten visar hur du använder [Ruby](https://www.ruby-lang.org) för att ansluta till en Azure SQL-databas och hur du frågar efter data med Transact-SQL-instruktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En Azure SQL-databas. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  || Enskild databas | Hanterad instans |
  |:--- |:--- |:---|
  | Skapa| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurera | [IP-brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)| [Anslutning från en virtuell dator](sql-database-managed-instance-configure-vm.md)|
  |||[Anslutning från en lokal plats](sql-database-managed-instance-configure-p2s.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Återställa eller importera Adventure Works från [BACPAC](sql-database-import.md)-fil från [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en hanterad instans måste du antingen importera Adventure Works-databasen till en instansdatabas eller ändra skripten i den här artikeln om du vill använda Wide World Importers-databasen.
  
- Ruby och relaterad programvara för ditt operativsystem:
  
  - **MacOS**: Installera Homebrew, rbenv och ruby-build, Ruby, FreeTDS och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Installera förhandskraven för Ruby, rbenv och ruby-build, Ruby, FreeTDS och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Installera Ruby, Ruby Devkit och TinyTDS. Se [Konfigurera utvecklingsmiljön för Ruby-utveckling](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**. 

## <a name="create-code-to-query-your-sql-database"></a>Skapa kod för att fråga din SQL-databas

1. Skapa en ny fil med namnet *sqltest.rb* i en text- eller kodredigerare.
   
1. Lägg till följande kod. Byt ut värdena från Azure SQL-databasen för `<server>`, `<database>`, `<username>` och `<password>`.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommando i en kommandotolk:

   ```bash
   ruby sqltest.rb
   ```
   
1. Kontrollera att de 20 främsta raderna för kategori/produkt från databasen returneras. 

## <a name="next-steps"></a>Nästa steg
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md).
- [GitHub-lagringsplatsen för TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Rapportera problem eller ställ frågor om TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby-drivrutiner för SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
