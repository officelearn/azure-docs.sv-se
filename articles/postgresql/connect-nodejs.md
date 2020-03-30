---
title: Använd nod.js för att ansluta till Azure Database för PostgreSQL - Single Server
description: Den här snabbstarten innehåller ett nod.js-kodexempel som du kan använda för att ansluta och fråga data från Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom:
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 1d48fc818ca32b6168b0986ddb6453fe66fc8341
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80062263"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabbstart: Använd nod.js för att ansluta och fråga data i Azure Database för PostgreSQL - Single Server

I den här snabbstarten ansluter du till en Azure-databas för PostgreSQL med hjälp av ett Node.js-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. I den här artikeln förutsätter vi att du har kunskaper om Node.js och att du inte har arbetat med Azure Database for PostgreSQL tidigare.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Slutförande av [snabbstart: Skapa en Azure-databas för PostgreSQL-server i Azure-portalen](quickstart-create-server-database-portal.md) eller [Snabbstart: Skapa en Azure-databas för PostgreSQL med Hjälp av Azure CLI](quickstart-create-server-database-azure-cli.md).

- [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>Installera pg-klienten
Installera [pg](https://www.npmjs.com/package/pg), som är en PostgreSQL-klient för Node.js.

Kör du node package manager (npm) för JavaScript från kommandoraden och installera pg-klienten.
```bash
npm install pg
```

Verifiera installationen genom att visa en lista över de paket som har installerats.
```bash
npm list
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta den information som du behöver för att ansluta till Azure Database för PostgreSQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Sök efter och välj den server som du har skapat i [Azure-portalen](https://portal.azure.com/)(till exempel **mydemoserver**).

1. Anteckna **servernamn** och **administratörsanvändarnamn**på panelen **Översikt** på serverns översiktspanel . Om du glömmer lösenordet kan du även återställa det på den här panelen.

   ![Azure-databas för PostgreSQL-anslutningssträng](./media/connect-nodejs/server-details-azure-database-postgresql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Kör JavaScript-kod i Node.js
Du kan köra Node.js från Bash-gränssnittet, terminalen eller Windows kommandotolk genom att skriva `node` och sedan köra JavaScript-exempelkoden interaktivt genom att kopiera och klistra in den i kommandotolken. Du kan också spara JavaScript-koden i en textfil och starta `node filename.js` med namnet som en parameter för att köra den.

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med hjälp av SQL-instruktionerna **CREATE TABLE** och **INSERT**.
Objektet [pg. Klienten](https://github.com/brianc/node-postgres/wiki/Client) används för gränssnittet med PostgreSQL-server. Funktionen [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) används för att etablera anslutningen till servern. Funktionen [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) används för att köra SQL-frågor mot en PostgreSQL-databas. 

Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. Objektet [pg. Klienten](https://github.com/brianc/node-postgres/wiki/Client) används för gränssnittet med PostgreSQL-server. Funktionen [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) används för att etablera anslutningen till servern. Funktionen [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) används för att köra SQL-frågor mot en PostgreSQL-databas. 

Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och läsa data med en **UPDATE**-SQL-instruktion. Objektet [pg. Klienten](https://github.com/brianc/node-postgres/wiki/Client) används för gränssnittet med PostgreSQL-server. Funktionen [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) används för att etablera anslutningen till servern. Funktionen [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) används för att köra SQL-frågor mot en PostgreSQL-databas. 

Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion. Objektet [pg. Klienten](https://github.com/brianc/node-postgres/wiki/Client) används för gränssnittet med PostgreSQL-server. Funktionen [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) används för att etablera anslutningen till servern. Funktionen [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) används för att köra SQL-frågor mot en PostgreSQL-databas. 

Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
