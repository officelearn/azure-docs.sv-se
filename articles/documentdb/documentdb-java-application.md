---
title: "Självstudie om apputveckling i Java med DocumentDB | Microsoft Docs"
description: "Den här självstudien om apputveckling i Java visar hur Azure DocumentDB-tjänsten kan användas för lagring av och åtkomst till data från en Java-app på Azure Websites."
keywords: "Programutveckling, självstudier för databas, java-program, självstudier för java-webbprogram, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: java
author: dennyglee
manager: jhubbard
editor: mimig
ms.assetid: 0867a4a2-4bf5-4898-a1f4-44e3868f8725
ms.service: documentdb
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/16/2016
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 6e315ea33fe4a493f1ab349482f9af3a732b3127
ms.lasthandoff: 04/18/2017


---
# <a name="build-a-java-web-application-using-documentdb"></a>Skapa en webbapp i Java med DocumentDB
> [!div class="op_single_selector"]
> * [NET](documentdb-dotnet-application.md)
> * [.NET för MongoDB](documentdb-mongodb-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Den här självstudien om webbapputveckling i Java beskriver hur [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) kan användas för lagring av och åtkomst till data från en Java-app på Azure Websites. I det här avsnittet får du veta följande:

* Hur du skapar en grundläggande JSP-app i Eclipse.
* Hur du arbetar i Azure DocumentDB-tjänsten med [DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java).

I den här självstudien om Java visar vi hur du skapar en webbaserad aktivitetshanteringsapp där du kan skapa, hämta och markera aktiviteter som slutförda, enligt bilden nedan. Alla aktiviteter i att göra-listan lagras som JSON-dokument i Azure DocumentDB.

![Java-app med att göra-lista](./media/documentdb-java-application/image1.png)

> [!TIP]
> Den här självstudien om apputveckling förutsätter att du har tidigare erfarenhet av Java. Om du inte har använt Java eller [verktygen som krävs](#Prerequisites) tidigare, rekommenderar vi att du hämtar det fullständiga [todo](https://github.com/Azure-Samples/documentdb-java-todo-app)-projektet från GitHub och skapar det enligt [anvisningarna i slutet av artikeln](#GetProject). När du har skapat det kan du läsa den här artikeln för information om koden i projektets sammanhang.  
> 
> 

## <a id="Prerequisites"></a>Förhandskrav för den här självstudien för Java-webbprogram
Innan du påbörjar den här självstudien om apputveckling måste du ha följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/)

    ELLER

    En lokal installation av [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md).
* [Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Eclipse IDE för Java EE-utvecklare.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
* [An Azure Website med en aktiverad Java runtime-miljö (t.ex. Tomcat eller Jetty).](../app-service-web/web-sites-java-get-started.md)

Om du installerar verktygen för första gången finns det en beskrivning av installationsprocessen på coreservlets.com avsnittet Quick Start i artikeln [Tutorial: Installing TomCat7 and Using it with Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

## <a id="CreateDB"></a>Steg 1: Skapa ett DocumentDB-databaskonto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto eller om du använder DocumentDB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa Java JSP-programmet](#CreateJSP).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a id="CreateJSP"></a>Steg 2: Skapa Java JSP-programmet
Så här skapar du JSP-appen:

1. Vi börjar med att skapa ett Java-projekt. Starta Eclipse och klicka på **Arkiv**, **Nytt** och slutligen **Dynamiskt webbprojekt**. Om **Dynamiskt webbprojekt** inte finns på listan över tillgängliga projekt gör du så här: klicka på **Arkiv**, **Nytt** och **Projekt**…, expandera **Webb**, klicka på **Dynamiskt webbprojekt** och slutligen på **Nästa**.
   
    ![JSP Java-apputveckling](./media/documentdb-java-application/image10.png)
2. Ange ett projektnamn i rutan **Projektnamn** och välj eventuellt ett värde (t.ex. Apache Tomcat v7.0) i rullgardinsmenyn **Körningsmål** och klicka sedan på **Slutför**. När du väljer ett mål för körning kan du köra projektet lokalt genom Eclipse.
3. Expandera projektet i Eclipse i vyn Projektutforskaren. Högerklicka på **Webbinnehåll**, klicka på **Ny** och sedan på **JSP-fil**.
4. I dialogrutan **Ny JSP-fil** namnger du filen **index.jsp**. Behåll den överordnade mappen som **Webbinnehåll**, så som visas i nedanstående bild, och klicka sedan på **Nästa**.
   
    ![Skapa en ny JSP-fil – självstudie om Java-webbapp](./media/documentdb-java-application/image11.png)
5. I dialogrutan **Välj JSP-mall** väljer du i den här självstudien **Ny JSP-fil (html)** och klickar sedan på **Slutför**.
6. Lägg till en text som visar **Hello World!** i det befintliga <body>-elementet när index.jsp-filen öppnas i Eclipse. Det uppdaterade <body>-innehållet bör se ut som följande kod:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Spara filen index.jsp.
8. Om du anger ett mål för körning i steg 2 kan du klicka på **Projekt** och **Kör** för att köra JSP-appen lokalt:
   
    ![Hello World – självstudie om Java-app](./media/documentdb-java-application/image12.png)

## <a id="InstallSDK"></a>Steg 3: Installera DocumentDB Java SDK:n
Det enklaste sättet att dra in DocumentDB Java SDK och dess beroenden är via [Apache Maven](http://maven.apache.org/).

För att kunna göra det måste du konvertera ditt projekt till ett Maven-projekt genom följande steg:

1. Högerklicka på ditt projekt i Projektutforskaren, klicka på **Konfigurera** och sedan på **Konvertera till Maven-projekt**.
2. I fönstret **Skapa ny POM** godkänner du standardinställningarna och klickar på **Slutför**.
3. Öppna filen pom.xml i **Projektutforskaren**.
4. På fliken **Beroenden** i panelen **Beroenden** klickar du på **Lägg till**.
5. I fönstret **Välj beroende** gör du följande:
   
   * Ange com.microsoft.azure i rutan **Grupp-id**.
   * Ange azure-documentdb i rutan **Artefakt-id**.
   * Ange 1.5.1 i rutan **Version**.
     
     ![Installera DocumentDB-SDK för Java-app](./media/documentdb-java-application/image13.png)
     
     Du kan även lägga till XML för beroenden för grupp-id och artefakt-id direkt till pom.xml via en textredigerare:
     
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.9.1</version>
        </dependency>
6. Klicka på **Ok**, så installerar Maven DocumentDB-SDK:et för Java-app
7. Spara filen pom.xml.

## <a id="UseService"></a>Steg 4: Använd DocumentDB-tjänsten i ett Java-program
1. Först måste du definiera objektet TodoItem:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    I det här projektet generar vi konstruktor, get-metoder, set-metoder och ett verktyg via [Project Lombok](http://projectlombok.org/). Du kan även skriva koden manuellt eller generera den i IDE.
2. Om du vill anropa DocumentDB-tjänsten måste du skapa en instans av en ny **DocumentClient**. Oftast är det bäst att återanvända en **DocumentClient** istället för att skapa en ny klient för varje efterföljande begäran. Vi kan återanvända klienten genom att omsluta klienten med en **DocumentClientFactory**. Det är även här du ska klistra in de URI- och PRIMÄRNYCKEL-värden som du sparade i Urklipp i [steg 1](#CreateDB). Ersätt [YOUR\_ENDPOINT\_HERE] med din URI och ersätt [YOUR\_KEY\_HERE] med din PRIMÄRNYCKEL.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Nu skapar vi ett DAO-objekt (Data Access Object) för att separera sparade ToDo-objekt i DocumentDB.
   
    För att kunna spara ToDo-objekt till en samling måste klienten veta vilken databas och samling de ska sparas i (refererade med självlänkar). Det är vanligen bäst att cachelagra databasen och samlingen när det är möjligt, för att undvika ytterligare rundor till databasen.
   
    Följande kod visar hur du hämtar vår databas och samling, om den redan finns, eller skapar en ny om det inte finns någon:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The DocumentDB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. Nästa steg är att skriva kod för att bevara TodoItems i samlingen. I det här exemplet ska vi serialisera och deserialisera TodoItem Plain Old Java Objects (POJO) till JSON-dokument via [Gson](https://code.google.com/p/google-gson/). [Jackson](http://jackson.codehaus.org/) eller din egen anpassade serialiserare är också bra alternativ för serialisering av POJO-objekt.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Precis som i DocumentDB-databaser och samlingar refereras dokument också med självlänkar. Med följande hjälpfunktion kan vi hämta dokument efter andra attribut (t.ex. ”id”) än självlänkar:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Med hjälpmetoden i steg 5 kan vi hämta ett TodoItem JSON-dokument efter id och sedan deserialisera det till en POJO:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. Vi kan även använda DocumentClient för att hämta en samling eller en lista över TodoItems med hjälp av DocumentDB SQL:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. Det finns många sätt att uppdatera ett dokument med DocumentClient. Vi vill kunna ange om ett TodoItem har slutförts eller inte i vår Todo-app. Du kan göra det genom att uppdatera attributet ”slutförd” i dokumentet:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Slutligen vill vi ha möjlighet att ta bort ett TodoItem från listan. Vi kan använda hjälpmetoden vi skrev tidigare för att hämta självlänken och sedan instruera klienten att ta bort den:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // DocumentDB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>Steg 5: Koppla samman resten av Java-programmets utvecklingsprojekt
Nu har vi gjort alla roliga delar, och allt som återstår är att skapa ett snabbt användargränssnitt och ansluta det till vår DAO.

1. Vi börjar med att skapa en styrning (controller) som kan anropa vår DAO:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    I en mer komplicerad app kanske styrningen innehåller komplicerad affärslogik utöver en DAO.
2. Därefter skapar vi ett servlet som dirigerar HTTP-förfrågningar till styrningen:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Vi behöver ett webbaserat användargränssnitt att visa för användaren. Vi skriver om index.jsp som vi skapade tidigare:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure DocumentDB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. Slutligen skriver vi JavaScript för klientsidan som kopplar samman webbgränssnitt och servlet.
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Fantastiskt! Nu återstår bara att testa appen. Kör appen lokalt och lägg till några Todo-objekt genom att fylla i namn och kategori och klicka på **Lägg till aktivitet**.
6. När objektet visas kan du uppdatera om aktiviteten är slutförd eller inte genom att använda kryssrutan och sedan klicka på **Uppdatera aktiviteter**.

## <a id="Deploy"></a>Steg 6: Distribuera ditt Java-program till Azure Websites
Med Azure Websites är det enkelt att distribuera Java-appar. Allt du behöver göra är exportera appen som en WAR-fil och antingen ladda upp den via källkontrollen (t.ex. GIT) eller FTP.

1. Du exporterar appen till WAR genom att högerklicka på projektet i **Projektutforskaren**, klicka på **Exportera** och sedan på **WAR-fil**.
2. I fönstret **WAR-export** gör du så här:
   
   * Ange azure-documentdb-java-sample i rutan Webbprojekt.
   * Välj en plats där WAR-filen ska sparas i rutan Destination.
   * Klicka på **Slutför**.
3. Nu när du har en WAR-fil kan du bara ladda upp den till katalogen **Webbappar** på Azure Websites. Anvisningar för uppladdning av filen finns på [Lägga till ett program på din Java-webbplats på Azure](../app-service-web/web-sites-java-add-app.md).
   
    När WAR-filen har laddats upp till katalogen Webbappar identifierar körningsmiljön att du har lagt till den och läser in den automatiskt.
4. Du kan visa din färdiga produkt genom att besöka http:/ /NAMNETPÅDIN\_WEBBPLATS\_.azurewebsites.net/azure-documentdb-java-sample/ och börja lägga till aktiviteter!

## <a id="GetProject"></a>Hämta projektet från GitHub
Alla exempel i den här självstudien finns i projektet [Todo](https://github.com/Azure-Samples/documentdb-java-todo-app) på GitHub. Om du vill importera Todo-projektet till Eclipse ska du se till att du har de program och resurser som anges i avsnittet [Förutsättningar](#Prerequisites) och sedan göra följande:

1. Installera [Project Lombok](http://projectlombok.org/). Lombok används för att generera konstruktorer, get-metoder och set-metoder i projektet. När du har laddat ned filen lombok.jar installerar du den genom att dubbelklicka på filen eller från kommandoraden.
2. Om Eclipse är öppet startar du om det för att läsa in Lombok.
3. I **Arkiv**-menyn i Eclipse klickar du på **Importera**.
4. I fönstret **Importera** klickar du på **Git**, **Projekt från Git** och **Nästa**.
5. På skärmen **Välj lagerkälla** klickar du på **Klona URI**.
6. På skärmen **Git-lagerkälla**, i rutan **URI**, anger du https://github.com/Azure-Samples/documentdb-java-todo-app.git och klickar sedan på **Nästa**.
7. På skärmen**Val av gren** kontrollerar du att **master** är markerat och klickar sedan på **Nästa**.
8. På skärmen **Lokal destination** klickar du på **Bläddra** och väljer en mapp dit lagret kan kopieras och klickar sedan på **Nästa**.
9. På skärmen **Välj en guide för importprojekt** kontrollerar du att **Importera befintliga projekt** är markerat och klickar sedan på **Nästa**.
10. På skärmen **Importera projekt** avmarkerar du projektet **DocumentDB** och klickar sedan på **Slutför**. DocumentDB-projektet innehåller DocumentDB Java SDK, vilket vi istället lägger till som ett beroende.
11. Navigera till azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java i **Projektutforskaren** och ersätt värdena i VÄRD och HUVUDNYCKEL med URI och PRIMÄRNYCKEL till ditt DocumentDB-konto och spara sedan filen. Mer information finns [steg 1. Steg 1: Skapa ett DocumentDB-databaskonto](#CreateDB).
12. I **Projektutforskaren** högerklickar du på **azure-documentdb-java-sample**. Klicka sedan på **Byggsökväg** och på **Konfigurera byggsökväg**.
13. På skärmen **Java byggsökväg**, i den högra panelen, väljer du fliken **Bibliotek** och klickar sedan på **Lägg till externa JAR:er**. Navigera till platsen där filen lombok.jar finns. Klicka på **Öppna** och sedan på **OK**.
14. Öppna fönstret **Egenskaper** genom steg 12 och klicka sedan på **Körningsmål** i den vänstra panelen.
15. På skärmen **Körningsmål** klickar du på **Nytt**, väljer **Apache Tomcat v7.0** och klickar sedan på **OK**.
16. Öppna fönstret **Egenskaper** som i steg 12 och klicka sedan på **Projektfasetter** i den vänstra panelen.
17. På skärmen **Projektfasetter** väljer du **Dynamisk webbmodul** och **Java**, och klickar sedan på **OK**.
18. På fliken **Servrar** längst ned på skärmen högerklickar du på **Tomcat v7.0-server på localhost** och klickar sedan på **Lägg till och ta bort**.
19. I fönstret **Lägg till och ta bort** flyttar du **azure-documentdb-java-sample** till rutan **Konfigurerad** och klickar sedan på **Slutför**.
20. På fliken **Server** högerklickar du på **Tomcat v7.0-server på localhost** och klickar sedan på **Starta om**.
21. Navigera till http://localhost:8080/azure-documentdb-java-sample/ i webbläsaren och börja lägga till aktiviteter i listan. Om du har ändrat portarnas standardvärden ändrar du 8080 till värdet du har valt.
22. [Steg 6 innehåller information om hur du distribuerar ditt projekt till en Azure-webbplats. Distribuera appen till Azure Websites](#Deploy).

[1]: media/documentdb-java-application/keys.png

