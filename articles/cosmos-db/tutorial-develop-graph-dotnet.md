---
title: 'Azure Cosmos DB: Utveckla med Graph API i .NET | Microsoft Docs'
description: Lär dig utveckla med Azure Cosmos DB:s SQL API med .NET
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1843e37d9baf1ab264db96109eb5ffd0704e35b7
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271297"
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Utveckla med Graph API i .NET
Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här självstudien visar hur du skapar ett Azure Cosmos DB-konto med hjälp av Azure Portal, samt hur du skapar en grafdatabas och behållare. Programmet skapar sedan ett enkelt socialt nätverk med fyra personer med hjälp av [Graph API](graph-sdk-dotnet.md). Därefter går det vidare och frågar i grafen med hjälp av Gremlin.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Skapa en grafdatabas och en behållare
> * Serialisera hörn och kanter till .NET-objekt
> * Lägga till hörn och kanter
> * Fråga i grafen med Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Grafer i Azure Cosmos DB
Du kan använda Azure Cosmos DB till att skapa, uppdatera och fråga i grafer med hjälp av biblioteket [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). Microsoft.Azure.Graph-biblioteket innehåller en enda tilläggsmetod `CreateGremlinQuery<T>` ovanpå `DocumentClient`- klassen för att kunna köra Gremlin-frågor.

Gremlin är ett funktionellt datorspråk som stöder skrivåtgärder (DML) samt frågor och bläddring. Vi visar några exempel i den här artikeln för att du ska komma igång med Gremlin. Se [Gremlin-frågor](gremlin-support.md) för en detaljerad genomgång av Gremlin-funktionerna i Azure Cosmos DB. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 
    * Du kan också använda [den lokala emulatorn](local-emulator.md) i den här självstudien.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Skapa ett databaskonto

Vi ska börja med att skapa ett Azure Cosmos DB-konto i Azure-portalen.  

> [!TIP]
> * Har du redan ett Azure Cosmos DB-konto? I sådana fall kan du hoppa vidare till [Konfigurera din Visual Studio-lösning](#SetupVS)
> * Om du använder Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och hoppa vidare till [Konfigurera din Visual Studio-lösning](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogen **Nytt projekt**, väljer du **Mallar** / **Visual C#** / **Konsolapp (.NET Framework)**. Namnge ditt projekt och klicka sedan på **OK**.
4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**
5. På fliken **NuGet** klickar du på **Bläddra** och skriver **Microsoft.Azure.Graphs** i sökrutan. Markera alternativet för att **ta med förhandsutgåvor**.
6. Leta reda på **Microsoft.Azure.Graphs** i resultatet och klicka på **Installera**.
   
   Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.
   
    Biblioteket `Microsoft.Azure.Graphs` innehåller enbart tilläggsmetoden `CreateGremlinQuery<T>` för att köra Gremlin-åtgärder. Gremlin är ett funktionellt datorspråk som stöder skrivåtgärder (DML) samt frågor och bläddring. Vi visar några exempel i den här artikeln för att du ska komma igång med Gremlin. [Gremlin-frågor](gremlin-support.md) innehåller en detaljerad genomgång av Gremlin-funktionerna i Azure Cosmos DB.

## <a id="add-references"></a>Anslut din app

Lägg till de här två konstanterna och din *klient*variabel i programmet. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta slutpunkts-URL:en och primärnyckeln. Slutpunkts-URL:en och den primära nyckeln behövs för att programmet ska veta vart ditt program ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning. 

I Azure Portal, går du till ditt Azure Cosmos DB-konto, klickar på **Nycklar** och klickar sedan på **Läs-skriv nycklar**. 

Kopiera URI:n från portalen och klistra in den över `Endpoint` i slutpunktsegenskapen ovan. Kopiera sedan PRIMÄRNYCKEL från portalen och klistra in den i `AuthKey`-egenskapen ovan. 

![Skärmbild av Azure Portal som används i självstudien för att skapa ett C#-program. Visar ett Azure Cosmos DB-konto med knappen NYCKLAR markerad i Azure Cosmos DB-navigeringen, och där värdena URI och PRIMÄRNYCKEL är markerade på blader Nycklar](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Skapa en instans av DocumentClient 
Skapa nu en ny instans av **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Skapa en databas 

Nu ska du skapa en Azure Cosmos DB-[databas](sql-api-resources.md#databases) med metoderna [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) eller [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) i klassen **DocumentClient** från [.NET-SDK:n i SQL](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Skapa en graf 

Nu ska du skapa en grafbehållare med hjälp av metoderna [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) eller [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) i klassen **DocumentClient**. En samling är en behållare med grafentiteter. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 400 }); 
``` 

## <a id="serializing"></a>Serialisera hörn och kanter till .NET-objekt
Azure Cosmos DB använder ett [GraphSON-kabelformat](gremlin-support.md), som definierar ett JSON-schema för hörn, kanter och egenskaper. Azure Cosmos DB .NET SDK innehåller JSON.NET som ett beroende. Detta innebär att vi kan serialisera/deserialisera GraphSON till .NET-objekt där vi kan arbeta med kod.

Vi kan exempelvis arbeta med ett enkelt socialt nätverk med fyra personer. Vi tittar på hur man skapar `Person`-hörn, lägger till `Knows`-relationer mellan dem och frågar sedan samt går vidare i grafen för att hitta ”vän-till-vän”-relationer. 

Namnområdet `Microsoft.Azure.Graphs.Elements` ger klasserna `Vertex`, `Edge`, `Property` och `VertexProperty` för deserialisering av GraphSON-svar på väldefinierade .NET-objekt.

## <a name="run-gremlin-using-creategremlinquery"></a>Köra Gremlin med CreateGremlinQuery
Gremlin, precis som SQL, stöder läs-, skriv- och frågeåtgärder. Till exempel visar följande kodfragment hur du skapar hörn, kanter, utför vissa exempelfrågor med `CreateGremlinQuery<T>`, samt asynkront itererar dessa resultat med hjälp av `ExecuteNextAsync` och `HasMoreResults`.

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

## <a name="add-vertices-and-edges"></a>Lägga till hörn och kanter

Nu ska vi titta mer detaljerat på de Gremlin-instruktioner som visades i föregående avsnitt. Först har vi några hörn där Gremlins `addV`-metod används. Följande kodfragment skapar till exempel ett ”Thomas Andersen”-hörn av typen ”person”, med egenskaper för förnamn och ålder.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas').property('age', 44)");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Vi skapar sedan några kanter mellan dessa hörn med hjälp av Gremlins `addE`-metod. 

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

Vi kan uppdatera ett befintligt hörn med steget `properties` i Gremlin. Vi hoppar över anropet för att köra frågan via `HasMoreResults` och `ExecuteNextAsync` i resten av exemplen.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Du kan släppa kanter och hörn med Gremlins `drop`-steg. Här är ett kodfragment som visar hur du tar bort ett hörn och en kant. Observera att släppa ett hörn utför en sammanhängande borttagning av de associerade kanterna.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Fråga i grafen

Du kan även utföra frågor och bläddringar med Gremlin. Följande kodfragment visar hur du räknar antalet hörn i grafen:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Du kan använda filter med Gremlins `has`- och `hasLabel`-steg, samt kombinera dem med hjälp av `and`, `or` och `not` för att skapa mer komplexa filter:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Du kan projicera vissa egenskaper i frågeresultaten med hjälp av steget `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Hittills har vi endast sett frågeoperatorer som fungerar i alla databaser. Diagram är snabba och effektiva vid bläddringsåtgärder när du behöver navigera till relaterade kanter och hörn. Nu ska vi hitta alla Thomas vänner. Vi kan göra detta med hjälp av Gremlins `outE`-steg för att söka efter alla ut-kanter från Thomas och sedan bläddra till in-hörnen från dessa kanter med Gremlins `inV`-steg:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

Nästa fråga utför två hopp för att hitta alla Thomas ”vänner till vänner”, genom att anropa `outE` och `inV` två gånger. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Du kan skapa mer komplexa frågor och implementera kraftfull bläddringslogik i diagrammet med Gremlin, inklusive att blanda filteruttryck, utföra loopar med hjälp av `loop`-steget och implementera villkorlig navigering med hjälp av `choose`-steget. Läs mer om vad du kan göra med [Gremlin-support](gremlin-support.md)!

Det var allt – självstudien för Azure Cosmos DB är klar! 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen.  

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Skapat ett Azure Cosmos DB-konto 
> * Skapat en grafdatabas och en behållare
> * Serialiserat hörn och kanter till .NET-objekt
> * Lagt till hörn och kanter
> * Frågat i grafen med Gremlin

Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)
