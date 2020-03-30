---
title: Använda Node.js för att fråga en databas
description: Så använder du Node.js för att skapa ett program som ansluter till en Azure SQL-databas och kör frågor mot den med hjälp av SQL-instruktioner.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768607"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Snabbstart: Använda Node.js för att köra frågor mot en Azure SQL-databas

I den här snabbstarten använder du Node.js för att ansluta till en Azure SQL-databas och använda T-SQL-uttryck för att fråga data.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL-databas](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org)-relaterad programvara

  # <a name="macos"></a>[Macos](#tab/macos)

  Installera Homebrew och Node.js och installera sedan ODBC-drivrutinen och SQLCMD med hjälp av steg **1.2** och **1.3** i [Skapa node.js-appar med SQL Server på macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installera Node.js och installera sedan ODBC-drivrutinen och SQLCMD med hjälp av steg **1.2** och **1.3** i [Skapa node.js-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installera Chocolatey och Node.js och installera sedan ODBC-drivrutinen och SQLCMD med hjälp av steg **1.2** och **1.3** i [Skapa nod.js-appar med SQL Server i Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works-databasen.**

> [!NOTE]
> Du kan också välja att använda en Azure SQL-hanterad instans.
>
> Om du vill skapa och konfigurera använder du [Azure Portal,](sql-database-managed-instance-get-started.md) [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)eller [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)och konfigurerar sedan anslutning [på plats](sql-database-managed-instance-configure-p2s.md) eller [virtuell dator.](sql-database-managed-instance-configure-vm.md)
>
> Information om hur du läser [återställa med BACPAC](sql-database-import.md) med [Adventure Works-filen](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) eller i återställa [wide world-importördatabasen](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser** eller **SQL-hanterade instanser.**

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**. 

## <a name="create-the-project"></a>Skapa projektet

Öppna en kommandotolk och skapa en mapp med namnet *sqltest*. Öppna mappen som du skapade och kör följande kommando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Lägga till kod i frågedatabas

1. Skapa en ny fil, *sqltest.js*, i valfri textredigerare.

1. Ersätt dess innehåll med följande kod. Lägg sedan till lämpliga värden för servern, databas, användare och lösenord.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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
