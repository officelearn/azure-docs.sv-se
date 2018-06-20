---
title: Skapa en Node.js-webbapp för Azure Cosmos DB | Microsoft Docs
description: I den här självstudiekursen för Node.js lär du dig hur du använder Microsoft Azure Cosmos DB för att lagra och komma åt data från ett Node.js Express-webbprogram på Azure Websites.
keywords: Application development, database tutorial, learn node.js, node.js tutorial
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: d18e6dd9464ef103157a8532215fa797ab282437
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797489"
---
# <a name="_Toc395783175"></a>Skapa ett Node.js-webbprogram med Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Den här självstudiekursen om Node.js beskriver hur du kan använda Azure Cosmos DB och SQL API för lagring av och åtkomst till data från ett Node.js Express-program på Azure Websites. Du bygger ett enkelt webbaserat aktivitetshanteringsprogram, en ToDo-app, där du kan skapa, hämta och slutföra aktiviteter. Uppgifterna lagras som JSON-dokument i Azure Cosmos DB. Den här självstudien vägleder dig genom skapandet och distributionen av appen och förklarar vad som händer i varje kodfragment.

![Skärmdump av programmet My Todo List som skapas genom stegen i den här självstudien om Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Har du inte tid att gå igenom självstudien, men vill ha hela lösningen? Inga problem, du kan hämta den fullständiga exempellösningen från [GitHub][GitHub]. Läs bara [Viktigt](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md)-filen för instruktioner om hur du kör appen.

## <a name="_Toc395783176"></a>Förhandskrav
> [!TIP]
> Den här Node.js-självstudien förutsätter att du har tidigare erfarenhet av Node.js och Azure Websites.
> 
> 

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version v0.10.29 eller högre. Vi rekommenderar Node.js 6.10 eller högre.
* [Express generator](http://www.expressjs.com/starter/generator.html) (kan installeras via `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Steg 1: Skapa ett Azure Cosmos DB-databaskonto
Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett konto eller om du använder Azure Cosmos DB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa ett nytt Node.js-program](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Steg 2: Skapa ett nytt Node.js-program
Nu ska vi skapa ett grundläggande Hello World Node.js-projekt med [Express](http://expressjs.com/)-ramverket.

1. Öppna din favoritterminal, till exempel Node.js-kommandotolken.
2. Navigera till den katalog där du vill lagra det nya programmet.
3. Skapa ett nytt program kallat **todo** med hjälp av Express Generator.
   
        express todo
4. Öppna din nya **todo**-katalog och installera beroenden.
   
        cd todo
        npm install
5. Kör det nya programmet.
   
        npm start
6. Du kan visa det nya programmet genom att öppna [http://localhost:3000](http://localhost:3000) i webbläsaren.
   
    ![Lär dig använda Node.js – Skärmdump av programmet Hello World i ett webbläsarfönster](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    Om du vill stoppa programmet trycker du på CTRL+C i terminalfönstret och sedan (endast för Windows-datorer) på **y** för att avbryta batch-jobbet.

## <a name="_Toc395783179"></a>Steg 3: Installera ytterligare moduler
Filen **package.json** är en av filerna som skapas i projektets rot. Den här filen innehåller en lista över ytterligare moduler som krävs för Node.js-programmet. Senare, när du distribuerar programmet till Azure Websites, används den här filen för att avgöra vilka moduler som behöver installeras på Azure som stöd för ditt program. Vi behöver installera två paket till för den här självstudien.

1. Gå tillbaka till terminalen och installera modulen **async** via npm.
   
        npm install async --save
2. Installera modulen **documentdb** via npm. Det här är modulen där Azure Cosmos DB-magin händer.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Steg 4: Använda Azure Cosmos DB-tjänsten i ett nodprogram
Nu när vi har slutfört den första installationen och konfigurationen är det dags att ta itu med vårt verkliga syfte: att skriva kod med Azure Cosmos DB.

### <a name="create-the-model"></a>Skapa modellen
1. Skapa en ny katalog i projektkatalogen med namnet **models**, i samma katalog som package.json-filen.
2. I katalogen **models** skapar du en ny fil med namnet **task-model.js**. Den här filen innehåller modellen för de aktiviteter som skapats av vårt program.
3. I samma **models**-katalog skapar du ytterligare en ny fil med namnet **cosmosdb-manager.js**. Den här filen innehåller användbar och återanvändbar kod som vi ska använda i programmet. 
4. Kopiera följande kod till **cosmosdb-manager.js**
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Spara och stäng filen **cosmosdb-manager.js**.
6. I början av filen **task-model.js** lägger du till följande kod för att referera till **DocumentDBClient** och **cosmosdb-manager.js** som vi skapade ovan: 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. Sedan lägger du till kod för att definiera och exportera aktivitetsobjektet. Den ansvarar för att initiera vårt aktivitetsobjekt och konfigurera databasen och dokumentsamlingen som vi ska använda.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. Lägg sedan till följande kod för att definiera ytterligare metoder för aktivitetsobjektet, som gör att det går att interagera med data som lagras i Azure Cosmos DB.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Spara och stäng filen **task-model.js**. 

### <a name="create-the-controller"></a>Skapa styrningen
1. I projektets **routes**-katalog skapar du en ny fil med namnet **tasklist.js**. 
2. Lägg till följande kod i **tasklist.js**. Den läser in DocumentDBClient och async-moduler som används av **tasklist.js**. Detta definieras även i funktionen **TaskList**, som mottar en instans av **Task**-objektet som vi definierade tidigare:
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Fortsätt att lägga till kod i filen **tasklist.js** genom att lägga till metoderna **showTasks, addTask** och **completeTasks**:
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Spara och stäng filen **tasklist.js**.

### <a name="add-configjs"></a>Lägg till config.js
1. Skapa en ny fil med namnet **config.js** i projektkatalogen.
2. Lägg till följande i **config.js**. Det definierar konfigurationsinställningar och värden som behövs i appen.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. I filen **config.js** uppdaterar du värdet för HOST och AUTH_KEY med värdena på sidan Nycklar för ditt Azure Cosmos DB-konto på [Microsoft Azure-portalen](https://portal.azure.com).
4. Spara och stäng filen **config.js**.

### <a name="modify-appjs"></a>Ändra app.js
1. Öppna filen **app.js** i projektkatalogen. Den här filen skapades tidigare när Express-webbappen skapades.
2. Lägg till följande kod högst upp i **app.js**:
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Den här koden definierar vilken konfigurationsfil som ska användas och läser sedan värden från denna fil till några variabler som vi snart ska använda.
4. Ersätt följande två rader i filen **app.js**:
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    med följande utdrag:
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Dessa rader definierar en ny instans av **TaskModel**-objektet med en ny anslutning till Azure Cosmos DB (med de värden som lästs in från **config.js**), initierar aktivitetsobjektet och binder formuläråtgärder till metoder i **TaskList**-styrenheten. 
6. Avsluta med att spara och stänga filen **app.js**. Vi är nästan klara.

## <a name="_Toc395783181"></a>Steg 5: Skapa ett användargränssnitt
Nu är det dags att skapa användargränssnittet, så att användaren faktiskt kan samverka med vår app. Express-appen som vi skapade använder **Jade** som visningsmotor. Mer information om Jade finns på [http://jade-lang.com/](http://jade-lang.com/).

1. Filen **layout.jade** i katalogen **views** används som en global mall för andra **.jade**-filer. I det här steget ändrar du den så att den använder [Twitter Bootstrap](https://github.com/twbs/bootstrap), vilket är en verktygslåda som gör det enkelt att utforma en snygg webbplats. 
2. Öppna filen **layout.jade** i mappen **views** och ersätt innehållet med följande:

    ```
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

    Det säger åt **Jade**-motorn att rendera en del HTML för vårt program och skapar ett **block** kallat **content** där vi kan tillhandahålla layout för våra innehållssidor.

    Spara och stäng filen **layout.jade**.

3. Öppna nu filen **index.jade**, den vy som ska användas av vårt program, och ersätt innehållet i filen med följande:
   
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
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Det utökar layouten och ger innehåll för platshållaren **content** som vi såg i filen **layout.jade**.
   
I den här layouten skapade vi två HTML-formulär.

Det första formuläret innehåller en tabell för våra data och en knapp som gör att vi kan uppdatera objekt genom att publicera till metoden **/completetask** i styrningen.
    
Det andra formuläret innehåller två inmatningsfält och en knapp som gör att vi kan skapa ett nytt objekt genom att publicera till metoden **/addtask** i styrningen.

Det här ska vara allt som behövs för att appen ska fungera.

## <a name="_Toc395783181"></a>Steg 6: Kör ditt program lokalt
1. Om du vill testa programmet på din lokala dator kör du `npm start` i terminalen för att starta programmet. Uppdatera sedan din [http://localhost:3000](http://localhost:3000)-webbläsarsida. Sidan ska nu se ut som på bilden nedan:
   
    ![Skärmdump av programmet MyTodo List i ett webbläsarfönster](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Om du får ett felmeddelande om indraget i layout.jade-filen eller index.jade-filen, säkerställ att de två första raderna i båda filerna är vänsterjusterade, utan blanksteg. Om det finns blanksteg före de två första raderna, ta bort dem, spara filerna och uppdatera sedan webbläsarfönstret. 

2. Använd fälten Objekt, Objektnamn och Kategori och klicka sedan på **Lägg till objekt**. När du gör det skapas ett dokument i Azure Cosmos DB med dessa egenskaper. 
3. Sidan bör uppdateras och visa det nya objektet i ToDo-listan.
   
    ![Skärmdump av programmet med ett nytt objekt i ToDo-listan](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Du slutför en aktivitet genom att markera kryssrutan i kolumnen Complete och sedan klicka på **Update tasks**. Då uppdateras dokumentet du redan har skapat och tas bort från vyn.

5. För att stoppa programmet trycker du på CTRL+C i terminalfönstret och klickar sedan på **Y** för att avbryta batch-jobbet.

## <a name="_Toc395783182"></a>Steg 7: Distribuera ditt programutvecklingsprojekt till Azure Websites
1. Om du inte redan gjort det aktiverar du en git-databas för Azure-webbplatsen. Information om hur du gör det finns i artikeln [Lokal Git-distribuering på Azure App Service](../app-service/app-service-deploy-local-git.md).
2. Lägg till Azure-webbplatsen som en fjärransluten git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Distribuera genom att pusha till fjärranslutningen.
   
        git push azure master
4. Efter några sekunder har git publicerat din webbapp och öppnar en webbläsare där du kan se ditt arbete köras i Azure!

    Grattis! Du har skapat ditt första Node.js Express-webbprogram med Azure Cosmos DB och publicerat det på Azure Websites.

    Om du vill hämta eller referera till det färdiga referensprogrammet för den här självstudien kan det hämtas från [GitHub][GitHub].

## <a name="_Toc395637775"></a>Nästa steg

* Vill du testa skalning och prestanda med Azure Cosmos DB? Mer information finns i avsnittet om hur du [testar prestanda och skalning med Azure Cosmos DB](performance-testing.md)
* Lär dig hur du [övervakar ett Azure Cosmos DB-konto](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Utforska [Azure Cosmos DB-dokumentationen](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

