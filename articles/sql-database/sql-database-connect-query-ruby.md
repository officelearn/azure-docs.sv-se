---
title: Fråga Azure SQL Database med Ruby | Microsoft Docs
description: Det här avsnittet visar hur du använder Ruby för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f86c53465636f82cf36d5099699fc9e6efeb55a5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704665"
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Använd Ruby för att fråga en Azure SQL-databas

Den här snabbstarten visar hur du använder [Ruby](https://www.ruby-lang.org) för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här snabbstarten behöver du följande:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- Du har installerat Ruby och relaterad programvara för ditt operativsystem:
    - **MacOS**: Installera Homebrew, installera rbenv och Ruby-build, installera Ruby och sedan FreeTDS. Se [steg 1.2, 1.3, 1.4 och 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: Installera förhandskraven för Ruby, installera rbenv och Ruby-build, installera Ruby och sedan FreeTDS. Se [steg 1.2, 1.3, 1.4 och 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas

1. Skapa en ny fil, **sqltest.py**, i valfri textredigerare

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för din server, databas, användare och lösenord.

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
