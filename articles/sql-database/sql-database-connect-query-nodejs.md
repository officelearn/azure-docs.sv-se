---
title: Fråga Azure SQL Database med Node.js | Microsoft Docs
description: Det här avsnittet visar hur du använder Node.js för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: c6cfbd330f77c5a2f448093e58684032a84a41d9
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063480"
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Använd Node.js för att fråga en Azure SQL-databas

Den här snabbstarten visar hur du använder [Node.js](https://nodejs.org/en/) för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- Du har installerat Node.js och relaterad programvara för ditt operativsystem:
    - **MacOS**: Installera Homebrew och Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu**: Installera Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) .
    - **Windows**: Installera Chocolatey och Node.js och installera därefter ODBC-drivrutinen och SQL CMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal-firewall.md). 

## <a name="create-a-nodejs-project"></a>Skapa ett Node.js-projekt

Öppna en kommandotolk och skapa en mapp med namnet *sqltest*. Navigera till den mapp som du har skapat och kör följande kommando:

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas

1. Skapa en ny fil **sqltest.js** i din utvecklingsmiljö eller valfri textredigerare.

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för din server, databas, användare och lösenord.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommandon i kommandotolken:

   ```js
   node sqltest.js
   ```

2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.

## <a name="next-steps"></a>Nästa steg

- Läs om [Microsoft Node.js-drivrutinen för SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Lär dig hur du [ansluter och frågar en Azure SQL-databas med .NET Core](sql-database-connect-query-dotnet-core.md) på Windows/Linux/macOS.  
- Lär dig mer om att [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [Utformar din första Azure SQL-databas med hjälp av SSMS](sql-database-design-first-database.md) eller [Utformar din första Azure SQL-databas med hjälp av .NET](sql-database-design-first-database-csharp.md).
- Läs hur du [ansluter och frågar med SSMS](sql-database-connect-query-ssms.md)
- Läs hur du [ansluter och frågar med Visual Studio Code](sql-database-connect-query-vscode.md).

