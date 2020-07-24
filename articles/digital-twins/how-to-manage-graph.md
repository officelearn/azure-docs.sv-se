---
title: Hantera tvillingdiagram med relationer
titleSuffix: Azure Digital Twins
description: Se hur du hanterar en graf med digitala dubbla, genom att ansluta dem till relationer.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f7239e0c13478af712d8e8d9dad8fda23fe42c7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125540"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Hantera en graf med digitala dubbla med relationer

Hjärtat i Azure Digitals flätas är det [dubbla diagrammet](concepts-twins-graph.md) som representerar hela miljön. Den dubbla grafen består av enskilda digitala dubbla anslutningar via **relationer**.

När du har en fungerande [Azure Digital-instansen](how-to-set-up-instance-scripted.md) och har konfigurerat [autentiserings](how-to-authenticate-client.md) kod i din klient app, kan du använda [**DigitalTwins-API: er**](how-to-use-apis-sdks.md) för att skapa, ändra och ta bort digitala dubbla och deras relationer i en digital Azure-instans. Du kan också använda [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)eller [Azure Digitals flätat CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera relationer och diagrammet som helhet. information om hur du arbetar med enskilda digitala dubbla, finns i [*så här gör du: hantera digitala dubbla*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Skapa relationer

Relationerna beskriver hur olika digitala anslutningar är anslutna till varandra, som utgör grunden för det dubbla diagrammet.

Relationer skapas med hjälp av `CreateRelationship` anropet. 

Om du vill skapa en relation måste du ange:
* Det dubbla käll-ID: t (den dubbla där relationen kommer)
* Målets dubbla ID (den dubbla där relationen anländer)
* Ett Relations namn
* Ett relations-ID

Relations-ID: t måste vara unikt inom den aktuella källan. Den behöver inte vara globalt unik.
Till exempel måste varje ID för en speciell relation vara unikt för den dubbla *foo*. En annan rad dubbla *staplar* kan dock ha en utgående relation som matchar samma ID för en *foo* -relation. 

Följande kod exempel visar hur du lägger till en relation till din Azure Digital-instansen.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Mer information om hjälp klassen `BasicRelationship` finns i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).

## <a name="list-relationships"></a>List relationer

Om du vill få åtkomst till listan över relationer för en specifik i grafen kan du använda:

```csharp
await client.GetRelationshipsAsync(id);
```

Detta returnerar en `Azure.Pageable<T>` eller `Azure.AsyncPageable<T>` , beroende på om du använder en synkron eller asynkron version av anropet.

Här är ett fullständigt exempel som hämtar en lista med relationer:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Du kan använda de hämtade relationerna för att navigera till andra dubbla i grafen. Det gör du genom att läsa `target` fältet från relationen som returneras och använda det som ID för nästa anrop till `GetDigitalTwin` . 

### <a name="find-relationships-to-a-digital-twin"></a>Hitta relationer till en digital, dubbel

Azure Digitals dubbla är också ett API för att hitta alla inkommande relationer till en specifik kontakt. Detta är ofta användbart för omvänd navigering, eller när du tar bort en dubbel.

Föregående kod exempel fokuserar på att hitta utgående relationer. Följande exempel är liknande, men hittar inkommande relationer i stället. Det tar också bort dem när de har hittats.

Observera att `IncomingRelationship` anropen inte returnerar hela bröd texten i relationen.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Ta bort relationer

Du kan ta bort relationer med `DeleteRelationship(source, relId);` .

Den första parametern anger den dubbla källan (den dubbla där relationen kommer). Den andra parametern är relations-ID. Du behöver både det dubbla ID: t och relations-ID: t eftersom relations-ID: n endast är unika inom omfånget för en dubbel.

## <a name="create-a-twin-graph"></a>Skapa ett dubbel diagram 

I följande kodfragment används Relations åtgärderna från den här artikeln för att skapa ett sammanflätat diagram från digitala dubbla och relationer.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Skapa ett dubbel diagram från ett kalkyl blad

I praktiska användnings fall kommer dubbla hierarkier ofta att skapas från data som lagras i en annan databas eller kanske i ett kalkyl blad. Det här avsnittet illustrerar hur ett kalkyl blad kan parsas.

Tänk på följande data tabell, som beskriver en uppsättning digitala dubbla och relationer som ska skapas.

| Modell    | ID | Överordnad | Relations namn | Andra data |
| --- | --- | --- | --- | --- |
| maximal    | Floor01 | | | … |
| rum    | Room10 | Floor01 | contains | … |
| rum    | Room11 | Floor01 | contains | … |
| rum    | Room12 | Floor01 | contains | … |
| maximal    | Floor02 | | | … |
| rum    | Room21 | Floor02 | contains | … |
| rum    | Room22 | Floor02 | contains | … |

I följande kod används [Microsoft Graph API](https://docs.microsoft.com/graph/overview) för att läsa ett kalkyl blad och skapa ett digitalt Azure-elektroniskt diagram från resultaten.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
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