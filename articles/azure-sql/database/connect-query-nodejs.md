---
title: Använd Node. js för att fråga en databas
description: Hur du använder Node. js för att skapa ett program som ansluter till en databas i Azure SQL Database och frågar den med hjälp av T-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2 
ms.openlocfilehash: 5f53d6b3e8b477d7b93eb1063679126a9533ef03
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054481"
---
# <a name="quickstart-use-nodejs-to-query-a-microsoft-azure-sql-database"></a>Snabb start: Använd Node. js för att fråga en Microsoft Azure SQL-databas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här snabb starten använder du Node. js för att ansluta till en Azure SQL-databas och använda T-SQL-uttryck för att fråga efter data.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

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


- [Node. js](https://nodejs.org)-relaterad program vara

  # <a name="macos"></a>[macOS](#tab/macos)

  Installera homebrew och Node. js och installera sedan ODBC-drivrutinen och SQLCMD med steg **1,2** och **1,3** i [skapa Node. js-appar med hjälp av SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installera Node. js och installera sedan ODBC-drivrutinen och SQLCMD med steg **1,2** och **1,3** i [skapa Node. js-appar med hjälp av SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installera choklad och Node. js och installera sedan ODBC-drivrutinen och SQLCMD med steg **1,2** och **1,3** i [skapa Node. js-appar med hjälp av SQL Server i Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works** -databasen.

> [!NOTE]
> Alternativt kan du välja att använda en hanterad Azure SQL-instans.
>
> Om du vill skapa och konfigurera använder du [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)eller [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)och sedan installerar du anslutningar [på plats](../managed-instance/point-to-site-p2s-configure.md) eller [virtuell dator](../managed-instance/connect-vm-instance-configure.md) .
>
> Om du vill läsa in data, se [restore with BACPAC](database-import.md) med [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -filen, eller se [återställa Wide World imports-databasen](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Hämta anslutnings informationen som du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser** eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server i en virtuell Azure-dator. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på en virtuell Azure-dator finns i [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

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

- Ansluta och köra frågor på Windows/Linux/macOS med [.NET core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md) eller [SSMS](connect-query-ssms.md) (endast Windows)

- [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli)

- Utforma din första Azure SQL Database med [.net](design-first-database-csharp-tutorial.md) eller [SSMS](design-first-database-tutorial.md)
