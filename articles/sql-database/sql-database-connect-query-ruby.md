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
ms.date: 12/20/2018
ms.openlocfilehash: 8fa161f82cf73e61127ab67aba6ca3408c8e7c10
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197831"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Snabbstart: Använd Ruby för att fråga en Azure SQL-databas

Den här snabbstarten visar hur du använder [Ruby](https://www.ruby-lang.org) för att ansluta till en Azure SQL-databas och hur du frågar efter data med Transact-SQL-instruktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Ruby och relaterad programvara för ditt operativsystem:
  
  - **MacOS**: Installera Homebrew, rbenv och ruby-build, Ruby, FreeTDS och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Installera förhandskraven för Ruby, rbenv och ruby-build, Ruby, FreeTDS och TinyTDS. Se steg 1.2, 1.3, 1.4, 1.5 och 2.1 i [Skapa Ruby-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Installera Ruby, Ruby Devkit och TinyTDS. Se [Konfigurera utvecklingsmiljön för Ruby-utveckling](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
