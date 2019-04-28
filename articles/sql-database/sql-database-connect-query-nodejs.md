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
ms.date: 03/25/2019
ms.openlocfilehash: cd0435ffc27402299da85434693dcdf32a4083ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408936"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Snabbstart: Använd Node.js för att fråga en Azure SQL-databas

Den här artikeln visar hur du använder [Node.js](https://nodejs.org) för att ansluta till en Azure SQL-databas. Du kan sedan använda T-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra det här exemplet behöver du följande:

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


- Node.js-relaterad programvara för ditt operativsystem:

  - **MacOS**, installera Homebrew och Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, installera Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, installera Chocolatey och Node.js och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**. 

## <a name="create-the-project"></a>Skapa projektet

Öppna en kommandotolk och skapa en mapp med namnet *sqltest*. Navigera till den mapp som du har skapat och kör följande kommando:

  ```bash
  npm init -y
  npm install tedious@5.0.3
  npm install async@2.6.2
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
        authentication: {
            options: {
                userName: 'userName', // update me
                password: 'password' // update me
            },
            type: 'default'
        },
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
        var request = new Request(
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
