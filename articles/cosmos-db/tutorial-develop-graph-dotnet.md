---
title: 'Azure Cosmos DB: Utveckla med Graph API i .NET | Microsoft Docs'
description: "Lär dig att utveckla med Azure Cosmos DB SQL-API med hjälp av .NET"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ddbfe11e4415e1c240914142f4daf54b3032f5d8
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Utveckla med Graph API i .NET
Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här kursen visar hur du skapar ett Azure DB som Cosmos-konto med Azure-portalen och hur du skapar ett diagram databas och en behållare. Sedan skapar ett enkelt sociala nätverk med fyra personer som använder den [Graph API](graph-sdk-dotnet.md), sedan passerar och frågar diagrammet med Gremlin.

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Skapa ett diagram databas och en behållare
> * Serialisera hörn och kanter till .NET-objekt
> * Lägg till hörn och kanter
> * Fråga diagrammet med Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Diagram i Azure Cosmos DB
Du kan använda Azure Cosmos DB att skapa, uppdatera och fråga diagram med hjälp av den [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) bibliotek. Microsoft.Azure.Graph-bibliotek innehåller en enda metod `CreateGremlinQuery<T>` ovanpå det `DocumentClient` klassen för att köra Gremlin frågor.

Gremlin är en funktionell programmeringsspråk som stöder skriva åtgärder (DML) och fråga och traversal-åtgärder. Vi igenom några exempel i den här artikeln för att få din igång med Gremlin. Se [Gremlin frågor](gremlin-support.md) en detaljerad genomgång av Gremlin av funktionerna i Azure Cosmos DB. 

## <a name="prerequisites"></a>Förutsättningar
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 
    * Du kan också använda den [lokala emulatorn](local-emulator.md) för den här självstudiekursen.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Skapa konto

Börja med att skapa ett Azure DB som Cosmos-konto i Azure-portalen.  

> [!TIP]
> * Redan har ett Azure DB som Cosmos-konto? I så fall, gå vidare till [konfigurera Visual Studio-lösning](#SetupVS)
> * Om du använder Azure Cosmos DB-emulatorn, följer du stegen i [Azure Cosmos DB emulatorn](local-emulator.md) konfigurera emulatorn och gå vidare till [ställa in din Visual Studio-lösning](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I den **nytt projekt** markerar **mallar** / **Visual C#** / **Konsolapp (.NET Framework)**namnge projektet och klicka sedan på **OK**.
4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**
5. I den **NuGet** klickar du på **Bläddra**, och skriv **Microsoft.Azure.Graphs** i sökrutan och kontrollera den **är förhandsversioner**.
6. I resultatet hitta **Microsoft.Azure.Graphs** och på **installera**.
   
   Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.
   
    Den `Microsoft.Azure.Graphs` -bibliotek innehåller en enda metod `CreateGremlinQuery<T>` för att köra Gremlin åtgärder. Gremlin är en funktionell programmeringsspråk som stöder skriva åtgärder (DML) och fråga och traversal-åtgärder. Vi igenom några exempel i den här artikeln för att få din igång med Gremlin. [Gremlin frågor](gremlin-support.md) har en detaljerad genomgång av Gremlin funktioner i Azure Cosmos DB.

## <a id="add-references"></a>Anslut appen

Lägg till nedanstående två konstanter och dina *klienten* variabeln i ditt program. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Därefter head tillbaka till den [Azure-portalen](https://portal.azure.com) att hämta dina slutpunkts-URL och primärnyckel. Slutpunkts-URL:en och den primära nyckeln behövs för att programmet ska veta vart ditt program ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning. 

Navigera till ditt Azure DB som Cosmos-konto i Azure-portalen klickar du på **nycklar**, och klicka sedan på **skrivskyddad nycklar**. 

Kopiera URI: N från portalen och via `Endpoint` i slutpunktsegenskapen. Kopiera den PRIMÄRNYCKELN från portalen och klistrar in det i den `AuthKey` egenskapen ovan. 

![Skärmbild av Azure portal som används av kursen för att skapa ett C#-program. Visar en Cosmos-databas med Azure-konto knappen nycklar markerad i navigeringen till Azure Cosmos DB och värdena URI och PRIMÄRNYCKEL markerade i bladet nycklar](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Skapa en instans av DocumentClient 
Skapa sedan en ny instans av den **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Skapa en databas 

Nu skapa en Azure-Cosmos-DB [databasen](sql-api-resources.md#databases) med hjälp av den [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metod eller [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metod för den  **DocumentClient** klass från den [SQL .NET SDK](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Skapa ett diagram 

Därefter skapa en behållare för diagram med hjälp av den med hjälp av den [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metod eller [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metod för den **DocumentClient** klass. En samling är en behållare för diagram entiteter. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serialisera hörn och kanter till .NET-objekt
Använder Azure Cosmos-DB i [GraphSON kabelformat](gremlin-support.md), som definierar en JSON-schema för formhörnen, kanter och egenskaper. Azure Cosmos DB .NET SDK innehåller JSON.NET som ett beroende och detta gör att vi kan serialisering/deserialisering GraphSON i .NET-objekt som vi kan arbeta med kod.

Exempelvis vi arbeta med ett enkelt sociala nätverk med fyra personer. Vi titta på hur du skapar `Person` formhörnen, lägga till `Knows` relationer mellan dem, och sedan fråga och passerar i diagrammet för att söka efter ”vän vän” relationer. 

Den `Microsoft.Azure.Graphs.Elements` namnområde ger `Vertex`, `Edge`, `Property` och `VertexProperty` klasser för avserialisering av GraphSON svar väldefinierade .NET-objekt.

## <a name="run-gremlin-using-creategremlinquery"></a>Kör Gremlin med CreateGremlinQuery
Gremlin som SQL, stöder Läs-, Skriv- och frågor. Till exempel följande utdrag visar hur du skapar formhörnen kanter utföra vissa exempelfrågor som använder `CreateGremlinQuery<T>`, och asynkront gå igenom dessa resultat med hjälp av `ExecuteNextAsync` och ' HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Lägg till hörn och kanter

Nu ska vi titta på Gremlin-instruktioner som visas i föregående avsnitt detalj. Första vi vissa formhörnen med Gremlin's `addV` metod. Följande kodutdrag skapar en ”Thomas Andersen” nod av typen ”Person”, med egenskaper för förnamn, efternamn och ålder.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Vi skapa vissa kanter mellan dessa formhörnen med Gremlin's `addE` metod. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Vi kan uppdatera en befintlig vertex med `properties` steg i Gremlin. Vi hoppar över anropet för att köra frågan via `HasMoreResults` och `ExecuteNextAsync` för resten av exemplen.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Du kan släppa kanter och formhörnen med Gremlin's `drop` steg. Här är ett kodfragment som visar hur du tar bort en nod och en kant. Observera att släppa en nod utför borttagningsåtgärder associerade kanter.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Frågan i diagrammet

Du kan utföra frågor och traversals som också använder Gremlin. Till exempel visar följande utdrag hur du räkna antalet formhörnen i diagrammet:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Du kan utföra filter med Gremlin's `has` och `hasLabel` steg och kombinera dem med hjälp av `and`, `or`, och `not` att skapa fler komplexa filter:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Du kan projicera vissa egenskaper i frågeresultatet med hjälp av den `values` steg:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Hittills har sett vi endast frågeoperatorer som fungerar i en databas. Diagram är snabb och effektiv för traversal åtgärder när du behöver att navigera till relaterade kanter och formhörnen. Vi ska hitta alla vänner till Thomas. Vi kan göra detta med hjälp av Gremlin's `outE` steg för att söka efter alla de kanter ut från Thomas och sedan bläddra till de i formhörnen från dessa kanter med Gremlin's `inV` steg:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

Nästa fråga utför två hopp för att söka efter alla Thomas' ”vänner till vänner”, genom att anropa `outE` och `inV` två gånger. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Du kan skapa mer komplexa frågor och implementera kraftfulla diagrammet traversal logik med Gremlin, inklusive blanda filteruttryck, utför slingor med hjälp av den `loop` steg och implementera villkorlig navigering med hjälp av den `choose` steg. Mer information om vad du kan göra med [Gremlin stöd](gremlin-support.md)!

Det, självstudierna Azure Cosmos DB är klar! 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen.  

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Skapa ett Azure DB som Cosmos-konto 
> * Skapa ett diagram databas och en behållare
> * Serialiserade hörn och kanter till .NET-objekt
> * Tillagda hörn och kanter
> * Frågas diagrammet med Gremlin

Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)
