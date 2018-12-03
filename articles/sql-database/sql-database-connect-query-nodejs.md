---
title: Fråga Azure SQL Database med Node.js | Microsoft Docs
description: Så använder du Node.js för att skapa ett program som ansluter till en Azure SQL-databas och kör frågor mot den med hjälp av SQL-instruktioner.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 3a6060c59c2b338a2fad3327fe89dcf3df955ef5
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496695"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Snabbstart: Använda Node.js för att köra frågor mot en Azure SQL-databas

Den här artikeln visar hur du använder [Node.js](https://nodejs.org) för att ansluta till en Azure SQL-databas. Du kan sedan använda T-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra det här exemplet behöver du följande:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till den dator som du använder

- Node.js-relaterad programvara för ditt operativsystem:

  - **MacOS**, installera Homebrew och Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, installera Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, installera Chocolatey och Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Hämta databasanslutning

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress skapar du en [brandväggsregel på servernivå med hjälp av Azure-portalen](sql-database-get-started-portal-firewall.md).

## <a name="create-the-project"></a>Skapa projektet

Öppna en kommandotolk och skapa en mapp med namnet *sqltest*. Navigera till den mapp som du har skapat och kör följande kommando:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Lägga till kod i frågedatabas

1. Skapa en ny fil, *sqltest.js*, i valfri textredigerare.

1. Ersätt dess innehåll med följande kod. Lägg sedan till lämpliga värden för servern, databas, användare och lösenord.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
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
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
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

> [!NOTE]
> Kodexemplet använder exempeldatabasen **AdventureWorksLT** för SQL Azure.

## <a name="run-the-code"></a>Kör koden

1. Kör programmet i kommandotolken.

    ```bash
    node sqltest.js
    ```

1. Kontrollera att de 20 översta raderna returneras och stäng programfönstret.

## <a name="next-steps"></a>Nästa steg

- [Microsoft-drivrutin för Node.JS för SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Ansluta och köra frågor på Windows/Linux/macOS med [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) eller [SSMS](sql-database-connect-query-ssms.md) (endast Windows)

- [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli)

- Designa din första Azure SQL-databas med hjälp av [.NET](sql-database-design-first-database-csharp.md) eller [SSMS](sql-database-design-first-database.md)
