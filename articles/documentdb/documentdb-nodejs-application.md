<properties 
    pageTitle="Lär dig använda Node.js – självstudie om DocumentDB Node.js | Microsoft Azure" 
    description="Lär dig Node.js! I den här självstudien får du lära dig använda Microsoft Azure DocumentDB för att lagra och komma åt data från Node.js Express-webbappar på Azure Websites." 
    keywords="Application development, database tutorial, learn node.js, node.js tutorial, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="hero-article" 
    ms.date="04/18/2016" 
    ms.author="andrl"/>

# <a name="_Toc395783175"></a>Skapa en Node.js-webbapp med DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

I den här självstudien om Node.js beskrivs hur Azure DocumentDB-tjänsten kan användas för lagring av och åtkomst till data från ett Node.ja Express-program på Azure Websites.

Vi rekommenderar att du börjar med att titta på nedanstående video, där du får lära dig etablera ett Azure DocumentDB-databaskonto och lagra JSON-dokument i ditt Node.js-program. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Återvänd sedan till den här självstudien om Node.js, där du får svar på följande frågor:

- Hur arbetar jag med DocumentDB via documentdb npm-modulen?
- Hur distribuerar jag webbappen till Azure Websites?

Genom att följa stegen i den här självstudien om databaser kommer du att skapa ett enkelt webbaserat aktivitetshanteringsprogram där du kan skapa, hämta och slutföra aktiviteter. Aktiviteterna lagras som JSON-dokument i Azure DocumentDB.

![Skärmdump av programmet My Todo List som skapas genom stegen i den här självstudien om Node.js](./media/documentdb-nodejs-application/image1.png)

Har du inte tid att gå igenom självstudien, men vill ha hela lösningen? Inga problem, du kan hämta den fullständiga exempellösningen från [GitHub][].

## <a name="_Toc395783176"></a>Förutsättningar

> [AZURE.TIP] Den här Node.js-självstudien förutsätter att du har tidigare erfarenhet av Node.js och Azure Websites.

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande:

- Ett aktivt Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js][] version 0.10.29 eller högre.
- [Express generator](http://www.expressjs.com/starter/generator.html) (kan installeras via `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Steg 1: Skapa ett DocumentDB-databaskonto

Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto kan du gå vidare till [Steg 2: Skapa ett nytt Node.js-program](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Steg 2: Skapa ett nytt Node.js-program

Nu ska vi skapa ett grundläggande Hello World Node.js-projekt med [Express](http://expressjs.com/)-ramverket.

1. Öppna valfri terminal.

2. Skapa ett nytt program kallat **todo** med hjälp av Express Generator.

        express todo

3. Öppna din nya **todo**-katalog och installera beroenden.

        cd todo
        npm install

4. Kör det nya programmet.

        npm start

5. Du kan visa det nya programmet genom att öppna [http://localhost:3000](http://localhost:3000) i webbläsaren.

    ![Lär dig använda Node.js – Skärmdump av programmet Hello World i ett webbläsarfönster](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Steg 3: Installera ytterligare moduler

Filen **package.json** är en av filerna som skapas i projektets rot. Den här filen innehåller en lista över ytterligare moduler som krävs för Node.js-programmet. Senare, när du distribuerar programmet till Azure Websites, används den här filen för att avgöra vilka moduler som behöver installeras på Azure som stöd för ditt program. Vi behöver installera två paket till för den här självstudien.

1. Gå tillbaka till terminalen och installera modulen **async** via npm.

        npm install async --save

1. Installera modulen **documentdb** via npm. Det här är modulen där DocumentDB-magin händer.

        npm install documentdb --save

3. En snabb kontroll av filen **package.json** i appen bör visa ytterligare moduler. Den här filen talar om för Azure vilka paket som ska laddas ned och installeras när du kör programmet. Det bör likna exemplet nedan.

    ![Skärmdump av fliken package.json](./media/documentdb-nodejs-application/image17.png)

       This tells Node (and Azure later) that your application depends on these additional modules.

## <a name="_Toc395783180"></a>Steg 4: Använda DocumentDB-tjänsten i en Node-app

När vi har slutfört den första installationen och konfigurationen är det dags att ta itu med vårt verkliga syfte: att skriva kod med Azure DocumentDB.

### Skapa modellen

1. Skapa en ny katalog med namnet **models** i projektkatalogen.
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
                            
                            var requestOptions = {
                                offerType: 'S1'
                            };
                            
                            client.createCollection(databaseLink, collectionSpec, requestOptions, function (err, created) {
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

> [AZURE.TIP] createCollection använder en valfri requestOptions-parameter som kan användas för att ange erbjudandetyp för samlingen. Om inget värde anges för requestOptions.offerType skapas samlingen med standardtypen för erbjudande.
> Mer information om erbjudandetyper i DocumentDB finns i [Prestandanivåer i DocumentDB](documentdb-performance-levels.md) 
        
3. Spara och stäng filen **docdbUtils.js**.

4. I början av filen **taskDao.js** lägger du till följande kod för att referera till **DocumentDBClient** och **docdbUtils.js** som vi skapade ovan:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');

4. Sedan lägger du till kod för att definiera och exportera aktivitetsobjektet. Den ansvarar för att initiera vårt aktivitetsobjekt och konfigurera databasen och dokumentsamlingen som vi ska använda.

        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
        
          this.database = null;
          this.collection = null;
        }
        
        module.exports = TaskDao;

5. Lägg sedan till följande kod för att definiera ytterligare metoder för aktivitetsobjektet som tillåter samverkan med data som lagras i DocumentDB.

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

6. Spara och stäng filen **taskDao.js**. 

### Skapa styrningen

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
 
### Lägg till config.js

1. Skapa en ny fil med namnet **config.js** i projektkatalogen.
2. Lägg till följande i **config.js**. Det definierar konfigurationsinställningar och värden som behövs i appen.

        var config = {}
        
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
        
        module.exports = config;

3. I filen **config.js** uppdaterar du värdet för HOST och AUTH_KEY med de värden som finns i bladet Nycklar i ditt DocumentDB-konto på [Microsoft Azure Portal](https://portal.azure.com):

4. Spara och stäng filen **config.js**.
 
### Ändra app.js

1. Öppna filen **app.js** i projektkatalogen. Den här filen skapades tidigare när Express-webbappen skapades.
2. Lägg till följande kod högst upp i **app.js**
    
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');

3. Den här koden definierar vilken konfigurationsfil som ska användas och läser sedan värden från denna fil till några variabler som vi snart ska använda.
4. Ersätt följande två rader i filen **app.js**:

        app.use('/', routes);
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



6. Dessa rader definierar en ny instans av vårt **TaskDao**-objekt med en ny koppling till DocumentDB (med de värden som läses in från **config.js**), initierar aktivitetsobjektet och binder formuläråtgärder till metoder i vår **TaskList**-styrning. 

7. Avsluta med att spara och stänga filen **app.js**. Vi är nästan klara.
 
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

4. Öppna nu filen **index.jade**, den vy som ska användas av vårt program, och ersätt innehållet i filen med följande:

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

5. Öppna filen **style.css** i katalogen **public\stylesheets** och ersätt koden med följande:

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

## <a name="_Toc395783181"></a>Steg 6: Kör programmet lokalt

1. Testa programmet på din lokala dator genom att köra `npm start` i en terminal för att starta programmet och starta en webbläsare med en sida som ser ut som på bilden nedan:

    ![Skärmdump av programmet MyTodo List i ett webbläsarfönster](./media/documentdb-nodejs-application/image18.png)


2. Ange information i fälten Objekt, Objektnamn och Kategori och klicka sedan på **Lägg till objekt**.

3. Sidan bör uppdateras och visa det nya objektet i ToDo-listan.

    ![Skärmdump av programmet med ett nytt objekt i ToDo-listan](./media/documentdb-nodejs-application/image19.png)

4. Du slutför en aktivitet genom att markera kryssrutan i kolumnen Complete och sedan klicka på **Update tasks**.

## <a name="_Toc395783182"></a>Steg 7: Distribuera ditt programutvecklingsprojekt till Azure Websites

1. Om du inte redan gjort det aktiverar du en git-databas för Azure-webbplatsen. Information om hur du gör det finns under ämnet [Kontinuerlig distribution med GIT i Azure App Service](../app-service-web/web-sites-publish-source-control.md).

2. Lägg till Azure-webbplatsen som en fjärransluten git.

        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Distribuera genom att pusha till fjärranslutningen.

        git push azure master

4. Efter några sekunder har git publicerat din webbapp och öppnar en webbläsare där du kan se ditt arbete köras i Azure!

## <a name="_Toc395637775"></a>Nästa steg

Grattis! Du har precis skapat din första Node.js Express-webbapp med Azure DocumentDB och publicerat den på Azure Websites.

Du kan ladda ned källkoden till det fullständiga referensprogrammet från [GitHub][].

Mer information finns i [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app
 



<!--HONumber=Jun16_HO2-->


