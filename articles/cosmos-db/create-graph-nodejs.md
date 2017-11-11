---
title: Skapa en Node.js-app i Azure Cosmos DB med Graph API | Microsoft Docs
description: "Presenterar ett Node.js-kodexempel som du kan använda för att ansluta till och ställa frågor mot Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: 361f63141a8bf3f901eee6c93742f1a7fdc4348f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Skapa en Node.js-app med Graph API

Azure Cosmos-DB är globalt distribuerad multimodel databastjänst från Microsoft. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här artikeln för Snabbstart visar hur du skapar ett Azure DB som Cosmos-konto för Graph API (förhandsgranskning), databasen och diagram med hjälp av Azure portal. Sedan skapar och kör du en konsolapp med drivrutinen [Gremlin Node.js](https://www.npmjs.com/package/gremlin) (öppen källkod).

## <a name="prerequisites"></a>Krav

Innan du kan köra det här exemplet måste du uppfylla följande krav:
* [Node.js](https://nodejs.org/en/)-version v0.10.29 eller senare
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Graph API-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash och ändra (via kommandot `cd`) till en arbetskatalog.

2. Kör följande kommando för att klona lagringsplatsen exempel: 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna den `app.js` fil och du ser följande rader med kod. 

* Gremlin-klienten skapas.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  Konfigurationerna som finns i `config.js`, som vi redigera i den [efter avsnittet](#update-your-connection-string).

* En serie funktioner har definierats för att köra olika Gremlin åtgärder. Detta är en av dem:

    ```nodejs
    function addVertex1(callback)
    {
        console.log('Running Add Vertex1'); 
        client.execute("g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('userid', 1)", { }, (err, results) => {
          if (err) callback(console.error(err));
          console.log("Result: %s\n", JSON.stringify(results));
          callback(null)
        });
    }
    ```

* Varje funktion kör en `client.execute` metod med en Gremlin frågesträngparametern. Här är ett exempel på hur `g.V().count()` körs:

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

* I slutet av filen, alla metoder sedan anropas med hjälp av den `async.waterfall()` metoden. Detta ska köra dem efter varandra:

    ```nodejs
    try{
        async.waterfall([
            dropGraph,
            addVertex1,
            addVertex2,
            addEdge,
            countVertices
            ], finish);
    } catch(err) {
        console.log(err)
    }
    ```


## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

1. Uppdatera filen config.js. 

2. I config.js, fyller du i den `config.endpoint` nyckeln med den **Gremlin URI** värdet från den **översikt** sidan i Azure-portalen. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-graph-nodejs/gremlin-uri.png)

   Om den **Gremlin URI** värdet är tomt, kan du skapa värdet från den **nycklar** sida i portalen. Använd den **URI** värdet tar du bort https:// och ändra dokument till diagram.

   Gremlin-slutpunkten måste vara värddatorns namn utan port och protocol-nummer, till exempel `mygraphdb.graphs.azure.com` (inte `https://mygraphdb.graphs.azure.com` eller `mygraphdb.graphs.azure.com:433`).

3. I config.js, fyller du i config.primaryKey värdet med den **primärnyckel** värdet från den **nycklar** sidan i Azure-portalen. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Azure portal ”nycklar”, blad](./media/create-graph-nodejs/keys.png)

4. Ange databasens namn och grafvärdet (behållaren) för config.database och config.collection. 

Här är ett exempel på hur din färdiga config.js-fil ska se ut:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Kör konsolappen

1. Öppna ett terminalfönster och ändra (via kommandot `cd`) till installationskatalogen för package.json-fil som ingår i projektet.

2. Kör `npm install` att installera nödvändiga npm-moduler, inklusive `gremlin`.

3. Kör `node app.js` i en terminal för att starta nodprogrammet.

## <a name="browse-with-data-explorer"></a>Bläddra med datautforskaren

Nu kan du gå tillbaka till datautforskaren i Azure Portal och bläddra bland, ställa frågor mot och arbeta med dina nya grafdata.

Den nya databasen visas i fönstret **Graphs** (Diagram) i Datautforskaren. Expandera databasen, följt av samlingen, och välj sedan **diagram**.

Data som genereras av sample-appen visas i nästa ruta inom den **diagram** fliken när du väljer **Använd Filter**.

Prova att slutföra `g.V()` med `.has('firstName', 'Thomas')` för att testa filtret. Observera att värdet är skiftlägeskänsliga.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Rensa dina resurser

Om du inte planerar att fortsätta använda den här appen kan du ta bort alla resurser som du skapade i den här artikeln genom att göra följande: 

1. Välj i Azure-portalen på den vänstra navigeringsmenyn **resursgrupper**. Välj sedan namnet på den resurs som du skapade. 

2. Välj **Ta bort** på din resursgruppssida. Skriv namnet på resursen som ska tas bort och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar ett konto i Azure Cosmos DB, skapa ett diagram med hjälp av Data Explorer och kör en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)
