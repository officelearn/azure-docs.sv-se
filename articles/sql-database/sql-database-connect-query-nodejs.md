---
title: "Ansluta till Azure SQL Database med hjälp av Node.Js | Microsoft Docs"
description: "Anger ett Node.js kodexempel som du kan använda för att ansluta till och fråga en Azure SQL-databas."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: a7b1a504c6ae7850e5f986895adcf4d92fd5adc1
ms.contentlocale: sv-se
ms.lasthandoff: 06/15/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database: Använd Node.js för att ansluta och skicka frågor till data

Den här snabbstarten visar hur du använder [Node.js](https://nodejs.org/en/) för att ansluta till en Azure SQL-databas. Använd sedan Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Windows-, Mac OS- och Ubuntu Linux-plattformar.

## <a name="prerequisites"></a>Krav

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)
- [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>Installera Node.js 

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med Node.js och att du är nybörjare när det gäller att arbeta med Azure SQL Database. Om du är nybörjare på utveckla med Node.js går du till [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Skapa en app med SQL Server) och väljer **Node.js** och sedan ditt operativsystem.

### <a name="mac-os"></a>**Mac OS**
Ange följande kommandon för att installera **brew**, en användarvänlig pakethanterare för Mac OS X och **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ange följande kommandon för att installera **npm**, en användarvänlig pakethanterare för Mac OS X, och **Node.js**.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Besök [hämtningssidan för Node.js ](https://nodejs.org/en/download/) och välj önskat alternativ för Windows installeringsverktyg.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Installera Tedious SQL Server-drivrutinen för Node.js
Rekommenderade drivrutinen för Node.js är **[tedious](https://github.com/tediousjs/tedious)**. Tedious är ett initiativ med öppen källkod som Microsoft bidrar till för Node.js-program på alla plattformar. För den här kursen behöver du en tom katalog som innehåller koden och `npm`-beroenden som vi ska installera.

Kör följande kommando i din katalog för att installera drivrutinen **tedious**:

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du har glömt inloggningsinformationen för Azure SQL Database-server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.
    
## <a name="select-data"></a>Välj data

Du kan använda följande kod för att fråga din Azure SQL-databas om de främsta 20 produkterna efter kategori. Importera först anslutnings- och begärandrivrutiner från tedious-biblioteket. Skapa sedan konfigurationsobjektet och ersätt **server**, **databas**, **användarnamn** och **lösenordsparametrar** med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. Skapa ett `Connection`-objekt med det angivna `config`-objektet. Definiera sedan motringning för `connect`-händelsen för `connection`-objektet så att `queryDatabase()`-funktionen körs.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
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

## <a name="insert-data-into-the-database"></a>Infoga data i databasen
Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med funktionen `insertIntoDatabase()` och transact-SQL-instruktionen [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Ersätt **server**, **databas**, **användarnamn** och **lösenordsparametrar** med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Uppdatera data i databasen
Med följande kod uppdaterar du den nya produkt du tidigare lade till med funktionen `updateInDatabase()` och Transact-SQL-instruktionen [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Ersätt **server**, **databas**, **användarnamn** och **lösenordsparametrar** med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. Det här exemplet används produktnamnet som infogades i föregående exempel.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Ta bort data från databasen
Använd följande kod för att ta bort data från databasen. Ersätt **server**, **databas**, **användarnamn** och **lösenordsparametrar** med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. Använd en **DELETE-instruktion** i `deleteFromDatabase()`-funktionen. Det här exemplet används produktnamnet som infogades i föregående exempel.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Nästa steg
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft-drivrutin för Node.JS för SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- [Anslut och fråga med Visual Studio Code](sql-database-connect-query-vscode.md).



