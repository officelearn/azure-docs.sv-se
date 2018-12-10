---
title: Självstudie om MongoDB, Angular och Node för Azure – del 5
description: Del 5 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB.
services: cosmos-db
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: jopapa
ms.custom: mvc
ms.openlocfilehash: ce4ad1c069d34e79c81b8335bf2d58cb95068b9a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866193"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Skapa en MongoDB-app med Angular och Azure Cosmos DB – del 5: Ansluta till Azure Cosmos DB med hjälp av Mongoose

Den här självstudiekursen i flera delar visar hur du skapar en Node.js-app med Express, Angular och ansluter den till [Azure Cosmos DB MongoDB API](mongodb-introduction.md)-konto.

Del 5 av självstudiekursen bygger vidare på [del 4](tutorial-develop-mongodb-nodejs-part4.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Ansluta till Azure Cosmos DB med hjälp av Mongoose
> * Hämta information om Cosmos DB-anslutningssträngen
> * Skapa hero-modellen
> * Skapa hero-tjänsten för att hämta hero-data
> * Köra appen lokalt

## <a name="video-walkthrough"></a>Videogenomgång

Du kan även titta på följande video för att snabbt lära dig stegen som beskrivs i det här dokumentet: 

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Nödvändiga komponenter

Utför stegen i [del 4](tutorial-develop-mongodb-nodejs-part4.md) av självstudiekursen innan du påbörjar den här delen.

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB med hjälp av Mongoose

1. Installera mongoose npm-modulen, ett API som används för att kommunicera med MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Skapa en ny fil i mappen **server** och ge den namnet **mongo.js**. Du lägger till anslutningsinformationen för Cosmos DB-kontot i den här filen.

3. Kopiera följande kod till **mongo.js**. Den här koden:

    * Kräver Mongoose.

    * Åsidosätter Mongo-löftet (promise) att använda det grundläggande löftet som är inbyggt i ES6/ES2015 och senare versioner.

    * Anropar en env-fil som du kan använda för att konfigurera vissa saker beroende på om du arbetar med mellanlagring, produktion eller utveckling. Du skapar den filen i nästa avsnitt.

    * Inkludera MongoDB-anslutningssträngen, som anges i env-filen.

    * Skapar en anslutningsfunktion som anropar Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. Skapa en mapp i Explorer-fönstret under **server** med namnet **environment** och skapa en ny fil i mappen **environment** med namnet **environment.js**.

5. Vi måste lägga till `dbName`, `key` och `cosmosPort` från mongo.js-filen. Kopiera därför följande kod till **environment.js**.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Hämta information om anslutningssträngen

1. Ändra värdet `port` till 10255 i **environment.js**. (Du hittar din Cosmos DB-port i Azure-portalen)

    ```javascript
    const port = 10255;
    ```

2. I **environment.js** ändrar du värdet på `accountName` till namnet på det Azure Cosmos DB-konto som du skapade i [steg 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Hämta primärnyckeln för Azure Cosmos DB-kontot genom att köra följande kommando i kommandoradsgränssnittet i terminalfönstret: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` är namnet på det Azure Cosmos DB-konto som du skapade i [steg 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Kopiera primärnyckeln till filen environment.js som värdet för `key`.

    Nu har din app all information den behöver för att ansluta till Azure Cosmos DB. Den här informationen kan även hämtas på portalen. Mer information finns i [Get the MongoDB connection string to customize](connect-mongodb-account.md#GetCustomConnection) (Hämta och anpassa MongoDB-anslutningssträngen). Username på portalen motsvarar dbName i environments.js. 

## <a name="create-a-hero-model"></a>Skapa en Hero-modell

1. I Explorer-fönstret skapar du filen **hero.model.js** under mappen **server**.

2. Kopiera följande kod till **hero.model.js**. Den här koden innehåller följande funktioner:

   * Kräver Mongoose.
   * Skapar ett nytt schema med ett id, ett namn och en fras.
   * Skapar en modell med hjälp av schemat.
   * Exporterar modellen. 
   * Ge samlingen namnet Heroes (i stället för Heros, som är standardnamnet för samlingen baserat på reglerna för namn i plural i Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Skapa en Hero-tjänst

1. I Explorer-fönstret skapar du filen **hero.service.js** under mappen **server**.

2. Kopiera följande kod till **hero.service.js**. Den här koden:

   * Hämtar modellen som du precis skapat.
   * Ansluter till databasen.
   * Skapar en docquery-variabel som använder metoden hero.find för att definiera en fråga som returnerar alla heroes-komponenter.
   * Kör en fråga med docquery.exec med ett löfte (promise) om att hämta en lista över alla heroes-komponenter, där svarsstatusen är 200. 
   * Om statusen är 500 returneras felmeddelandet
   * Eftersom vi använder moduler hämtas heroes-komponenterna. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Lägga till hero-tjänsten i routes.js

1. I Visual Studio Code i **routes.js** avkommenterar du `res.send`-funktionen som skickar hero-exempeldata och lägger till en rad för att anropa `heroService.getHeroes`-funktionen i stället.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. Infoga require för att kräva hero-tjänsten i **routes.js**:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. Uppdatera getHeroes-funktionen i **hero.service.js** så att den använder parametrarna `req` och `res`:

    ```javascript
    function getHeroes(req, res) {
    ```

    Nu ska vi ägna en minut åt att gå igenom anropskedjan. Först kommer vi till `index.js`, som konfigurerar nodservern. Och som du ser konfigureras och definieras dina vägar. Sedan kommunicerar routes.js-filen med hero-tjänsten och beordrar den att hämta dina funktioner, som getHeroes, och att skicka begäran och svaret. I vårt exempel hämtar hero.service.js modellen och ansluter till Mongo. Tjänsten kör sedan getHeroes när vi anropar funktionen och returnerar svaret 200. Händelsen sprids sedan vidare genom kedjan till det översta elementet. 

## <a name="run-the-app"></a>Kör appen

1. Nu ska vi köra appen igen. Spara alla ändringar i Visual Studio Code, klicka på **Felsök** ![Felsökningsikon i Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) till vänster och klicka sedan på **Starta felsökning** ![Felsökningsikon i Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Nu ska vi gå över till webbläsaren, öppna Utvecklarverktyg och fliken Nätverk samt gå till http://localhost:3000. Där finns vårt program.

    ![Nytt Azure Cosmos DB-konto på Azure-portalen](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Det finns inga heroes-komponenter lagrade i appen än, men i nästa steg i självstudiekursen ska vill lägga till Put-, Push- och Delete-funktionerna så att vi kan lägga till, uppdatera och ta bort heroes-komponenter från användargränssnittet med hjälp av Mongoose-anslutningar till vår Azure Cosmos DB-databas. 

## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Använt Mongoose-API:er för att ansluta heroes-appen till Azure Cosmos DB 
> * Lagt till getHeroes-funktionen till appen

Fortsätt till nästa del av självstudiekursen och lägg till Post-, Put- och Delete-funktioner till appen.

> [!div class="nextstepaction"]
> [Lägga till Post-, Put- och Delete-funktioner till appen](tutorial-develop-mongodb-nodejs-part6.md)
