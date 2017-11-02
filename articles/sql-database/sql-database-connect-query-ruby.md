---
title: "Fråga Azure SQL Database med Ruby | Microsoft Docs"
description: "Det här avsnittet visar hur du använder Ruby för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 07/14/2017
ms.author: carlrab
ms.openlocfilehash: b7617d8f4cfe09052204182b40f6e5f7d46087b8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Använd Ruby för att fråga en Azure SQL-databas

Den här snabbstartsguiden visar hur man använder [Ruby](https://www.ruby-lang.org) för att skapa ett program för att ansluta till en Azure SQL-databas och använda Transact-SQL-uttryck för att fråga data.

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här snabbstartskursen behöver du följande:

- En Azure SQL-databas. Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter: 

   - [Skapa DB – Portal](sql-database-get-started-portal.md)
   - [Skapa DB – CLI](sql-database-get-started-cli.md)
   - [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

- En [brandväggsregel på servernivå](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) för den offentliga IP-adressen för den dator du använder för den här snabbstartskursen.
- Du har installerat Ruby och relaterad programvara för ditt operativsystem.
    - **MacOS**: Installera Homebrew, installera rbenv och Ruby-build, installera Ruby och sedan FreeTDS. Se [steg 1.2, 1.3, 1.4 och 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: Installera förhandskraven för Ruby, installera rbenv och Ruby-build, installera Ruby och sedan FreeTDS. Se [steg 1.2, 1.3, 1.4 och 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Kontrollera det fullständigt kvalificerade servernamnet på sidan **Översikt** för databasen. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**, vilket visas på följande bild:

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du har glömt inloggningsinformationen för Azure SQL Database-server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL Database

1. Skapa en ny fil, **sqltest.py**, i valfri textredigerare

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för server, databas, användare och lösenord.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

1. Kör följande kommandon i kommandotolken:

   ```bash
   ruby sqltest.rb
   ```

2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.


## <a name="next-steps"></a>Nästa steg
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [GitHub-lagringsplatsen för TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Rapportera problem eller ställ frågor om TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby-drivrutiner för SQLServer](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
