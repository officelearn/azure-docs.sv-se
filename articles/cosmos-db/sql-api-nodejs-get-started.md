---
title: Självstudie om Node.js för SQL-API:et för Azure Cosmos DB
description: En Node.js-självstudiekurs som demonstrerar hur du ansluter och frågar Azure Cosmos DB med SQL-API:et
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: c758b3f56d8935b8d0d7873df41a965be1281044
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407585"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Självstudie: Skapa en Node.js-konsolapp med JavaScript SDK för att hantera Azure Cosmos DB SQL API-data

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Som utvecklare kan du ha program som använder NoSQL-dokumentdata. Du kan använda ett SQL API-konto i Azure Cosmos DB till att lagra och komma åt dessa dokumentdata. Den här självstudien visar hur du skapar ett Node.js-konsolprogram som skapar Azure Cosmos DB-resurser och skickar frågor mot dem.

I den här kursen ska du:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos DB-konto.
> * Konfigurera ditt program.
> * Skapa en databas.
> * Skapa en container.
> * Lägga till objekt i containern.
> * Utföra grundläggande åtgärder på objekten, containern och databasen.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Se till att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 eller högre.

## <a name="create-azure-cosmos-db-account"></a>Skapa Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [konfigurationen av Node.js-programmet](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Konfigurera Node.js-programmet

Innan du börjar skriva kod för att skapa programmet kan du skapa ramverket för din app. Kör följande steg för att konfigurera ditt Node.js-program som har ramverkskoden:

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

## <a id="Config"></a>Konfigurera appens konfigurationer

Nu när din app finns behöver du se till att den kan kommunicera med Azure Cosmos DB. Genom att uppdatera några konfigurationsinställningar, vilket visas i följande steg, kan du konfigurera din app att kommunicera med Azure Cosmos DB:

1. Öppna ```config.js``` i valfri textredigerare.

1. Kopiera och klistra in kodfragmentet nedan och ange egenskaper för ```config.endpoint``` och ```config.primaryKey``` till slutpunkts-URI och primärnyckel i Azure Cosmos DB. Båda dessa konfigurationer finns i [Azure Portal](https://portal.azure.com).

   ![Hämta nycklar från Azure-portalen, skärmbild][keys]

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Kopiera och klistra in data från ```database```, ```container``` och ```items``` till ditt ```config```-objekt nedan där du anger ```config.endpoint```- och ```config.primaryKey```-egenskaper. Om du redan har data som du vill lagra i databasen kan du använda datamigreringsverktyget i Azure Cosmos DB i stället för att definiera data här.

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

   JavaScript SDK använder de generiska termerna *container* och *objekt*. En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. 

1. Exportera slutligen ```config```-objektet, så att du kan referera till det i filen ```app.js```.

   ```nodejs
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Ansluta till ett Azure Cosmos DB-konto

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

## <a name="create-a-database"></a>Skapa en databas

1. Kopiera och klistra in koden nedan för att ange databasens ID och containerns ID. Det är via dessa ID:n som Azure Cosmos DB-klienten hittar rätt databas och container.

   ```nodejs
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   En databas kan skapas med hjälp av antingen `createIfNotExists` eller create-funktionen (skapa) i **Databases**-klassen (Databaser). En databas är en logisk container med objekt som är partitionerade över containrar. 

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

## <a id="CreateContainer"></a>Skapa en container

Sedan skapar du en container i Azure Cosmos DB-kontot så att du kan lagra och köra frågor mot data. 

> [!WARNING]
Att skapa en container påverkar prissättningen. Besök [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/) så att du vet vad du kan förvänta dig.

En container kan skapas med hjälp av antingen `createIfNotExists` eller create-funktionen (skapa) i **Containers**-klassen (Containrar).  En container består av objekt (som i fallet med SQL-API:et är JSON-dokument) och associerad JavaScript-programlogik.

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

## <a id="CreateItem"></a>Skapa ett objekt

Ett objekt kan skapas med hjälp av funktionen create-funktionen (skapa) i klassen **Items** (Objekt). När du använder SQL-API:et projiceras objekt som dokument, som är användardefinierat (godtyckligt) JSON-innehåll. Nu kan du infoga ett objekt i Azure Cosmos DB.

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


## <a id="Query"></a>Köra frågor mot Azure Cosmos DB-resurser
Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje container. Följande exempelkod visar en fråga som du kan köra mot dokumenten i containern.

1. Kopiera och klistra in funktionen **queryContainer** under funktionen **createFamilyItem** i app.js-filen. Azure Cosmos DB stöder SQL-liknande frågor som visas nedan.

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


## <a id="ReplaceItem"></a>Ersätta ett objekt
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


## <a id="DeleteItem"></a>Ta bort ett objekt

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


## <a id="DeleteDatabase"></a>Ta bort databasen

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

## <a id="Run"></a>Köra Node.js-programmet

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

## <a id="GetSolution"></a>Hämta den fullständiga lösningen till Node.js-självstudien 

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned koden kan du hämta den från [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

För att köra den komma igång-lösning som innehåller all kod i den här artikeln behöver du följande: 

* Ett [Azure Cosmos DB-konto][create-account]. 
* Lösningen [Komma igång](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) är tillgänglig på GitHub. 

Installera modulen **@azure/cosmos** via npm. Ange följande kommando: 

* ```npm install @azure/cosmos --save``` 

Sedan uppdaterar du värdena config.endpoint och config.primaryKey i filen ```config.js``` enligt beskrivningen i [Steg 3: Konfigurera appens konfigurationer](#Config).  

Leta reda på fin ```app.js```-fil i terminalen och kör kommandot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos DB-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta markerar du den resursgrupp som du använde för Azure Cosmos DB-kontot. Välj sedan **Ta bort** och bekräfta namnet på den resursgrupp som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka ett Azure Cosmos DB-konto](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
