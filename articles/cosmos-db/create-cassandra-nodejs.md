---
title: 'Snabbstart: Cassandra API med Node.js – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Cassandra API i Azure Cosmos DB för att skapa ett profilprogram med Node.js
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 429b8845e49158c906c02773f654c9487ff98d1e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134736"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Snabb start: Bygg en Cassandra-app med Node. js SDK och Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

I den här snabb starten skapar du ett Azure Cosmos DB API för Cassandra konto och använder en Cassandra Node. js-app som klonas från GitHub för att skapa en Cassandra-databas och-behållare. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
- [Node. js-0.10.29 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk. Skapa en ny mapp med namnet `git-samples`. Stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, till exempel git bash. Använd kommandot `cd` för att ändra till den nya mappen för att installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kodavsnitt kommer från filen `uprofile.js` i mappen `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

* Användarnamnet och lösenordet angavs med hjälp av anslutningssträngsidan i Azure-portalen. `path\to\cert` tillhandahåller en sökväg till X509-certifikatet. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client` har initierats med contactPoint-information. contactPoint hämtas från Azure-portalen.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` ansluter till Azure Cosmos DB Cassandra-API.

    ```javascript
    client.connect(next);
    ```

* Ett nytt keyspace skapas.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* En ny tabell skapas.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Nyckel/värde-entiteter infogas.

    ```javascript
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Fråga för att hämta alla nyckelvärden.

    ```javascript
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Fråga för att hämta ett nyckelvärde.

    ```javascript
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen. Anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. Välj **anslutnings sträng**i Azure Cosmos DB-kontot i [Azure Portal](https://portal.azure.com/). 

    Använd ![Knappen Kopiera](./media/create-cassandra-nodejs/copy.png) knappen på höger sida av skärmen för att kopiera det övre värdet, KONTAKTPUNKT.

    ![Visa och kopiera KONTAKTPUNKT, ANVÄNDARNAMN och LÖSENORD från Azure Portal, sidan med anslutningssträng](./media/create-cassandra-nodejs/keys.png)

2. Öppna filen `config.js`. 

3. Klistra in KONTAKTPUNKT-värdet från portalen ovanpå `<FillMEIN>` på rad 4.

    Rad 4 bör nu se ut ungefär så här 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Kopiera ANVÄNDARNAMN-värdet från portalen och klistra in det ovanpå `<FillMEIN>` på rad 2.

    Rad 2 bör nu se ut ungefär så här 

    `config.username = 'cosmos-db-quickstart';`
    
5. Kopiera LÖSENORD-värdet från portalen och klistra in det ovanpå `<FillMEIN>` på rad 3.

    Rad 3 bör nu se ut ungefär så här

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Spara filen `config.js`.
    
## <a name="use-the-x509-certificate"></a>Använd X509-certifikatet

1. Ladda ned Baltimore CyberTrust-rotcertifikatet lokalt från [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Byt namn på filen med filnamnstillägget `.cer`.

   Certifikatet har serienummer `02:00:00:b9` och SHA1-fingeravtryck `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Öppna `uprofile.js` och ändra `path\to\cert` så att den pekar på det nya certifikatet.

3. Spara `uprofile.js`.

## <a name="run-the-nodejs-app"></a>Köra Node.js-appen

1. I git-terminalfönstret kör du `npm install` för att installera de npm-moduler som krävs.

2. Kör `node uprofile.js` för att starta nodprogrammet.

3. Kontrollera att resultatet är som förväntat från kommandoraden.

    ![Granska och verifiera utdata.](./media/create-cassandra-nodejs/output.png)

    Tryck på CTRL + C för att stoppa körningen av programmet och stänga konsol fönstret. 

4. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data. 

    ![Visa data i Datautforskaren](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto med API för Cassandra och kör en Cassandra Node. js-app som skapar en Cassandra-databas och-behållare. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)


