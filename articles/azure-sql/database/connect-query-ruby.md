---
title: Använd ruby för att fråga
description: Det här avsnittet visar hur du använder ruby för att skapa ett program som ansluter till en databas i Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 1a0f65ff6d0b98d3764cb6a7b1d4b07ccb8ae92a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054409"
---
# <a name="quickstart-use-ruby-to-query-a-microsoft-azure-sql-database"></a>Snabb start: Använd ruby för att fråga en Microsoft Azure SQL-databas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här snabbstarten visar hur du använder [Ruby](https://www.ruby-lang.org) för att ansluta till en Azure SQL-databas och hur du frågar efter data med Transact-SQL-instruktioner.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En Azure SQL-databas. Du kan använda någon av dessa snabb starter för att skapa och konfigurera en databas i Azure SQL:

  || SQL Database | SQL-hanterad instans | SQL Server på Azure VM |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från en lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en hanterad instans måste du antingen importera Adventure Works-databasen till en instansdatabas eller ändra skripten i den här artikeln om du vill använda Wide World Importers-databasen.
  
- Ruby och relaterad programvara för ditt operativsystem:
  
  - **MacOS**: installera homebrew, rbenv och ruby-build, ruby, freetds och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: installera förutsättningar för ruby, rbenv och ruby-build, ruby, freetds och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: installera ruby, ruby devkit och TinyTDS. Se [Konfigurera utvecklingsmiljön för Ruby-utveckling](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Hämta anslutnings informationen du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server i en virtuell Azure-dator. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på en virtuell Azure-dator finns i [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

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
- [Skapa din första Azure SQL Database](design-first-database-tutorial.md).
- [GitHub-lagringsplats för TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Rapportera problem eller Ställ frågor om TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby-drivrutin för SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
