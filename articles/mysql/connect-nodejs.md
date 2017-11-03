---
title: "Ansluta till Azure Database för MySQL med Node.js | Microsoft Docs"
description: "I den här snabbstarten finns flera kodexempel i Node.js som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 2f18016614b229273aa4d661991149be949ce238
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-nodejs-to-connect-and-query-data"></a>Azure Database för MySQL: Använda Node.js för att ansluta och fråga efter data
Den här snabbstarten visar hur du ansluter till en Azure Database för MySQL med hjälp av ett [Node.js](https://nodejs.org/)-program från plattformar med Windows, Ubuntu Linux och Mac. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är bekant med att utveckla med Node.js och att du har arbetat med Azure-databas för MySQL.

## <a name="prerequisites"></a>Krav
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Du måste också:
- Installera [Node.js](https://nodejs.org) runtime.
- Installera [mysql2](https://www.npmjs.com/package/mysql2)-paketet för att ansluta till MySQL från Node.js-program. 

## <a name="install-nodejs-and-the-mysql-connector"></a>Installera Node.js och MySQL Connector
Följ anvisningarna i relevant avsnitt för att installera Node.js beroende på din plattform. Använd npm för att installera paketet mysql2 och dess beroenden i projektmappen.

### <a name="windows"></a>**Windows**
1. Besök den [Node.js hämtar sidan](https://nodejs.org/en/download/), och välj sedan din önskat alternativ för Windows installer.
2. Skapa en lokal projektmapp som till exempel `nodejsmysql`. 
3. Starta Kommandotolken och ändra katalogen till projektmappen som`cd c:\nodejsmysql\`
4. Kör verktyget NPM om du vill installera mysql2-biblioteket i projektmappen.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql2
   "C:\Program Files\nodejs\npm" list
   ```

5. Verifiera installationen genom att kontrollera `npm list`-utdatatexten för `mysql2@1.3.5`.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. Ange följande kommandon för att installera **npm** och **Node.js**, en användarvänlig pakethanterare för Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Kör följande kommandon för att skapa en projektmapp `mysqlnodejs` och installera paketet mysql2 till mappen.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```
3. Verifiera installationen genom att kontrollera npm-listans utdatatext för `mysql2@1.3.5`.

### <a name="mac-os"></a>**Mac OS**
1. Ange följande kommandon för att installera **brew**, en användarvänlig pakethanterare för Mac OS X och **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Kör följande kommandon för att skapa en projektmapp `mysqlnodejs` och installera paketet mysql2 till mappen.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```

3. Verifiera installationen genom att kontrollera `npm list`-utdatatexten för `mysql2@1.3.6`. Versionsnumret kan variera när nya korrigeringsfiler blir tillgängliga.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. I den vänstra rutan klickar du på **alla resurser** och söker sedan efter servern som du skapade (till exempel **myserver4demo**).
3. Klicka på servernamnet **myserver4demo**.
4. Välj servern **egenskaper** sidan och anteckna sedan en av **servernamn** och **serverinloggningsnamnet för admin**.
 ![Azure Database för MySQL – inloggning för serveradministratör](./media/connect-nodejs/1_server-properties-name-login.png)
5. Om du glömmer bort inloggningsinformationen servern navigerar du till den **översikt** om du vill visa serverinloggningsnamnet admin och återställa lösenordet om det behövs.

## <a name="running-the-javascript-code-in-nodejs"></a>Kör JavaScript-kod i Node.js
1. Klistra in JavaScript-kod i textfiler och spara den till en projektmapp med filen tillägget .js (till exempel C:\nodejsmysql\createtable.js eller /home/username/nodejsmysql/createtable.js).
2. Starta Kommandotolken eller bash shell och ändra katalogen till projektmappen `cd nodejsmysql`.
3. Skriv node-kommandot följt av filnamnet, till exempel `node createtable.js`, för att köra programmet.
4. I Windows, om node-programmet inte finns på sökvägen för miljövariabeln, kan du behöva använda den fullständiga sökvägen för att starta nodprogrammet, till exempel `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med hjälp av **CREATE TABLE** och **INSERT INTO** SQL-instruktioner.

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Funktionen [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Funktionen [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med hjälp av en **Välj** SQL-instruktionen. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. Resultatmatrisen används för resultat från frågan.

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och läsa data med hjälp av en **uppdatering** SQL-instruktionen. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med hjälp av en **ta bort** SQL-instruktionen. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md)
