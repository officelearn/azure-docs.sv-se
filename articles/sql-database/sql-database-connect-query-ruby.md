---
title: "Ansluta till Azure SQL Database med hjälp av Ruby | Microsoft Docs"
description: "Anger ett Ruby kodexempel som du kan använda för att ansluta till och fråga en Azure SQL-databas."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: c5d09cf03c87c8da1d8588be62fea3f0cc3eec4f
ms.contentlocale: sv-se
ms.lasthandoff: 06/22/2017

---

# Azure SQL Database: Använd Ruby för att ansluta och skicka frågor till data
<a id="azure-sql-database-use-ruby-to-connect-and-query-data" class="xliff"></a>

Den här snabbstarten visar hur du använder [Ruby](https://www.ruby-lang.org) för att ansluta till en Azure SQL-databas. Använd sedan Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac OS- och Ubuntu Linux-plattformar.

## Krav
<a id="prerequisites" class="xliff"></a>

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)
- [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

## Installera Ruby- och databaskommunikationsbibliotek
<a id="install-ruby-and-database-communication-libraries" class="xliff"></a>

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med Ruby och att du är nybörjare när det gäller att arbeta med Azure SQL Database. Om du är nybörjare på utveckla med Ruby går du till [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Skapa en app med SQL Server) och väljer **Ruby** och sedan ditt operativsystem.

### **Mac OS**
<a id="mac-os" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Ruby-skriptet. Ange följande kommandon för att installera **brew**, **FreeTDS** och **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### **Linux (Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Ruby-skriptet. Ange följande kommandon för att installera **FreeTDS** och **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## Hämta anslutningsinformation
<a id="get-connection-information" class="xliff"></a>

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du glömmer inloggningsinformationen för din server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.
    

## Välj data
<a id="select-data" class="xliff"></a>
Använd följande kod för att söka efter de 20 främsta produkterna med funktionen [TinyTDS::Client ](https://github.com/rails-sqlserver/tiny_tds) och transact-SQL-instruktionen [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Funktionen TinyTDS::Client accepterar en fråga och returnerar en resultatuppsättning. Resultatuppsättningen kan upprepas med [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

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

## Infoga data
<a id="insert-data" class="xliff"></a>
Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med funktionen [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) med Transact-SQL-instruktionen [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

Det här exemplet visar hur du kör en INSERT-instruktion på ett säkert, överför parametrar som skyddar ditt program från [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)-problem och hämtar automatiskt genererade [primärnyckel](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)-värden.    
  
Om du vill använda TinyTDS med Azure, bör du köra flera `SET`-instruktioner för att ändra hanteringen av specifik information i den aktuella sessionen. Rekommenderade `SET`-instruktioner finns i kodexemplet. Till exempel, `SET ANSI_NULL_DFLT_ON` låter nya kolumner tillåta null-värden, även om detta inte har angetts uttryckligen för kolumnen.  
  
För att samverka med Microsoft SQL Serverns [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) använder du funktionen [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) för att använda motsvarande datum- och tidformat.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## Uppdatera data
<a id="update-data" class="xliff"></a>
Med följande kod uppdaterar du den nya produkt du tidigare lade till med funktionen [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) och Transact-SQL-instruktionen [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) . Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## Ta bort data
<a id="delete-data" class="xliff"></a>
Med följande kod raderar du den nya produkt du tidigare lade till med funktionen [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) och Transact-SQL-instruktionen [DELETe](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) . Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## Nästa steg
<a id="next-steps" class="xliff"></a>
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [GitHub-lagringsplatsen för TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Rapportera problem/ställ frågor](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby-drivrutiner för SQLServer](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


