---
title: Varaktiga entiteter – Azure-funktioner
description: Lär dig vilka varaktiga entiteter är och hur du använder dem i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877550"
---
# <a name="entity-functions"></a>Funktioner för entitet

Entitetsfunktioner definierar åtgärder för läsning och uppdatering av små delar av tillståndet, så kallade *varaktiga entiteter*. Precis som orchestrator-funktioner är entitetsfunktioner funktioner med en speciell utlösartyp, *entitetens utlösare*. Till skillnad från orchestrator-funktioner hanterar entitetsfunktioner tillståndet för en entitet explicit, i stället för att implicit representera tillstånd via kontrollflödet.
Entiteter är ett sätt att skala ut program genom att distribuera arbetet över många entiteter, var och en med ett tillstånd av blygsam storlek.

> [!NOTE]
> Entitetsfunktioner och relaterade funktioner är endast tillgängliga i Varaktiga funktioner 2.0 och högre.

## <a name="general-concepts"></a>Allmänna begrepp

Entiteter beter sig lite som små tjänster som kommunicerar via meddelanden. Varje entitet har en unik identitet och ett internt tillstånd (om det finns). Precis som tjänster eller objekt utför entiteter åtgärder när de uppmanas att göra det. När en åtgärd körs kan den uppdatera entitetens interna tillstånd. Det kan också ringa externa tjänster och vänta på ett svar. Entiteter kommunicerar med andra entiteter, orkestreringar och klienter med hjälp av meddelanden som implicit skickas via tillförlitliga köer. 

För att förhindra konflikter garanteras alla åtgärder på en enda entitet att köras seriellt, det vill än en efter en. 

### <a name="entity-id"></a>Enhets-ID
Entiteter nås via en unik identifierare, *entitets-ID.* Ett entitets-ID är helt enkelt ett par strängar som unikt identifierar en entitetsinstans. Den består av en:

* **Entitetsnamn**, vilket är ett namn som identifierar typen av entitet. Ett exempel är "Räknare". Det här namnet måste matcha namnet på entitetsfunktionen som implementerar entiteten. Det är inte känsligt för fallet.
* **Entitetsnyckel**, som är en sträng som unikt identifierar entiteten bland alla andra entiteter med samma namn. Ett exempel är ett GUID.

En entitetsfunktion `Counter` kan till exempel användas för att hålla poäng i ett onlinespel. Varje instans av spelet har ett unikt `@Counter@Game1` entitets-ID, till exempel och `@Counter@Game2`. Alla operationer som är inriktade på en viss entitet kräver att du anger ett entitets-ID som en parameter.

### <a name="entity-operations"></a>Entitetsåtgärder ###

Om du vill anropa en operation på en entitet anger du:

* **Entitets-ID** för målentiteten.
* **Åtgärdsnamn**, som är en sträng som anger vilken åtgärd som ska utföras. Entiteten kan till `add` `get`exempel `reset` stödja , eller operationer. `Counter`
* **Indata för drift**, som är en valfri indataparameter för åtgärden. Tilläggsåtgärden kan till exempel ta ett heltal som indata.
* **Schemalagd tid**, som är en valfri parameter för att ange leveranstiden för operationen. En åtgärd kan till exempel schemaläggas på ett tillförlitligt sätt för att köras flera dagar i framtiden.

Åtgärder kan returnera ett resultatvärde eller ett felresultat, till exempel ett JavaScript-fel eller ett .NET-undantag. Det här resultatet eller felet kan observeras av orkestreringar som anropade åtgärden.

En entitetsåtgärd kan också skapa, läsa, uppdatera och ta bort tillståndet för entiteten. Tillståndet för entiteten är alltid durably kvar i lagring.

## <a name="define-entities"></a>Definiera entiteter

För närvarande är de två distinkta API:erna för att definiera entiteter:

**Funktionsbaserad syntax**, där entiteter representeras som funktioner och operationer skickas uttryckligen av programmet. Den här syntaxen fungerar bra för entiteter med enkelt tillstånd, få åtgärder eller en dynamisk uppsättning åtgärder som i programramverk. Den här syntaxen kan vara tråkig att underhålla eftersom den inte fångar typfel vid kompileringstillfället.

**Klassbaserad syntax (.NET endast)**, där entiteter och operationer representeras av klasser och metoder. Den här syntaxen ger mer lättläst kod och gör att åtgärder kan anropas på ett typsäkert sätt. Den klassbaserade syntaxen är ett tunt lager ovanpå den funktionsbaserade syntaxen, så båda varianterna kan användas omväxlande i samma program.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Exempel: Funktionsbaserad syntax - C #

Följande kod är ett exempel `Counter` på en enkel entitet som implementeras som en varaktig funktion. Den här funktionen definierar `reset`tre `get`operationer, `add`, och , som var och en fungerar på ett heltalstillstånd.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Mer information om den funktionsbaserade syntaxen och hur du använder den finns i [Funktionsbaserad syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exempel: Klassbaserad syntax - C #

Följande exempel är en motsvarande `Counter` implementering av entiteten med hjälp av klasser och metoder.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Tillståndet för den här entiteten är ett objekt av typen `Counter`, som innehåller ett fält som lagrar det aktuella värdet för räknaren. Om du vill behålla objektet i lagringen serialiseras det [Json.NET](https://www.newtonsoft.com/json) och avserialiseras det av Json.NET-biblioteket. 

Mer information om den klassbaserade syntaxen och hur du använder den finns i [Definiera entitetsklasser](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Exempel: JavaScript-enhet

Varaktiga entiteter är tillgängliga i JavaScript som börjar `durable-functions` med version **1.3.0** av npm-paketet. Följande kod är `Counter` den enhet som implementeras som en varaktig funktion skriven i JavaScript.

**Räknare/funktion.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Räknare/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

## <a name="access-entities"></a>Åtkomstentiteter

Entiteter kan nås med hjälp av enkelriktad eller dubbelriktad kommunikation. Följande terminologi skiljer de två kommunikationsformerna mellan olika former: 

* **Att anropa** en entitet använder tvåvägskommunikation (tur och retur). Du skickar ett åtgärdsmeddelande till entiteten och väntar sedan på svarsmeddelandet innan du fortsätter. Svarsmeddelandet kan ge ett resultatvärde eller ett felresultat, till exempel ett JavaScript-fel eller ett .NET-undantag. Detta resultat eller fel observeras sedan av den som ringer.
* **Signalering** en enhet använder enkelriktad (brand och glömma) kommunikation. Du skickar ett åtgärdsmeddelande men väntar inte på ett svar. Även om meddelandet garanterat levereras så småningom vet avsändaren inte när och kan inte observera något resultatvärde eller fel.

Entiteter kan nås inifrån klientfunktioner, inifrån orchestrator-funktioner eller inifrån entitetsfunktioner. Alla kommunikationsformer stöds inte av alla sammanhang:

* Inifrån klienter kan du signalera entiteter och du kan läsa entitetstillståndet.
* Inifrån orkestreringar kan du signalera entiteter och du kan anropa entiteter.
* Inifrån entiteter kan du signalera entiteter.

Följande exempel illustrerar dessa olika sätt att komma åt entiteter.

### <a name="example-client-signals-an-entity"></a>Exempel: Klienten signalerar en entitet

Om du vill komma åt entiteter från en vanlig Azure-funktion, som också kallas klientfunktion, använder du [entitetsklientbindningen](durable-functions-bindings.md#entity-client). I följande exempel visas en köutlöst funktion som signalerar en entitet med den här bindningen.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> För enkelhetens skull visar följande exempel den löst skrivna syntaxen för åtkomst till entiteter. I allmänhet rekommenderar vi att du [kommer åt entiteter via gränssnitt](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) eftersom det ger fler typkontroll.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

Termen *signal* innebär att entiteten API-anrop är enkelriktad och asynkron. Det är inte möjligt för en klientfunktion att veta när entiteten har bearbetat operationen. Klientfunktionen kan inte heller observera några resultatvärden eller undantag. 

### <a name="example-client-reads-an-entity-state"></a>Exempel: Klienten läser ett entitetstillstånd

Klientfunktioner kan också fråga tillståndet för en entitet, vilket visas i följande exempel:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Entitetstillståndsfrågor skickas till det varaktiga spårningsarkivet och returnerar entitetens senast bevarade tillstånd. Detta tillstånd är alltid en "engagerad" tillstånd, det vill säga det är aldrig ett tillfälligt mellanliggande tillstånd antas i mitten av att utföra en åtgärd. Det är dock möjligt att det här tillståndet är inaktuellt jämfört med entitetens minnestillstånd. Endast orkestreringar kan läsa en entitets minnestillstånd, enligt beskrivningen i följande avsnitt.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exempel: Orkestrering signalerar och anropar en entitet

Orchestrator-funktioner kan komma åt entiteter med hjälp av API:er på [orchestration-utlösarbindningen](durable-functions-bindings.md#orchestration-trigger). Följande exempelkod visar en orchestrator-funktion `Counter` som anropar och signalerar en entitet.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript stöder för närvarande inte signalering av en entitet från en orchestrator. Använd `callEntity` i stället.

---

Endast orkestreringar kan anropa entiteter och få ett svar, vilket kan vara antingen ett returvärde eller ett undantag. Klientfunktioner som använder [klientbindningen](durable-functions-bindings.md#entity-client) kan bara signalera entiteter.

> [!NOTE]
> Att anropa en entitet från en orchestrator-funktion liknar att anropa en [aktivitetsfunktion](durable-functions-types-features-overview.md#activity-functions) från en orchestrator-funktion. Den största skillnaden är att entitetsfunktioner är varaktiga objekt med en adress, vilket är entitets-ID. Entitetsfunktioner stöder att ange ett operationsnamn. Aktivitetsfunktioner, å andra sidan, är statslösa och har inte begreppet verksamhet.

### <a name="example-entity-signals-an-entity"></a>Exempel: Entitet signalerar en entitet

En entitetsfunktion kan skicka signaler till andra entiteter, eller till och med sig själv, medan den utför en åtgärd.
Vi kan till exempel `Counter` ändra föregående entitetsexempel så att det skickar en "milstolpe nådd" signal till en viss övervakarentitet när räknaren når värdet 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Enhetssamordning (endast för närvarande .NET)

Det kan finnas tillfällen när du behöver samordna åtgärder över flera entiteter. I ett bankprogram kan du till exempel ha entiteter som representerar enskilda bankkonton. När du överför pengar från ett konto till ett annat måste du se till att källkontot har tillräckliga medel. Du måste också se till att uppdateringar av både käll- och målkonton görs på ett transaktionsmässigt konsekvent sätt.

### <a name="example-transfer-funds-c"></a>Exempel: Överför medel (C#)

I följande exempel överför kod medel mellan två kontoentiteter med hjälp av en orchestrator-funktion. Samordning av entitetsuppdateringar kräver att du använder `LockAsync` metoden för att skapa ett kritiskt _avsnitt_ i orkestreringen.

> [!NOTE]
> För enkelhetens skull återanvänder det här exemplet den `Counter` entitet som definierats tidigare. I ett verkligt program skulle det vara `BankAccount` bättre att definiera en mer detaljerad enhet.

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

I .NET `LockAsync` `IDisposable`returnerar , vilket avslutar det kritiska avsnittet när det kasseras. Detta `IDisposable` resultat kan användas `using` tillsammans med ett block för att få en syntaktisk representation av det kritiska avsnittet.

I föregående exempel överförde en orchestrator-funktion medel från en källentitet till en målentitet. Metoden `LockAsync` låste både käll- och målkontotiteterna. Den här låsningen säkerställde att ingen annan klient kunde fråga eller ändra tillståndet för något `using` av kontona tills orchestration-logiken avslutade det kritiska avsnittet i slutet av satsen. Detta förhindrar möjligheten att checka över från källkontot.

> [!NOTE] 
> När en orkestrering avslutas, antingen normalt eller med ett fel, avslutas alla kritiska avsnitt implicit och alla lås släpps.

### <a name="critical-section-behavior"></a>Kritiskt avsnittsbeteende

Metoden `LockAsync` skapar ett kritiskt avsnitt i en orkestrering. Dessa kritiska avsnitt hindrar andra orkestreringar från att göra överlappande ändringar i en angiven uppsättning entiteter. Internt skickar `LockAsync` API:et "lås"-åtgärder till entiteterna och returnerar när det tar emot ett "låsupparvuten" svarsmeddelande från var och en av dessa samma entiteter. Både lås och upplåsning är inbyggda åtgärder som stöds av alla entiteter.

Inga åtgärder från andra klienter tillåts på en entitet medan den är i låst tillstånd. Detta säkerställer att endast en orchestration-instans kan låsa en entitet åt gången. Om en anropare försöker anropa en åtgärd på en entitet medan den är låst av en orkestrering placeras den åtgärden i en väntande åtgärdskön. Inga väntande åtgärder bearbetas förrän anläggningen orkestrering släpper sitt lås.

> [!NOTE] 
> Detta skiljer sig något från synkroniseringsföre primitiver som `lock` används i de flesta programmeringsspråk, till exempel uttrycket i C#. I C#måste `lock` satsen till exempel användas av alla trådar för att säkerställa korrekt synkronisering över flera trådar. Entiteter kräver dock inte att alla anropare uttryckligen låser en entitet. Om någon anropare låser en entitet blockeras alla andra åtgärder på den entiteten och köas bakom låset.

Lås på entiteter är hållbara, så de kvarstår även om körningsprocessen återvinns. Lås sparas internt som en del av en entitets varaktiga tillstånd.

Till skillnad från transaktioner återställs inte ändringar i händelse av fel automatiskt. I stället måste alla felhantering, till exempel återställning eller återförsök, uttryckligen kodas, till exempel genom att fånga fel eller undantag. Detta designval är avsiktligt. Det är svårt eller omöjligt att automatiskt återställa alla effekter av en orkestrering i allmänhet, eftersom orkestreringar kan köra aktiviteter och ringa till externa tjänster som inte kan återställas. Försök att återställa kan också själva misslyckas och kräver ytterligare felhantering.

### <a name="critical-section-rules"></a>Regler för kritiska avsnitt

Till skillnad från lågnivålåsning primitiver i de flesta programmeringsspråk, kritiska avsnitt är *garanterat inte dödläge*. För att förhindra dödlägen tillämpar vi följande begränsningar: 

* Kritiska avsnitt kan inte kapslas.
* Kritiska avsnitt kan inte skapa underorchestrations.
* Kritiska avsnitt kan anropa endast entiteter som de har låst.
* Kritiska avsnitt kan inte anropa samma entitet med flera parallella anrop.
* Kritiska avsnitt kan signalera endast entiteter som de inte har låst.

Alla överträdelser av dessa regler orsakar ett `LockingRulesViolationException` körningsfel, till exempel i .NET, som innehåller ett meddelande som förklarar vilken regel som bröts.

## <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Många av de hållbara entiteter funktioner är inspirerade av [skådespelaren modellen](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med skådespelare kanske du känner igen många av de begrepp som beskrivs i den här artikeln. Varaktiga enheter är särskilt lika [virtuella aktörer](https://research.microsoft.com/projects/orleans/), eller korn, som populariserades av [Orleans projektet](http://dotnet.github.io/orleans/). Ett exempel:

* Varaktiga entiteter kan adresseras via ett entitets-ID.
* Varaktiga entitetsåtgärder körs seriellt, en i taget, för att förhindra konkurrensförhållanden.
* Varaktiga entiteter skapas implicit när de anropas eller signaleras.
* När du inte kör åtgärder tas varaktiga entiteter tyst bort från minnet.

Det finns några viktiga skillnader som är värda att notera:

* Varaktiga entiteter prioriterar varaktighet över svarstid och kanske därför inte är lämpliga för program med strikta svarstidskrav.
* Varaktiga entiteter har inte inbyggda tidsutse för meddelanden. I Orleans, alla meddelanden time out efter en konfigurerbar tid. Standardvärdet är 30 sekunder.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i ordning. I Orleans stöds tillförlitlig eller beställd leverans för innehåll som skickas via strömmar, men är inte garanterat för alla meddelanden mellan korn.
* Mönster för begäran-svar i entiteter är begränsade till orkestreringar. Inifrån enheter är endast enkelriktad meddelandehantering (även känd som signalering) tillåten, som i den ursprungliga aktörsmodellen, och till skillnad från korn i Orleans. 
* Varaktiga enheter är inte låsta. I Orleans kan dödlägen uppstå och löser inte förrän meddelandena har time out.
* Varaktiga enheter kan användas tillsammans med hållbara orkestreringar och stödja distribuerade låsmekanismer. 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs utvecklarens guide till varaktiga enheter i .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Läs mer om aktivitetshubbar](durable-functions-task-hubs.md)
