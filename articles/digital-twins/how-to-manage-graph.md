---
title: Hantera tvillingdiagram med relationer
titleSuffix: Azure Digital Twins
description: Se hur du hanterar en graf med digitala dubbla, genom att ansluta dem till relationer.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 62deca7ed1c34bbefed7fb76224db6ec8ab12dae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147138"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Hantera en graf med digitala dubbla med relationer

Hjärtat i Azure Digitals flätas är det [dubbla diagrammet](concepts-twins-graph.md) som representerar hela miljön. Det dubbla grafen består av enskilda digitala dubbla anslutningar via **relationer** . 

När du har en fungerande [Azure Digital-instansen](how-to-set-up-instance-portal.md) och har konfigurerat [autentiserings](how-to-authenticate-client.md) kod i din klient app, kan du använda [**DigitalTwins-API: er**](/rest/api/digital-twins/dataplane/twins) för att skapa, ändra och ta bort digitala dubbla och deras relationer i en digital Azure-instans. Du kan också använda [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)eller [Azure Digitals flätat CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera relationer och diagrammet som helhet. information om hur du arbetar med enskilda digitala dubbla, finns i [*så här gör du: hantera digitala dubbla*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Skapa relationer

Relationerna beskriver hur olika digitala anslutningar är anslutna till varandra, som utgör grunden för det dubbla diagrammet.

Relationer skapas med hjälp av `CreateRelationship()` anropet. 

Om du vill skapa en relation måste du ange:
* Källans dubbla ID ( `srcId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Målets dubbla ID ( `targetId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Ett Relations namn ( `relName` i kod exemplet nedan): den generiska typen av relation, något som _innehåller_ .
* Ett relations-ID ( `relId` i kod exemplet nedan): det angivna namnet för den här relationen, t. ex. _Relationship1_ .

Relations-ID: t måste vara unikt inom den aktuella källan. Det behöver inte vara globalt unikt.
Till exempel måste varje ID för en speciell relation vara unikt för den dubbla *foo* . En annan rad dubbla *staplar* kan dock ha en utgående relation som matchar samma ID för en *foo* -relation.

Följande kod exempel illustrerar hur du skapar en relation i din Azure Digital-instansen.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
I din huvud metod kan du nu anropa `CreateRelationship()` funktionen för att skapa en _contains_ -relation så här: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Om du vill skapa flera relationer kan du upprepa anrop till samma metod och skicka olika Relations typer till argumentet. 

Mer information om hjälp klassen `BasicRelationship` finns i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Skapa flera relationer mellan dubbla

Relationer kan klassificeras som antingen: 

* Utgående relationer: relationer som hör till den här dubbla som punkten utåt för att ansluta den till andra dubbla. `GetRelationshipsAsync()`Metoden används för att hämta utgående relationer för en dubbel.
* Inkommande relationer: relationer som hör till andra dubbla platser som pekar mot den här dubbla för att skapa en "inkommande" länk. `GetIncomingRelationshipsAsync()`Metoden används för att hämta inkommande relationer av en dubbel.

Det finns ingen begränsning för antalet relationer som du kan ha mellan två dubbla, och du kan ha så många relationer som du vill. 

Det innebär att du kan uttrycka flera olika typer av relationer mellan två dubblas samtidigt. Till exempel kan *dubbla a* ha både en *lagrad* *relation och en* relation med *dubbla B* .

Du kan till och med skapa flera instanser av samma typ av relation mellan samma två dubbla, om du vill. I det här exemplet kan *dubbla A* ha två olika *lagrade* relationer med *dubbla B* , så länge relationerna har olika relations-ID.

## <a name="list-relationships"></a>List relationer

Om du vill få åtkomst till listan över **utgående** relationer för en specifik i grafen kan du använda `GetRelationships()` metoden som detta:

```csharp
await client.GetRelationships()
```

Detta returnerar en `Azure.Pageable<T>` eller `Azure.AsyncPageable<T>` , beroende på om du använder en synkron eller asynkron version av anropet.

Här är ett exempel som hämtar en lista med relationer:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Nu kan du anropa den här metoden för att se de utgående relationerna för de dubblarna så här:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Du kan använda de hämtade relationerna för att navigera till andra dubbla i grafen. Det gör du genom att läsa `target` fältet från relationen som returneras och använda det som ID för nästa anrop till `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Hitta inkommande relationer till en digital, dubbel

Azure Digitals dubbla är också ett API för att hitta alla _ *inkommande* *-relationer till en specifik dubbel. Detta är ofta användbart för omvänd navigering, eller när du tar bort en dubbel.

Föregående kod exempel fokuserade på att hitta utgående relationer från en dubbel. Följande exempel är strukturerat på samma sätt, men hittar *inkommande* relationer till den dubbla i stället.

Observera att `IncomingRelationship` anropen inte returnerar hela bröd texten i relationen.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Nu kan du anropa den här metoden för att se de ingående relationerna för de dubblarna så här:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Lista alla dubbla egenskaper och relationer

Med hjälp av ovanstående metoder för att visa utgående och inkommande relationer till en dubbel, kan du skapa en metod som skriver ut fullständig dubbel information, inklusive de dubbla egenskaperna och båda typerna av dess relationer. Här är en exempel metod som kallas `FetchAndPrintTwinAsync()` för att visa hur du gör detta.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Nu kan du anropa den här funktionen i din main-metod så här: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Ta bort relationer

Den första parametern anger den dubbla källan (den dubbla där relationen kommer). Den andra parametern är relations-ID. Du behöver både det dubbla ID: t och relations-ID: t eftersom relations-ID: n endast är unika inom omfånget för en dubbel.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Du kan nu anropa den här metoden för att ta bort en relation så här:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Skapa ett dubbel diagram 

Följande körbara-kodfragment använder Relations åtgärderna från den här artikeln för att skapa ett sammanflätat diagram från digitala dubbla och relationer.

### <a name="set-up-the-runnable-sample"></a>Konfigurera körbara-exemplet

Kodfragmentet använder [*Room.jspå*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) och [*Floor.jspå*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell definitioner från [*självstudien: utforska Azure Digitals med en exempel klient program*](tutorial-command-line-app.md). Du kan använda dessa länkar om du vill gå direkt till filerna eller ladda ned dem som en del av det fullständiga exempel projektet från början till slut [här](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Innan du kör exemplet gör du följande:
1. Ladda ned modell filerna, placera dem i projektet och Ersätt `<path-to>` plats hållarna i koden nedan för att tala om för programmet var de finns.
2. Ersätt plats hållaren `<your-instance-hostname>` med din Azure Digital-instansen värdnamn.
3. Lägg till de här paketen i projektet:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Du måste också konfigurera lokala autentiseringsuppgifter om du vill köra exemplet direkt. Nästa avsnitt går igenom detta.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Kör exemplet

När du har slutfört ovanstående steg kan du köra följande exempel kod direkt.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Här är konsol resultatet av programmet ovan: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konsol utdata som visar den dubbla informationen, inkommande och utgående relationer för de dubbla." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Det dubbla diagrammet är ett koncept för att skapa relationer mellan dubbla. Om du vill visa den visuella representationen av den dubbla grafen, se avsnittet [_Visualization *](how-to-manage-graph.md#visualization) i den här artikeln. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Skapa ett dubbel diagram från ett kalkyl blad

I praktiska användnings fall kommer dubbla hierarkier ofta att skapas från data som lagras i en annan databas eller kanske i ett kalkyl blad. Det här avsnittet illustrerar hur ett kalkyl blad kan parsas.

Tänk på följande data tabell, som beskriver en uppsättning digitala dubbla och relationer som ska skapas.

| Modell-ID| Dubbla ID (måste vara unikt) | Relations namn | Mål, dubbla ID | Dubbla init-data |
| --- | --- | --- | --- | --- |
| dtmi: exempel: våning; 1 | Floor1 |  contains | Room1 |{"Temperatur": 80, "fuktighet": 60}
| dtmi: exempel: våning; 1 | Floor0 |  hade      | Room0 |{"Temperatur": 70, "fuktighet": 30}
| dtmi: exempel: Room; 1  | Room1 | 
| dtmi: exempel: Room; 1  | Room0 |

I följande kod används [Microsoft Graph API](/graph/overview) för att läsa ett kalkyl blad och skapa ett digitalt Azure-elektroniskt diagram från resultaten.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Contents = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(sourceId, twin);
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateOrReplaceRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), rec);
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Hantera relationer med CLI

Dubbla och deras relationer kan också hanteras med hjälp av Azure Digitals flätade CLI. Kommandona finns i [*anvisningar: använda Azure Digitals flätade CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skicka frågor till en Azure Digitals dubbla graf:
* [*Begrepp: frågespråk*](concepts-query-language.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)