---
title: Varaktiga entiteter – Azure Functions
description: Lär dig mer om vad är varaktiga entiteter och hur du använder dem i Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 864a641968268c439c65996998cbb822746b96f9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839004"
---
# <a name="entity-functions-preview"></a>Enhets funktioner (förhands granskning)

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd,så kallade varaktiga entiteter. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskildutlösnings typ, enhets utlösare. Till skillnad från Orchestrator-funktioner har entiteter-funktioner inte några speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

> [!NOTE]
> Enhets funktioner och relaterade funktioner är bara tillgängliga i Durable Functions 2,0 och senare. Enhets funktioner finns för närvarande i offentlig för hands version.

## <a name="entity-identity"></a>Enhets identitet

Entiteter (kallas ibland enhets *instanser*) nås via en unik identifierare, *entitets-ID*. Ett entitets-ID är bara ett par med strängar som unikt identifierar en enhets instans. Det består av:

* Ett **entitetsnamn**: ett namn som identifierar entitetens typ (till exempel "Counter").
* En **enhets nyckel**: en sträng som unikt identifierar entiteten bland alla andra entiteter av samma namn (till exempel ett GUID).

En *Counter* -enhets funktion kan till exempel användas för att hålla poängen i ett onlinespel. Varje instans av spelet har ett unikt entitets-ID, t. `@Counter@Game1`ex `@Counter@Game2`., och så vidare. Alla åtgärder som är riktade till en viss entitet kräver att du anger ett entitets-ID som en parameter.

## <a name="programming-models"></a>Programmerings modeller

Varaktiga entiteter har stöd för två olika programmerings modeller. Den första modellen är en dynamisk "funktionell"-modell där entiteten definieras av en enda funktion. Den andra modellen är en objektorienterad modell där entiteten definieras av en klass och metoder. Dessa modeller och programmerings modeller för att interagera med entiteter beskrivs i nästa avsnitt.

### <a name="defining-entities"></a>Definiera entiteter

Det finns två valfria programmerings modeller för att redigera varaktiga entiteter. Följande kod är ett exempel på en enkel *Counter* -entitet som implementeras som en standard funktion. Den här funktionen definierartre åtgärder `add`, `reset`, och `get`, som körs på ett heltals tillstånds värde `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Den här modellen fungerar bäst för enkla enhets implementeringar eller implementeringar som har en dynamisk uppsättning åtgärder. Du kan dock också använda en klassbaserade programmerings modell som är användbar för entiteter som är statiska men har mer komplexa implementeringar. Följande exempel är en motsvarande implementering av `Counter` entiteten med hjälp av klasser och metoder.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Funktionens start punkt metod med `[FunctionName]` attributet *måste* deklareras `static` när du använder entitets klasser. Metoder som inte är statiska kan leda till att flera objekt initieras och potentiellt andra odefinierade beteenden.

I den klassbaserade programmerings modellen `IDurableEntityContext` är objektet tillgängligt i den `Entity.Current` statiska egenskapen.

Den klassbaserade modellen liknar programmerings modellen som är populär av [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). I den här modellen definieras en entitetstyp som en .NET-klass. Varje metod i klassen är en åtgärd som kan anropas av en extern klient. Till skillnad från Orleans är .NET-gränssnitt valfria. I föregående *räknar* exempel används inget gränssnitt, men det kan fortfarande anropas via andra funktioner eller via HTTP API-anrop.

> [!NOTE]
> Funktionerna för enhets utlösare är tillgängliga i Durable Functions 2,0 och senare. För närvarande är enhets utlösare endast tillgängliga för .NET Function-appar.

### <a name="accessing-entities-from-clients"></a>Åtkomst till entiteter från klienter

Varaktiga entiteter kan anropas eller efter frågas från vanliga funktioner, även kallade *klient funktioner* , med hjälp av [utgående bindning för enhets klienten](durable-functions-bindings.md#entity-client). I följande exempel visas en köade funktion som *signalerar* en entitet som använder den här bindningen.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> .NET Functions har stöd för både löst skrivna och typ säkra metoder för att signalera entiteter. Mer information finns i referens dokumentationen för [enhets klient bindning](durable-functions-bindings.md#entity-client-usage) .

Termen *signal* innebär att entitets-API-anropet är enkelriktat och asynkront. Det är inte möjligt för en *klient funktion* att veta när enheten har bearbetat åtgärden, eller så är det möjligt att en enhets funktion returnerar ett värde till en klient funktion. Enkelriktad meddelande tjänst var ett avsiktligt design val för varaktiga enheter som prioriterar hållbarhet över prestanda. Det här design valet är en av fördelarna med varaktiga enheter jämfört med andra liknande tekniker. För närvarande kan endast dirigerade värden hantera retur värden från entiteter, enligt beskrivningen i nästa avsnitt.

Klient funktioner kan också fråga efter status för entiteter, som visas i följande exempel:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

Frågor för enhets tillstånd skickas till lagrings platsen för beständig spårning och returnerar entitetens senast *sparade* tillstånd. Det är möjligt att det returnerade tillståndet kan vara inaktuellt jämfört med enhetens minnes tillstånd. Endast dirigering kan läsa en entitets minnes intern tillstånd enligt beskrivningen i följande avsnitt.

### <a name="accessing-entities-from-orchestrations"></a>Åtkomst till entiteter från dirigering

Orchestrator-funktioner har åtkomst till entiteter med hjälp av API: er i [bindningen för dirigering](durable-functions-bindings.md#orchestration-trigger) Orchestrator-funktioner kan välja mellan enkelriktad kommunikation (eld och glömma, även kallat *signalering*) och tvåvägs kommunikation (begäran och svar, även kallat *anrop*). Följande exempel kod visar en Orchestrator-funktion som *anropar* och *signalerar* en *Counter* -entitet.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value - will await a response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value - will not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Endast dirigering kan anropa entiteter och få svar, vilket kan vara antingen ett retur värde eller ett undantag. Klient funktioner som använder [klient bindningen](durable-functions-bindings.md#entity-client) kan bara *signalerar* entiteter.

> [!NOTE]
> Anrop av en entitet från en orchestrtor-funktion liknar att anropa en [aktivitets funktion](durable-functions-types-features-overview.md#activity-functions) från en Orchestrator-funktion. Den största skillnaden är att entitets funktioner är beständiga objekt med en adress ( *entitets-ID*) och de stöder att du anger ett åtgärds namn. Aktivitets funktioner, å andra sidan, är tillstånds lösa och har inte begreppet åtgärder.

### <a name="dependency-injection-in-entity-classes-net"></a>Beroende inmatning i entitets klasser (.NET)

Entitets klasser stöder [Azure Functions beroende insprutning](../functions-dotnet-dependency-injection.md). Följande exempel visar hur du registrerar en `IHttpClientFactory` tjänst i en klass-baserad entitet.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Följande kodfragment visar hur du införlivar den inmatade tjänsten i enhets klassen.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Till skillnad från när du använder konstruktorn för att använda konstruktorn i vanliga .NET-Azure Functions, *måste* funktions plats metoden Functions för klassbaserade entiteter deklareras `static`. Om du deklarerar en icke-statisk funktions start punkt kan det orsaka konflikter mellan den normala Azure Functions objekt initieraren och den varaktiga entitetens objekt initierare.

### <a name="bindings-in-entity-classes-net"></a>Bindningar i entitets klasser (.NET)

Till skillnad från vanliga funktioner har enhets klass metoder inte direkt åtkomst till indata-och utgående bindningar. I stället måste bindnings data samlas in i deklarationen för ingångs punkt funktionen och sedan `DispatchAsync<T>` skickas till-metoden. Alla objekt som skickas `DispatchAsync<T>` till skickas automatiskt till konstruktorn för entity-klassen som ett argument.

I följande exempel visas hur en `CloudBlobContainer` referens från [BLOB-databindningen](../functions-bindings-storage-blob.md#input) kan göras tillgänglig för en klass baserad entitet.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Mer information om bindningar i Azure Functions finns i dokumentationen för [Azure Functions-utlösare och bindningar](../functions-triggers-bindings.md) .

## <a name="entity-coordination"></a>Organisations samordning

Det kan finnas tillfällen när du behöver koordinera åtgärder över flera entiteter. I ett bank program kan du till exempel ha entiteter som representerar enskilda bank konton. När du överför betalningar från ett konto till ett annat, måste du se till att _käll_ kontot har tillräckligt med belopp och att uppdateringarna på både _käll_ -och _mål_ kontona görs på ett konsekvent sätt.

### <a name="transfer-funds-example-in-c"></a>Exempel på överförings fonder iC#

I följande exempel kod överförs fonder mellan två _konto_ enheter med en Orchestrator-funktion. Koordinerande enhets uppdateringar kräver att `LockAsync` du använder metoden för att skapa ett _kritiskt avsnitt_ i dirigeringen:

> [!NOTE]
> För enkelhetens skull återanvänder det `Counter` här exemplet den entitet som definierats tidigare. I ett verkligt program är det dock bättre att i stället definiera en mer detaljerad `BankAccount` entitet.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

I .net `LockAsync` returnerar ett `IDisposable` som avslutar det kritiska avsnittet när det tas bort. Det `IDisposable` här resultatet kan användas tillsammans med ett `using` block för att få en syntaktisk representation av det kritiska avsnittet.

I föregående exempel har en Orchestrator-funktion överfört fonder från en _källentiteten_ till en _målentitet_ . Metoden låser både _käll_ -och mål kontots entiteter. `LockAsync` Den här låsningen säkerställer att ingen annan klient kan fråga eller ändra status för något av kontona tills Orchestration-logiken avbröt det _kritiska avsnittet_ i slutet av `using` instruktionen. Detta förhindrade möjligheten att överformulera från _käll_ kontot.

### <a name="critical-section-behavior"></a>Beteende för kritiskt avsnitt

Metoden skapar ett _kritiskt avsnitt_ i en dirigering. `LockAsync` Dessa _kritiska avsnitt_ förhindrar andra dirigeringar från att göra överlappande ändringar till en angiven uppsättning entiteter. Internt skickar `LockAsync` API: et för att skicka "lås"-åtgärder till entiteterna och returnerar när de får ett meddelande om "Lås förvärvad" från var och en av dessa entiteter. Både *Lås* och *upplåsning* är inbyggda åtgärder som stöds av alla entiteter.

Inga åtgärder från andra klienter tillåts för en entitet medan den är låst. Det här beteendet säkerställer att endast en Dirigerings instans kan låsa en entitet i taget. Om en anropare försöker anropa en åtgärd på en entitet när den har låsts av ett dirigering, placeras den åtgärden i en *kö för väntande åtgärder*. Inga väntande åtgärder kommer att bearbetas förrän innehavaren har låst det.

> [!NOTE] 
> Detta skiljer sig något från primitiva primitiver som används i de flesta programmeringsspråk, till `lock` exempel instruktionen i. C# I C#, `lock` till exempel, måste uttrycket användas av alla trådar för att säkerställa korrekt synchonization över flera trådar. Entiteter kräver dock inte att alla anropare uttryckligen _låser_ en entitet. Om någon anropare låser en entitet kommer alla andra åtgärder på den entiteten att blockeras och placeras bakom det låset.

Lås på entiteter är varaktiga, så de förblir kvar även om processen som körs återvinns. Lås är internt bestående som en del av en enhets varaktiga tillstånd.

### <a name="critical-section-restrictions"></a>Begränsningar för kritiska avsnitt

Vi tillämpar flera begränsningar för hur viktiga avsnitt kan användas. Dessa begränsningar är till för att förhindra död lägen och återinträde.

* Det går inte att kapsla kritiska avsnitt.
* Det går inte att skapa under dirigering med kritiska avsnitt.
* Kritiska avsnitt kan bara anropa entiteter som de har låst.
* Kritiska avsnitt kan inte anropa samma entitet med flera parallella anrop.
* Kritiska avsnitt kan endast signalera entiteter som de inte har låst.

## <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Många av de varaktiga entiteternas funktioner inspireras av [aktörs modellen](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med aktörer kan du känna igen många av de begrepp som beskrivs i den här artikeln. I synnerhet är varaktiga entiteter snarlika [virtuella aktörer](https://research.microsoft.com/projects/orleans/) på många sätt:

* Varaktiga entiteter kan adresseras via ett *entitets-ID*.
* Varaktiga enhets åtgärder körs seriellt, en i taget, för att förhindra tävlings förhållanden.
* Varaktiga entiteter skapas automatiskt när de anropas eller signaleras.
* När åtgärder inte körs inaktive ras varaktiga entiteter tyst från minnet.

Det finns dock några viktiga skillnader, men det är värt att notera:

* Varaktiga enheter prioriterar *hållbarhet* över *svars tider*och kan därför vara lämpligt för program med strikt latens krav.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i rätt ordning.
* Varaktiga entiteter kan användas tillsammans med varaktiga dirigeringar och stöd för distribuerade Lås metoder.
* Mönster för begäran/svar i entiteter är begränsade till dirigering. För kommunikation mellan *klienter* *och entiteter* (kallas även "signalering") tillåts endast enkelriktad meddelande hantering, som i den ursprungliga aktörs modellen. Det här beteendet förhindrar distribuerade död lägen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om aktivitets nav](durable-functions-task-hubs.md)
