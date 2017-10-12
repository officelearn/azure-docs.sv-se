---
title: "Självstudiekurs om MongoDB, Angular och Node för Azure – del 2 | Microsoft Docs"
description: "Del 2 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 48b7da57b23dbd16571c8fa179efd900cdcf21ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>Skapa en MongoDB-app med Angular och Azure Cosmos DB – del 2: Skapa en Node.js-baserad Express-app med Angular CLI 

Den här självstudiekursen i flera delar demonstrerar hur du skapar en ny [MongoDB API](mongodb-introduction.md)-app skriven i Node.js med Express, Angular och din Azure Cosmos DB-databas.

Del 2 av kursen bygger vidare på [introduktionen](tutorial-develop-mongodb-nodejs.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Installera Angular CLI och TypeScript
> * Skapa ett nytt projekt med hjälp av Angular
> * Utveckla appen med hjälp av Express-ramverket
> * Testa appen i Postman

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Krav

Titta på [introduktionsvideon](tutorial-develop-mongodb-nodejs.md) innan du påbörjar den här delen av självstudiekursen.

Den här kursen kräver även följande: 
* [Node.js](https://nodejs.org/) version 8.4.0 eller senare.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) eller valfri kodredigerare.

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Installera Angular CLI och TypeScript

1. Öppna en kommandotolk i Windows eller ett terminalfönster på Mac och installera Angular CLI.

    ```bash
    npm install -g @angular/cli
    ```

2. Installera TypeScript genom att skriva följande kommando i kommandotolken. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Skapa ett nytt projekt med hjälp av Angular CLI

1. I kommandotolken byter du till den mapp där du vill skapa det nya projektet och kör följande kommando. Det här kommandot skapar en ny mapp och ett projekt med namnet angular-cosmosdb och installerar Angular-komponenterna som krävs för en ny app. Kommandot installerar också källkoden i mappen src/client (-sd src/client), använder den minsta installationen (--minimal) och anger att projektet använder Sass (en CSS-liknande syntax med flaggan --style scss).

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. När kommandot har slutförts byter du katalog till mappen src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Öppna sedan mappen i Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Utveckla appen med hjälp av Express-ramverket

1. Högerklicka på mappen **src** i **Explorer**-fönstret i Visual Studio Code, klicka på **Ny mapp** och ge den nya mappen namnet *server*.

2. Högerklicka på mappen **server** i **Explorer**-fönstret, klicka på **Ny fil** och ge den nya filen namnet *index.js*.

3. Tillbaka i kommandotolken installerar du body-parser genom att köra följande kommando. På så sätt kan appen parsa JSON-data som skickas via API:erna.

    ```bash
    npm i express body-parser --save
    ```

4. I Visual Studio Code kopierar du följande kod till filen index.js. Den här koden:
    * Refererar till Express
    * Hämtar body-parser för läsning av JSON-data i innehållet i begäranden
    * Använder en inbyggd funktion kallad path
    * Anger rotvariabler så att det blir lättare att se var koden finns
    * Konfigurerar en port
    * Sätter igång Express
    * Anger hur appen ska använda mellanprogrammet som vi ska använda för att leverera innehåll till servern
    * Tillhandahåller allt som finns i dist-mappen, som är det statiska innehållet
    * Visar programmet, och visar index.html för GET-begäranden som inte hittas på servern (för djuplänkar)
    * Startar servern med app.listen
    * Använder en arow-funktion för att logga att porten är aktiv
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Högerklicka på mappen **server** i **Explorer**-fönstret i Visual Studio Code och klicka på **Ny fil**. Ge den nya filen namnet *routes.js*. 

6. Kopiera följande kod till **routes.js**. Den här koden:
   * Refererar till Express-routern
   * Hämtar heroes-komponenterna
   * Skickar tillbaka JSON för en definierad hero-komponent

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Sparar alla ändrade filer 

8. Klicka på knappen **Felsök** ![Felsökningsikon i Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png) i Visual Studio Code, klicka på kugghjulsknappen ![Kugghjulsknapp i Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png) och välj **Node.js** för att skapa en konfiguration.

   Den nya launch.json-filen öppnas i Visual Studio Code.

8. Ändra `"program": "${file}"` till `"program": "${workspaceRoot}/src/server/index.js"` på rad 11 i launch.json och spara filen.

9. Kör appen genom att klicka på knappen **Starta felsökning** ![Felsökningsikon i Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png).

    Appen bör köras utan fel.

## <a name="use-postman-to-test-the-app"></a>Testa appen med hjälp av Postman

1. Öppna Postman och ange `http://localhost:3000/api/heroes` i rutan GET. 

2. Klicka på **Skicka** så returneras json-svaret från appen. 

    Svaret visar att appen är aktiv och körs lokalt. 

    ![Postman som visar begäran och svaret](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Skapat ett Node.js-projekt med hjälp av Angular CLI
> * Testat appen med hjälp av Postman

Fortsätt till nästa del av självstudiekursen och skapa användargränssnittet.

> [!div class="nextstepaction"]
> [Skapa användargränssnittet med Angular](tutorial-develop-mongodb-nodejs-part3.md)
