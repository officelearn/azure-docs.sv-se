---
title: Skapa en Node.js-webbapp med JavaScript SDK för att hantera Azure Cosmos DB SQL API-data | Microsoft Docs
description: I den här självstudiekursen för Node.js lär du dig hur du använder Microsoft Azure Cosmos DB för att lagra och komma åt data från ett Node.js Express-webbprogram på Azure Websites.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 0c99b7d1ef774e20a49564db269555bab95789a3
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741800"
---
# <a name="tutorial-build-a-nodejs-web-app-using-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Självstudie: Skapa en Node.js-webbapp med JavaScript SDK för att hantera Azure Cosmos DB SQL API-data

> [!div class="op_single_selector"]
> * [NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Den här självstudien om Node.js beskriver hur du lagrar och kommer åt data från ett Azure Cosmos DB SQL API-konto genom att använda ett Node.js Express-program som hanteras på Azure Websites. I den här självstudien skapar du ett webbaserat program (att göra-app) där du kan skapa, hämta och slutföra uppgifter. Uppgifterna lagras som JSON-dokument i Azure Cosmos DB. 

Den här självstudien visar hur du skapar ett Azure Cosmos DB SQL API-konto med hjälp av Azure-portalen. Du skapar och kör sedan en webbapp som bygger på Node.js SDK för att skapa en databas och en container samt lägga till objekt i containern. Den här självstudien använder JavaScript SDK version 2.0.

Du kan även hämta det färdiga exemplet från [GitHub][GitHub] och läsa [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md)-filen för instruktioner om hur du kör appen.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa ett nytt Node.js-program
> * Ansluta programmet till Azure Cosmos DB
> * Köra och distribuera programmet till Azure

## <a name="_Toc395783176"></a>Förhandskrav

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande resurser:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version 6.10 eller senare.
* [Express generator](http://www.expressjs.com/starter/generator.html) (du kan installera Express via `npm install express-generator -g`)
* Installera [Git][Git] på den lokala arbetsstationen.

## <a name="_Toc395637761"></a>Steg 1: Skapa ett Azure Cosmos DB-konto
Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett konto eller om du använder Azure Cosmos DB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa ett nytt Node.js-program](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Steg 2: Skapa ett nytt Node.js-program
Nu ska vi skapa ett grundläggande Hello World Node.js-projekt med [Express](http://expressjs.com/)-ramverket.

1. Öppna din favoritterminal, till exempel Node.js-kommandotolken.

1. Navigera till den katalog där du vill lagra det nya programmet.

1. Skapa ett nytt program kallat **todo** med hjälp av Express Generator.

   ```bash
   express todo
   ```

1. Öppna **todo**-katalogen och installera beroenden.

   ```bash
   cd todo
   npm install
   ```

1. Kör det nya programmet.

   ```bash
   npm start
   ```

1. Du kan visa det nya programmet genom att öppna [http://localhost:3000](http://localhost:3000) i webbläsaren.
   
   ![Lär dig använda Node.js – Skärmdump av programmet Hello World i ett webbläsarfönster](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Stoppa programmet genom att trycka på CTRL+C i terminalfönstret och välja **Y** för att avbryta batchjobbet.

## <a name="_Toc395783179"></a>Steg 3: Installera de nödvändiga modulerna

Filen **package.json** är en av filerna som skapas i projektets rot. Den här filen innehåller en lista över ytterligare moduler som krävs för Node.js-programmet. När du distribuerar det här programmet till Azure används den här filen för att avgöra vilka moduler som ska installeras på Azure för att stödja ditt program. Installera ytterligare två paket för den här självstudien.

1. Öppna terminalen och installera modulen **async** via npm.

   ```bash
   npm install async --save
   ```

2. Installera modulen **@azure/cosmos** via npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Steg 4: Ansluta Node.js-programmet till Azure Cosmos DB
Nu när du har slutfört den första installationen och konfigurationen kommer du att skriva kod som krävs av att göra-programmet för att kommunicera med Azure Cosmos DB.

### <a name="create-the-model"></a>Skapa modellen
1. Vid roten i projektkatalogen skapar du en ny katalog med namnet **models**  

2. I katalogen **models** skapar du en ny fil med namnet **taskDao.js**. Den här filen innehåller den kod som krävs för att skapa databasen och containern, och definierar metoder för att läsa, uppdatera, skapa och hitta uppgifter i Azure Cosmos DB. 

3. Kopiera följande kod till filen **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Spara och stäng filen **taskDao.js**.  

### <a name="create-the-controller"></a>Skapa styrningen

1. I projektets **routes**-katalog skapar du en ny fil med namnet **tasklist.js**.  

2. Lägg till följande kod i **tasklist.js**. Den här koden läser in modulerna CosmosClient och async, som används av **tasklist.js**. Koden definierar även klassen **TaskList**, som skickas som en instans av det **TaskDao**-objekt som vi definierade tidigare:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Spara och stäng filen **tasklist.js**.

### <a name="add-configjs"></a>Lägg till config.js

1. Vid roten av projektkatalogen skapar du en ny fil med namnet **config.js**. 

2. Lägg till följande kod i filen **config.js**. Den här koden definierar konfigurationsinställningar och värden som behövs i appen.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. I filen **config.js** uppdaterar du värdet för HOST och AUTH_KEY med värdena på sidan Nycklar för ditt Azure Cosmos DB-konto på [Microsoft Azure-portalen](https://portal.azure.com). 

4. Spara och stäng filen **config.js**.

### <a name="modify-appjs"></a>Ändra app.js

1. Öppna filen **app.js** i projektkatalogen. Den här filen skapades tidigare när Express-webbappen skapades.  

2. Lägg till följande kod i filen **app.js**. Den här koden definierar den konfigurationsfil som ska användas och läser in värdena till några variabler som du använder i kommande avsnitt. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Avsluta med att spara och stänga filen **app.js**.

## <a name="_Toc395783181"></a>Steg 5: Skapa ett användargränssnitt

Nu skapar vi användargränssnittet så att användare kan interagera med programmet. Det Express-program som vi skapade i föregående avsnitt använder **Jade** som visningsmotor. Mer information om Jade finns i avsnittet om [språket Jade](http://jade-lang.com/).

1. Filen **layout.jade** i katalogen **views** används som en global mall för andra **.jade**-filer. I det här steget ändrar du den så att den använder [Twitter Bootstrap](https://github.com/twbs/bootstrap), vilket är en verktygslåda som används för att utforma webbplatser.  

2. Öppna filen **layout.jade** i mappen **views** och ersätt innehållet med följande kod:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Koden instruerar **Jade**-motorn att rendera en del HTML för vårt program och skapar ett **block** kallat **content** där vi kan tillhandahålla layout för våra innehållssidor. Spara och stäng filen **layout.jade**.

3. Öppna nu filen **index.jade**, den vy som ska användas av vårt program, och ersätt innehållet i filen med följande kod:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Den här koden utökar layouten och ger innehåll för platshållaren **content** som vi såg i filen **layout.jade**. I den här layouten skapade vi två HTML-formulär.

Det första formuläret innehåller en tabell för dina data och en knapp som gör att du kan uppdatera objekt genom att publicera till metoden **/completeTask** i kontrollanten.
    
Det andra formuläret innehåller två inmatningsfält och en knapp som gör att du kan skapa ett nytt objekt genom att publicera till metoden **/addtask** i kontrollanten. Det är allt vi behöver för att programmet ska fungera.

## <a name="_Toc395783181"></a>Steg 6: Kör ditt program lokalt

1. Om du vill testa programmet på din lokala dator kör du `npm start` i terminalen för att starta programmet. Uppdatera sedan [http://localhost:3000](http://localhost:3000)-webbläsarsidan. Sidan bör ser ut så som på följande skärmbild:
   
    ![Skärmdump av programmet MyTodo List i ett webbläsarfönster](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Om du får ett felmeddelande om indraget i layout.jade-filen eller index.jade-filen, säkerställ att de två första raderna i båda filerna är vänsterjusterade, utan blanksteg. Om det finns blanksteg före de två första raderna, ta bort dem, spara filerna och uppdatera sedan webbläsarfönstret. 

2. Använd fälten Objekt, Objektnamn och Kategori och välj sedan **Lägg till objekt**. Då skapas ett dokument i Azure Cosmos DB med dessa egenskaper. 

3. Sidan bör uppdateras och visa det nya objektet i ToDo-listan.
   
    ![Skärmdump av programmet med ett nytt objekt i ToDo-listan](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Du slutför en aktivitet genom att markera kryssrutan i kolumnen Slutför och sedan klicka på **Uppdatera uppgifter**. Då uppdateras det dokument som du redan har skapat och tas bort från vyn.

5. För att stoppa programmet trycker du på CTRL+C i terminalfönstret och väljer sedan **Y** för att avbryta batch-jobbet.

## <a name="_Toc395783182"></a>Steg 7: Distribuera appen till Azure Websites

1. Om du inte redan gjort det aktiverar du en git-lagringsplats för Azure-webbplatsen. Instruktioner om hur du aktiverar en git-lagringsplats finns i ämnet [Lokal Git-distribution till Azure Apptjänst](../app-service/app-service-deploy-local-git.md).

2. Lägg till Azure-webbplatsen som en fjärransluten git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Distribuera programmet genom att push-överföra det till fjärranslutningen.
   
   ```bash
   git push azure master
   ```

4. Efter några sekunder publiceras webbappen och startas i en webbläsare.

Om du vill ladda ned eller se det färdiga referensprogrammet för den här självstudien kan du ladda ned det från [GitHub][GitHub].

## <a name="_Toc395637775"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Node.js-webbapp med hjälp av JavaScript SDK för att hantera Azure Cosmos DB SQL API-data. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Bygga mobilappar med Xamarin och Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

