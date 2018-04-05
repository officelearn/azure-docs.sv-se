---
title: 'Azure Table storage: skapa en Node.js-webbapp | Microsoft Docs'
description: En självstudiekurs som bygger på webbprogram med snabb kursen genom att lägga till Azure Storage-tjänster och Azure-modulen.
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2018
ms.author: mimig
ms.openlocfilehash: b63f6b3be2e4576b304c1a73ff326a937815b27e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-table-storage-nodejs-web-application"></a>Azure Table storage: Node.js-Webbapp
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Översikt
I den här självstudiekursen programmet du skapade i den [Node.js-Webbapp med hjälp av] kursen utökas som arbetar med data management-tjänster med Microsoft Azure-klientbibliotek för Node.js. Du kan utöka ditt program genom att skapa en webbaserad-uppgiftslista program som du kan distribuera till Azure. Listan kan användaren hämta uppgifter, lägga till nya aktiviteter och markera aktiviteter som slutförda.

Uppgiftsobjekt lagras i Azure Storage eller Azure Cosmos DB. Azure Storage och Azure Cosmos DB tillhandahåller lagring av Ostrukturerade data som är feltolerant och hög tillgänglighet. Azure Storage och Azure Cosmos DB innehåller flera datastrukturer där du kan lagra och komma åt data. Du kan använda lagring och Azure Cosmos DB services från API: er som ingår i Azure SDK för Node.js eller via REST API: er. Mer information finns i [lagring och åtkomst till Data i Azure].

Den här kursen förutsätter att du har slutfört den [Node.js-Webbapp] och [Node.js med snabb][Node.js-Webbapp med hjälp av] självstudier.

Det innehåller följande information:

* Hur du arbetar med motorn Jade mall
* Hur du arbetar med Azure Data Management services

Följande skärmbild visar den färdiga appen:

![Slutförda webbsida i internet explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Ange autentiseringsuppgifter för lagring i Web.Config
Du måste överföra i lagring autentiseringsuppgifter för åtkomst till Azure Storage eller Azure Cosmos DB. Detta görs genom att använda inställningarna för web.config-programmet.
Inställningarna för web.config skickas som miljövariabler till noden, som läses sedan av Azure SDK.

> [!NOTE]
> Lagring autentiseringsuppgifter används bara när programmet distribueras till Azure. När du kör i emulatorn använder programmet storage-emulatorn.
>
>

Utför följande steg för att hämta autentiseringsuppgifter för lagringskonto och lägga till dem i web.config-inställningar:

1. Om den inte redan är öppen start av Azure PowerShell från den **starta** menyn genom att expandera **alla program, Azure**, högerklicka på **Azure PowerShell**, och välj sedan  **Kör som administratör**.
2. Ändra sökvägen till mappen som innehåller programmet. Till exempel C:\\nod\\tasklist\\WebRole1.
3. Ange följande cmdlet för att hämta kontoinformationen för lagring i Azure Powershell-fönstret:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   Föregående cmdlet hämtar listan över storage-konton och konto nycklar som hör till din värdbaserade tjänst.

   > [!NOTE]
   > Eftersom Azure SDK om du skapar ett lagringskonto när du distribuerar en tjänst, ska ett lagringskonto redan finnas från att distribuera ditt program i föregående guider.
   >
   >
4. Öppna den **ServiceDefinition.csdef** -fil som innehåller miljöinställningar som används när programmet distribueras till Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Infoga följande kodblock under **miljö** element, ersätter {LAGRINGSKONTO} och {LAGRINGSÅTKOMSTNYCKEL} med namnet på kontot och den primära nyckeln för lagringskontot som du vill använda för distribution:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![Filinnehållet web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Spara filen och stäng Anteckningar.

### <a name="install-additional-modules"></a>Installera ytterligare moduler
1. Använd följande kommando för att installera [azure], [nod-uuid] [nconf] och [asynkrona] moduler lokalt samt att spara en post för att den **package.json** fil:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  Kommandots utdata ska se ut ungefär så här:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Med hjälp av tjänsten tabellen i en node-App
I det här avsnittet grundläggande program som skapats av den **express** kommandot utökas genom att lägga till en **task.js** -fil som innehåller modellen för dina uppgifter. Ändra den befintliga **app.js** filen och skapa en ny **tasklist.js** fil som används i modellen.

### <a name="create-the-model"></a>Skapa modellen
1. I den **WebRole1** directory, skapa en ny katalog med namnet **modeller**.
2. I den **modeller** directory, skapa en ny fil med namnet **task.js**. Den här filen innehåller modellen för de aktiviteter som skapats av programmet.
3. I början av den **task.js** lägger du till följande kod för att referera till bibliotek som krävs:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Lägg till kod för att definiera och exportera aktivitetsobjektet. Aktivitetsobjektet ansvarar för att ansluta till tabellen.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Lägg till följande kod för att definiera ytterligare metoder för aktivitetsobjektet som tillåter samverkan med data som lagras i tabellen:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(this.tablename, query, null, null, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.replaceEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Spara och Stäng den **task.js** fil.

### <a name="create-the-controller"></a>Skapa styrningen
1. I den **WebRole1/vägar** directory, skapa en ny fil med namnet **tasklist.js** och öppna den i en textredigerare.
2. Lägg till följande kod i **tasklist.js**. Den här koden läser in i azure och async-moduler som används av **tasklist.js** och definierar de **TaskList** funktion, som mottar en instans av den **aktivitet** objekt vi definierade tidigare:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Fortsätta att lägga till den **tasklist.js** filen genom att lägga till de metoder som används för att **showTasks**, **addTask**, och **completeTasks**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = new azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = {
            name: req.body.name, 
            category: req.body.category
        };
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Spara den **tasklist.js** fil.

### <a name="modify-appjs"></a>Ändra app.js
1. I den **WebRole1** katalog, öppna den **app.js** i en textredigerare.
2. Lägg till följande för att läsa in modulen för azure och ange namn och partition tabellnyckel i början av filen:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. Rulla ned till där du ser följande rad i filen app.js:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Ersätt de föregående raderna med följande kod. Den här koden initierar en instans av <strong>aktivitet</strong> med en anslutning till ditt lagringskonto. Den <strong>aktivitet</strong> har överförts till den <strong>TaskList</strong>, som används för att kommunicera med tjänsten tabell:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Spara den **app.js** fil.

### <a name="modify-the-index-view"></a>Ändra indexvyn
1. Ändra sökvägen till den **vyer** katalog och öppna den **index.jade** i en textredigerare.
2. Ersätt innehållet i den **index.jade** med följande kod. Den här koden definierar vyn för att visa befintliga aktiviteter och definierar ett formulär för att lägga till nya aktiviteter och markera befintliga som slutförts.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks == []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Spara och Stäng **index.jade** fil.

### <a name="modify-the-global-layout"></a>Ändra globala layouten
Filen **layout.jade** i katalogen **views** används som en global mall för andra **.jade**-filer. I det här steget kan du ändra den **layout.jade** fil som ska användas [Twitter Bootstrap](https://github.com/twbs/bootstrap), vilket är en verktygslåda som gör det enkelt att utforma en snygg webbplats.

1. Ladda ned och extrahera filerna för [Twitter Bootstrap](http://getbootstrap.com/). Kopiera den **bootstrap.min.css** filen från den **bootstrap\\dist\\css** mappen till den **offentliga\\matmallar** katalogen för tillämpningsprogrammet tasklist.
2. Från den **vyer** mapp, öppna den **layout.jade** filen i en textredigerare och Ersätt innehållet med följande:

    doctype html  html    head      title= title      link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')      link(rel='stylesheet', href='/stylesheets/style.css')    body.app      nav.navbar.navbar-default        div.navbar-header          a.navbar-brand(href='/') My Tasks      block content

3. Spara den **layout.jade** fil.

### <a name="running-the-application-in-the-emulator"></a>Kör programmet i emulatorn
Använd följande kommando för att starta programmet i emulatorn.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

Webbläsaren öppnas och visar följande sida:

![En webbplats som växlingsbart systemminne med titeln uppgiftslistan med en tabell som innehåller uppgifter och för att lägga till en ny uppgift.](./media/table-storage-cloud-service-nodejs/node44.png)

Använd formuläret för att lägga till objekt eller ta bort befintliga objekt genom att markera dem som slutförda.

## <a name="publishing-the-application-to-azure"></a>Publicera program till Azure
Anropa följande cmdlet om du vill distribuera dina värdbaserade tjänsten till Azure i Windows PowerShell-fönstret.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Ersätt **myuniquename** med ett unikt namn för det här programmet. Ersätt **datacentername** med namnet på ett Azure-datacenter som **västra USA**.

När distributionen är klar bör du se ett svar som liknar följande:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Genom att ange den **-starta** alternativ i föregående cmdlet webbläsaren öppnas och visar programmet körs i Azure när publiceringen är klar.

![Ett webbläsarfönster som visar sidan uppgiftslistan. URL: en anger sidan finns nu på Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Stoppa och ta bort programmet
När du distribuerar ditt program, kan du vill inaktivera den så att du kan undvika kostnader eller skapa och distribuera andra program inom den kostnadsfria utvärderingsversionen tidsperioden.

Azure fakturerar webbrollsinstanser per timme förbrukad servertid.
Servertid förbrukas när programmet har distribuerats, även om instanserna inte körs och är i stoppat tillstånd.

Följande steg visar hur du stoppar och ta bort programmet.

1. Stoppa tjänstdistributionen som skapades i föregående avsnitt med följande cmdlet i Windows PowerShell-fönstret:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   Det kan ta flera minuter att stoppa tjänsten. När tjänsten har stoppats får du ett meddelande som anger att den har stoppats.

2. Ta bort tjänsten genom att anropa följande cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   När du uppmanas, anger du **Y** för att ta bort tjänsten.

   Det kan ta flera minuter att ta bort tjänsten. När tjänsten har tagits bort, visas ett meddelande som anger att tjänsten har tagits bort.

[Node.js-Webbapp med hjälp av]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[lagring och åtkomst till Data i Azure]: https://docs.microsoft.com/azure/storage/
[Node.js-Webbapp]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


