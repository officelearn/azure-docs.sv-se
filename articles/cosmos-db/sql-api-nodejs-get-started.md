---
title: Självstudiekurs om Node.js för SQL-API:et för Azure Cosmos DB | Microsoft-dokument
description: En Node.js-självstudiekurs som demonstrerar hur du ansluter och frågar Azure Cosmos DB med SQL-API:et
services: cosmos-db
author: deborahc
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 7eeb4fdba8272d9bc2b67d8a33dd7b1d210f5e47
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278607"
---
# <a name="tutorial-build-a-nodejs-console-app-with-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Självstudie: Skapa ett Node.js-konsolprogram med JavaScript SDK för att hantera Azure Cosmos DB SQL API-data

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Den här självstudien visar hur du skapar ett Node.js-konsolprogram som skapar Azure Cosmos DB-resurser och skickar frågor mot dem.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos DB-konto
> * Konfigurera ditt program
> * Skapa en databas
> * Skapa en container
> * Lägga till objekt i containern
> * Utföra CRUD-åtgärder på objekt, container och databas

Har du inte tid att skapa programmet? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). I avsnittet [Hämta den kompletta lösningen](#GetSolution) i den här artikeln finns en snabbguide.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Se till att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) version v6.0.0 eller senare.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [konfigurationen av Node.js-programmet](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Steg 2: Konfigurera Node.js-programmet

1. Öppna valfri terminal.
2. Leta reda på mappen eller katalogen där du vill spara Node.js-programmet.
3. Skapa två tomma JavaScript-filer med följande kommandon:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Installera modulen @azure/cosmos via npm. Ange följande kommando:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Steg 3: Ange appkonfigurationer

1. Öppna ```config.js``` i valfri textredigerare.

1. Kopiera och klistra in kodfragmentet nedan och ange egenskaper för ```config.endpoint``` och ```config.primaryKey``` till slutpunkts-URI och primärnyckel i Azure Cosmos DB. Båda dessa konfigurationer finns i [Azure Portal](https://portal.azure.com).

   ![Hämta nycklar från Azure-portalen, skärmbild][keys]

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Kopiera och klistra in data från ```database```, ```container``` och ```items``` till ditt ```config```-objekt nedan där du anger ```config.endpoint```- och ```config.primaryKey```-egenskaper. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](import-data.md) för Azure Cosmos DB i stället att definiera data här.

   ```nodejs
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   Observera att om du är bekant med den tidigare versionen av JavaScript SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder [flera API-modeller](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities) använder version 2.0+ av JavaScript-SDK:et de allmänna termerna ”container” och ”objekt”. En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. 

1. Exportera slutligen ```config```-objektet, så att du kan referera till det i filen ```app.js```.

   ```nodejs
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Steg 4: Ansluta till ett Azure Cosmos DB-konto

1. Öppna den tomma filen ```app.js``` i textredigeraren. Kopiera och klistra in koden nedan för att importera modulen ```@azure/cosmos``` och modulen ```config``` som du just skapade.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');
   ```

1. Kopiera och klistra in koden för att använda ```config.endpoint``` och ```config.primaryKey``` som du tidigare skapade och för att skapa en ny CosmosClient.

   ```nodejs
   const url = require('url');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```

Nu har du koden för att initiera Azure Cosmos DB-klienten och det är dags att gå vidare till att arbeta med Azure Cosmos DB-resurser.

## <a name="step-5-create-a-database"></a>Steg 5: Skapa en databas

1. Kopiera och klistra in koden nedan för att ange databasens ID och containerns ID. Det är via ID:na som Azure Cosmos DB-klienten hittar rätt databas och container.

   ```nodejs
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   En [databas](sql-api-resources.md#databases) kan skapas genom att använda funktionen [createIfNotExists](/javascript/api/%40azure/cosmos/databases) eller [create](/javascript/api/%40azure/cosmos/databases) för klassen **Databaser**. En databas är en logisk container med objekt som är partitionerade över containrar. 

2. Kopiera och klistra in metoderna **createDatabase** och **readDatabase** i app.js-filen under definitionen för ```databaseId``` och ```containerId```. Funktionen **createDatabase** skapar en ny databas med ID ```FamilyDatabase```, som anges från ```config```-objektet om den inte redan finns. Funktionen **readDatabase** läser databasens definition för att säkerställa att databasen finns.

   ```nodejs
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Kopiera och klistra in koden nedanför där du anger funktionerna **createDatabase** och **readDatabase** för att lägga till hjälpfunktionenen **exit** som skriver ut avslutsmeddelandet. 

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Kopiera och klistra in koden nedanför där du anger funktionen **exit** för att anropa funktionerna **createDatabase** och **readDatabase**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Nu bör din kod i ```app.js``` se ut som följande kod:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. Leta upp filen ```app.js``` i terminalen och kör kommandot: 

   ```bash 
   node app.js
   ```

Grattis! Du har skapat en Azure Cosmos DB-databas.

## <a id="CreateContainer"></a>Steg 6: Skapa en container

> [!WARNING]
> När du anropar funktionen **createContainer** skapas en ny container, med konsekvenser för priset. Mer information finns på vår [prissättningssida](https://azure.microsoft.com/pricing/details/cosmos-db/).

En container kan skapas med funktionen [createIfNotExists](/javascript/api/%40azure/cosmos/containers) eller [create](/javascript/api/%40azure/cosmos/containers) från klassen **Containers**.  En container består av objekt (som i fallet med SQL-API:et är JSON-dokument) och associerad JavaScript-programlogik.

1. Kopiera och klistra in funktionen **createContainer** och **readContainer** under funktionen **readDatabase** i app.js-filen. Funktionen **createContainer** skapar en ny container med ```containerId``` angivet från ```config```-objektet om det inte redan finns. Funktionen **readContainer** läser containerdefinitionen för att verifiera att containern finns.

   ```nodejs
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Kopiera och klistra in koden nedanför anropet till **readDatabase** för att köra funktionerna **createContainer** och **readContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Nu bör din kod i ```app.js``` se ut så här:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Leta upp filen ```app.js``` i terminalen och kör kommandot: 

   ```bash 
   node app.js
   ```

Grattis! Du har skapat en Azure Cosmos DB-container.

## <a id="CreateItem"></a>Steg 7: Skapa ett objekt

Ett objekt kan skapas genom att använda funktionen [create](/javascript/api/%40azure/cosmos/items) i klassen **Objekt**. När du använder SQL-API:et projiceras objekt som dokument, som är användardefinierat (godtyckligt), JSON-innehåll. Nu kan du infoga ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in funktionen **createFamilyItem** under funktionen **readContainer**. Funktionen **createFamilyItem** skapar objekten som innehåller JSON-data sparade i ```config```-objektet. Vi kontrollerar att det inte redan finns ett objekt med samma ID innan det skapas.

   ```nodejs
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Kopiera och klistra in koden nedanför anropet till **readContainer** för att köra funktionen **createFamilyItem**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Leta upp filen ```app.js``` i terminalen och kör kommandot: 

   ```bash 
   node app.js
   ```

Grattis! Du har skapat ett Azure Cosmos DB-objekt.


## <a id="Query"></a>Steg 8: Skicka frågor mot Azure Cosmos DB-resurser
Azure Cosmos DB stöder [komplexa frågor](sql-api-sql-query.md) mot JSON-dokument som lagras i varje container. Följande exempelkod visar en fråga som du kan köra mot dokumenten i containern.

1. Kopiera och klistra in funktionen **queryContainer** under funktionen **createFamilyItem** i app.js-filen. Azure Cosmos DB stöder SQL-liknande frågor som visas nedan. Mer information om hur du skapar komplexa frågor finns i [Query Playground](https://www.documentdb.com/sql/demo) och [frågedokumentationen](sql-api-sql-query.md).

   ```nodejs
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Kopiera och klistra in koden nedanför anropen till **createFamilyItem** för att köra funktionen **queryContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Leta upp filen ```app.js``` i terminalen och kör kommandot:

   ```bash 
   node app.js
   ```

Grattis! Du har nu frågat dina Azure Cosmos DB-objekt.

## <a id="ReplaceItem"></a>Steg 9: Ersätta ett objekt
Azure Cosmos DB stöder att ersätta innehållet i objekt.

1. Kopiera och klistra in funktionen **replaceFamilyItem** under funktionen **queryContainer** i app.js-filen. Observera att vi har ändrat egenskapen ”grade” för en underordnad till 6 från det tidigare värdet 5.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Kopiera och klistra in koden nedanför anropen till **queryContainer** för att köra funktionen **replaceFamilyItem**. Lägg även till koden för att anropa **queryContainer** igen för att verifiera att objektet har ändrats.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Leta upp filen ```app.js``` i terminalen och kör kommandot:

   ```bash 
   node app.js
   ```

Grattis! Du har ersatt ett Azure Cosmos DB-objekt.

## <a id="DeleteItem"></a>Steg 10: Ta bort ett objekt

Azure Cosmos DB har stöd för borttagning av JSON-objekt.

1. Kopiera och klistra in funktionen **deleteFamilyItem** under funktionen **replaceFamilyItem**.

   ```nodejs
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Kopiera och klistra in koden nedanför anropet till den andra **queryContainer** för att köra funktionen **deleteFamilyItem**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Leta upp filen ```app.js``` i terminalen och kör kommandot: 

   ```bash 
   node app.js
   ```

Grattis! Du har tagit bort ett Azure Cosmos DB-objekt.

## <a id="DeleteDatabase"></a>Steg 11: Ta bort databasen

Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. containrar och objekt).

1. Kopiera och klistra in funktionen **cleanup** under funktionen **deleteFamilyItem** för att ta bort databasen och alla underordnade resurser.

   ```nodejs
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Kopiera och klistra in koden under anropet till **deleteFamilyItem** för att köra funktionen **cleanup**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Steg 12: Kör Node.js-programmet i sin helhet!

Sammantaget bör koden se ut så här:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

Leta upp filen ```app.js``` i terminalen och kör kommandot: 

```bash 
node app.js
```

Du bör nu se utdata från din kom-igång-app. Dina utdata bör motsvara exempeltexten nedan.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

Grattis! Du har slutfört Node.js-självstudiekursen och har skapat ditt första Azure Cosmos DB-konsolprogram!

## <a id="GetSolution"></a>Hämta den fullständiga lösningen till Node.js-självstudien

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned koden kan du hämta den från [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

För att köra komma igång-lösningen som innehåller alla kod i den här artikeln behöver du följande:

* Ett [Azure Cosmos DB-konto][create-account].
* Lösningen [Komma igång](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) är tillgänglig på GitHub.

Installera modulen **@azure/cosmos** via npm. Ange följande kommando:

* ```npm install @azure/cosmos --save```

Sedan uppdaterar du värdena config.endpoint och config.primaryKey i filen ```config.js``` enligt beskrivningen i [Steg 3: Ange appkonfigurationer](#Config). 

Leta reda på fin ```app.js```-fil i terminalen och kör kommandot: 

```bash 
node app.js
```

Så där, nu är du på gång! 

## <a name="next-steps"></a>Nästa steg

* Vill du ha ett mer komplext Node.js-exempel? Mer information finns i [Skapa ett Node.js-webbprogram med Azure Cosmos DB](sql-api-nodejs-application.md).
* Lär dig hur du [övervakar ett Azure Cosmos DB-konto](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png