---
title: "Lär dig använda Node.js – självstudie om DocumentDB Node.js | Microsoft Docs"
description: "Lär dig Node.js! I den här självstudien får du lära dig använda Microsoft Azure DocumentDB för att lagra och komma åt data från Node.js Express-webbappar på Azure Websites."
keywords: "Programutveckling, självstudier för databas, läs om node.js, självstudier för node.js, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: nodejs
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 12/16/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 284bf43ceae0c42b88d6ea3fe8a2e68e7530e5fe
ms.lasthandoff: 03/08/2017


---
# <a name="_Toc395783175"></a>Skapa ett Node.js webbprogram med DocumentDB
> [!div class="op_single_selector"]
> * [NET](documentdb-dotnet-application.md)
> * [.NET för MongoDB](documentdb-mongodb-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

I den här självstudien om Node.js beskrivs hur Azure DocumentDB kan användas för lagring av och åtkomst till data från ett Node.js Express-program på Azure Websites. Du bygger ett enkelt webbaserat aktivitetshanteringsprogram, en ToDo-app, där du kan skapa, hämta och slutföra aktiviteter. Aktiviteterna lagras som JSON-dokument i Azure DocumentDB. Den här självstudien vägleder dig genom skapandet och distributionen av appen och förklarar vad som händer i varje kodfragment.

![Skärmdump av programmet My Todo List som skapas genom stegen i den här självstudien om Node.js](./media/documentdb-nodejs-application/image1.png)

Har du inte tid att gå igenom självstudien, men vill ha hela lösningen? Inga problem, du kan hämta den fullständiga exempellösningen från [GitHub][GitHub]. Läs bara [Viktigt](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md)-filen för instruktioner om hur du kör appen.

## <a name="_Toc395783176"></a>Förhandskrav
> [!TIP]
> Den här Node.js-självstudien förutsätter att du har tidigare erfarenhet av Node.js och Azure Websites.
> 
> 

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

   ELLER

   En lokal installation av [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md).
* [Node.js][Node.js] version v0.10.29 eller högre.
* [Express generator](http://www.expressjs.com/starter/generator.html) (kan installeras via `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Steg 1: Skapa ett DocumentDB-databaskonto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto eller om du använder DocumentDB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa ett nytt Node.js-program](#_Toc395783178).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Steg 2: Läs hur man skapar ett nytt Node.js-program
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
   
    ![Lär dig använda Node.js – Skärmdump av programmet Hello World i ett webbläsarfönster](./media/documentdb-nodejs-application/image12.png)

    Därefter, för att stoppa programmet, trycker du på CTRL+C i terminalfönstret och klickar sedan på **y** för att avbryta batch-jobbet.

## <a name="_Toc395783179"></a>Steg 3: Installera ytterligare moduler
Filen **package.json** är en av filerna som skapas i projektets rot. Den här filen innehåller en lista över ytterligare moduler som krävs för Node.js-programmet. Senare, när du distribuerar programmet till Azure Websites, används den här filen för att avgöra vilka moduler som behöver installeras på Azure som stöd för ditt program. Vi behöver installera två paket till för den här självstudien.

1. Gå tillbaka till terminalen och installera modulen **async** via npm.
   
        npm install async --save
2. Installera modulen **documentdb** via npm. Det här är modulen där DocumentDB-magin händer.
   
        npm install documentdb --save
3. En snabb kontroll av filen **package.json** i appen bör visa ytterligare moduler. Den här filen talar om för Azure vilka paket som ska laddas ned och installeras när du kör programmet. Det bör likna exemplet nedan.
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    Det här visar för Node (och senare Azure) att ditt program är beroende av de här ytterligare modulerna.

## <a name="_Toc395783180"></a>Steg 4: Använd DocumentDB-tjänsten i ett nod-program
När vi har slutfört den första installationen och konfigurationen är det dags att ta itu med vårt verkliga syfte: att skriva kod med Azure DocumentDB.

### <a name="create-the-model"></a>Skapa modellen
1. Skapa en ny katalog i projektkatalogen med namnet **models**, i samma katalog som package.json-filen.
2. I katalogen **models** skapar du en ny fil med namnet **taskDao.js**. Den här filen innehåller modellen för de aktiviteter som skapats av vårt program.
3. I samma **models**-katalog skapar du ytterligare en ny fil med namnet **docdbUtils.js**. Den här filen innehåller användbar och återanvändbar kod som vi ska använda i programmet. 
4. Kopiera följande kod till **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };               
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
   > [!TIP]
   > createCollection använder en valfri requestOptions-parameter som kan användas för att ange erbjudandetyp för samlingen. Om inget värde anges för requestOptions.offerType skapas samlingen med standardtypen för erbjudande.
   > 
   > Mer information om erbjudandetyper i DocumentDB finns i [Prestandanivåer i DocumentDB](documentdb-performance-levels.md) 
   > 
   > 
5. Spara och stäng filen **docdbUtils.js**.
6. I början av filen **taskDao.js** lägger du till följande kod för att referera till **DocumentDBClient** och **docdbUtils.js** som vi skapade ovan:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. Sedan lägger du till kod för att definiera och exportera aktivitetsobjektet. Den ansvarar för att initiera vårt aktivitetsobjekt och konfigurera databasen och dokumentsamlingen som vi ska använda.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. Lägg sedan till följande kod för att definiera ytterligare metoder för aktivitetsobjektet som tillåter samverkan med data som lagras i DocumentDB.
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. Spara och stäng filen **taskDao.js**. 

### <a name="create-the-controller"></a>Skapa styrningen
1. I projektets **routes**-katalog skapar du en ny fil med namnet **tasklist.js**. 
2. Lägg till följande kod i **tasklist.js**. Den läser in DocumentDBClient och async-moduler som används av **tasklist.js**. Detta definieras även i funktionen **TaskList**, som mottar en instans av **Task**-objektet som vi definierade tidigare:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. Fortsätt att lägga till kod i filen **tasklist.js** genom att lägga till metoderna **showTasks, addTask** och **completeTasks**:
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. Spara och stäng filen **tasklist.js**.

### <a name="add-configjs"></a>Lägg till config.js
1. Skapa en ny fil med namnet **config.js** i projektkatalogen.
2. Lägg till följande i **config.js**. Det definierar konfigurationsinställningar och värden som behövs i appen.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. I filen **config.js** uppdaterar du värdet för HOST och AUTH_KEY med de värden som finns i bladet Nycklar i ditt DocumentDB-konto på [Microsoft Azure Portal](https://portal.azure.com).
4. Spara och stäng filen **config.js**.

### <a name="modify-appjs"></a>Ändra app.js
1. Öppna filen **app.js** i projektkatalogen. Den här filen skapades tidigare när Express-webbappen skapades.
2. Lägg till följande kod högst upp i **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Den här koden definierar vilken konfigurationsfil som ska användas och läser sedan värden från denna fil till några variabler som vi snart ska använda.
4. Ersätt följande två rader i filen **app.js**:
   
        app.use('/', index);
        app.use('/users', users); 
   
      med följande utdrag:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Dessa rader definierar en ny instans av vårt **TaskDao**-objekt med en ny koppling till DocumentDB (med de värden som läses in från **config.js**), initierar aktivitetsobjektet och binder formuläråtgärder till metoder i vår **TaskList**-styrning. 
6. Avsluta med att spara och stänga filen **app.js**. Vi är nästan klara.

## <a name="_Toc395783181"></a>Steg 5: Skapa ett användargränssnitt
Nu är det dags att skapa användargränssnittet, så att användaren faktiskt kan samverka med vår app. Express-appen som vi skapade använder **Jade** som visningsmotor. Mer information om Jade finns på [http://jade-lang.com/](http://jade-lang.com/).

1. Filen **layout.jade** i katalogen **views** används som en global mall för andra **.jade**-filer. I det här steget ändrar du den så att den använder [Twitter Bootstrap](https://github.com/twbs/bootstrap), vilket är en verktygslåda som gör det enkelt att utforma en snygg webbplats. 
2. Öppna filen **layout.jade** i mappen **views** och ersätt innehållet med följande:
   
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
             button.btn(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             label Item Name:
             input(name="name", type="textbox")
             label Item Category:
             input(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   
    Det utökar layouten och ger innehåll för platshållaren **content** som vi såg i filen **layout.jade**.
   
    I den här layouten skapade vi två HTML-formulär. 
    Det första formuläret innehåller en tabell för våra data och en knapp som gör att vi kan uppdatera objekt genom att publicera till metoden **/completetask** i styrningen.
    Det andra formuläret innehåller två inmatningsfält och en knapp som gör att vi kan skapa ett nytt objekt genom att publicera till metoden **/addtask** i styrningen.
   
    Det här ska vara allt som behövs för att appen ska fungera.
4. Öppna filen **style.css** i katalogen **public\stylesheets** och ersätt koden med följande:
   
        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }
   
    Spara och stäng filen **style.css**.

## <a name="_Toc395783181"></a>Steg 6: Kör ditt program lokalt
1. Om du vill testa programmet på din lokala dator kör du `npm start` i terminalen för att starta programmet, uppdatera därefter din [http://localhost:3000](http://localhost:3000)-webbläsarsida. Sidan ska nu se ut som på bilden nedan:
   
    ![Skärmdump av programmet MyTodo List i ett webbläsarfönster](./media/documentdb-nodejs-application/image18.png)

    > [!TIP]
    > Om du får ett felmeddelande om indraget i layout.jade-filen eller index.jade-filen, säkerställ att de två första raderna i båda filerna är vänsterjusterade, utan blanksteg. Om det finns blanksteg före de två första raderna, ta bort dem, spara filerna och uppdatera sedan webbläsarfönstret. 

2. Använd fälten Objekt, Objektnamn och Kategori och klicka sedan på **Lägg till objekt**. Detta skapar ett dokument i DocumentDB med dessa egenskaper. 
3. Sidan bör uppdateras och visa det nya objektet i ToDo-listan.
   
    ![Skärmdump av programmet med ett nytt objekt i ToDo-listan](./media/documentdb-nodejs-application/image19.png)
4. Du slutför en aktivitet genom att markera kryssrutan i kolumnen Complete och sedan klicka på **Update tasks**. Detta uppdaterar det dokument som du redan har skapat.

5. För att stoppa programmet trycker du på CTRL+C i terminalfönstret och klickar sedan på **Y** för att avbryta batch-jobbet.

## <a name="_Toc395783182"></a>Steg 7: Distribuera ditt programutvecklingsprojekt till Azure Websites
1. Om du inte redan gjort det aktiverar du en git-databas för Azure-webbplatsen. Information om hur du gör det finns i artikeln [Lokal Git-distribuering på Azure App Service](../app-service-web/app-service-deploy-local-git.md).
2. Lägg till Azure-webbplatsen som en fjärransluten git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Distribuera genom att pusha till fjärranslutningen.
   
        git push azure master
4. Efter några sekunder har git publicerat din webbapp och öppnar en webbläsare där du kan se ditt arbete köras i Azure!

    Grattis! Du har precis skapat din första Node.js Express-webbapp med Azure DocumentDB och publicerat den på Azure Websites.

    Om du vill hämta eller referera till det färdiga referensprogrammet för den här självstudien kan det hämtas från [GitHub][GitHub].

## <a name="_Toc395637775"></a>Nästa steg

* Vill du utföra skalnings- och prestandatester med DocumentDB? Se [Prestanda- och skalningstester med Azure DocumentDB](documentdb-performance-testing.md)
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Utforska [Dokumentation om DocumentDB](https://docs.microsoft.com/en-us/azure/documentdb/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app


