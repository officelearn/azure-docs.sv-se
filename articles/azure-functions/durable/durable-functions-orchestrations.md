---
title: Varaktiga orkestreringar – Azure-funktioner
description: Introduktion till orchestration-funktionen för Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241362"
---
# <a name="durable-orchestrations"></a>Hållbara orkestreringar

Varaktiga funktioner är ett tillägg av [Azure Functions](../functions-overview.md). Du kan använda en *orchestrator-funktion* för att dirigera körningen av andra varaktiga funktioner i en funktionsapp. Orchestrator funktioner har följande egenskaper:

* Orchestrator-funktioner definierar funktionsarbetsflöden med hjälp av procedurkod. Inga deklarativa scheman eller designers behövs.
* Orchestrator-funktioner kan anropa andra varaktiga funktioner synkront och asynkront. Utdata från anropade funktioner kan sparas på ett tillförlitligt sätt till lokala variabler.
* Orchestrator funktioner är hållbara och tillförlitliga. Körningsstatus styrs automatiskt när funktionen "väntar" eller "ger". Det lokala tillståndet förloras aldrig när processen återanvänds eller den virtuella datorn startas om.
* Orchestrator-funktioner kan vara långvariga. Den totala livslängden för en *orkestreringsinstans* kan vara sekunder, dagar, månader eller aldrig sinande.

Den här artikeln ger dig en översikt över orchestrator funktioner och hur de kan hjälpa dig att lösa olika app utveckling utmaningar. Om du inte redan är bekant med de typer av funktioner som är tillgängliga i en varaktig funktionsapp läser du först artikeln [För varaktig funktion.](durable-functions-types-features-overview.md)

## <a name="orchestration-identity"></a>Orkestrering identitet

Varje *instans* av en orkestrering har en instansidentifierare (kallas även *ett instans-ID*). Som standard är varje instans-ID ett automatiskt tvinnat GUID. Instans-ID:er kan dock också vara alla användargenererade strängvärde. Varje orkestreringsinstans-ID måste vara unikt i en [aktivitetsnav](durable-functions-task-hubs.md).

Följande är några regler om instans-ID:

* Instans-ID:er måste vara mellan 1 och 256 tecken.
* Instans-ID:er `@`får inte börja med .
* Instans-ID:er `/` `\`får `#`inte `?` innehålla , , eller tecken.
* Instans-ID får inte innehålla kontrolltecken.

> [!NOTE]
> Det rekommenderas i allmänhet att använda autogenererade instans-ID:er när det är möjligt. Användargenererade instans-ID:er är avsedda för scenarier där det finns en 1:1-mappning mellan en orkestreringsinstans och en viss extern programspecifik entitet, till exempel en inköpsorder eller ett dokument.

Instans-ID:t i en [instanshanteringsoperationer](durable-functions-instance-management.md)är en obligatorisk parameter. De är också viktiga för diagnostik, till exempel [genom orchestration tracking data](durable-functions-diagnostics.md#application-insights) i Application Insights för felsökning eller analys. Därför rekommenderas att du sparar genererade instans-ID:er på någon extern plats (till exempel en databas eller i programloggar) där de enkelt kan refereras senare.

## <a name="reliability"></a>Tillförlitlighet

Orchestrator-funktioner underhåller tillförlitligt sitt körningstillstånd med hjälp av designmönstret [för händelsekällor.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) I stället för att direkt lagra det aktuella tillståndet för en orkestrering använder ramverket för varaktiga uppgifter ett tilläggslager för att registrera hela serien av åtgärder som funktionsorkestreringen vidtar. En tilläggsbutik har många fördelar jämfört med att "dumpa" hela körningstillståndet. Fördelarna inkluderar ökad prestanda, skalbarhet och svarstider. Du får också slutlig konsekvens för transaktionsdata och fullständiga granskningshistorik och historik. Granskningsspåren stöder tillförlitliga kompensationsåtgärder.

Varaktiga funktioner använder händelseinköp transparent. Bakom kulisserna ger `await` operatorn (C#) eller `yield` (JavaScript) i en orchestrator-funktion kontroll över orchestrator-tråden tillbaka till dispatcher för hållbar aktivitetsram. Avsändaren genomför sedan alla nya åtgärder som orchestrator-funktionen schemalagt (till exempel anropa en eller flera underordnade funktioner eller schemalägga en varaktig timer) till lagring. Den transparenta commit-åtgärden lägger till körningshistoriken för orchestration-instansen. Historiken lagras i en lagringstabell. Commit-åtgärden lägger sedan till meddelanden i en kö för att schemalägga det verkliga arbetet. Nu kan orchestrator-funktionen tas bort från minnet.

När en orkestreringsfunktion får mer arbete att utföra (till exempel tas ett svarsmeddelande emot eller en varaktig timer upphör att gälla) aktiveras och kör orchestrator hela funktionen från början för att återskapa det lokala tillståndet. Under reprisen, om koden försöker anropa en funktion (eller göra något annat async-arbete), konsulterar durable task framework körningshistoriken för den aktuella orkestreringen. Om den upptäcker att [aktivitetsfunktionen](durable-functions-types-features-overview.md#activity-functions) redan har körts och gett ett resultat, spelas den upp funktionens resultat och orchestrator-koden fortsätter att köras. Uppspelningen fortsätter tills funktionskoden är klar eller tills den har schemalagt nytt asynkronarbete.

> [!NOTE]
> För att reprismönstret ska fungera korrekt och tillförlitligt måste orchestrator-funktionskoden vara *deterministisk*. Mer information om kodbegränsningar för orchestrator-funktioner finns i avsnittet [orchestrator-funktionskodbegränsningar.](durable-functions-code-constraints.md)

> [!NOTE]
> Om en orchestrator-funktion avger loggmeddelanden kan uppspelningsbeteendet orsaka att dubblettloggmeddelanden avges. Mer information om varför det här beteendet uppstår och hur du kan kringgå det finns i avsnittet [Loggning.](durable-functions-diagnostics.md#logging)

## <a name="orchestration-history"></a>Orkestrering historia

Händelseinköpsbeteendet för durable task framework är nära kopplat till orchestrator-funktionskoden du skriver. Anta att du har en aktivitetskedjad orchestrator-funktion, till exempel följande orchestrator-funktion:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

Vid `await` varje (C#) eller `yield` (JavaScript) -sats vägspärrar körningsverket körningstillståndet för funktionen i någon varaktig lagringsserverdel (vanligtvis Azure Table storage). Detta tillstånd är vad som kallas *orkestrering historia*.

### <a name="history-table"></a>Tabell över historik

Generellt sett gör ramverket för varaktiga uppgifter följande vid varje kontrollpunkt:

1. Sparar körningshistorik i Azure Storage-tabeller.
2. Enqueues meddelanden för funktioner som orchestrator vill anropa.
3. Enqueues meddelanden för orchestrator &mdash; själv till exempel varaktiga timermeddelanden.

När kontrollpunkten är klar är orchestrator-funktionen fri att tas bort från minnet tills det finns mer arbete för den att göra.

> [!NOTE]
> Azure Storage ger inga transaktionsgarantier mellan att spara data i tabelllagring och köer. För att hantera fel använder lagringsprovidern för varaktiga funktioner *eventuella konsekvensmönster.* Dessa mönster säkerställer att inga data går förlorade om det finns en krasch eller förlust av anslutning i mitten av en kontrollpunkt.

När funktionen har slutförts ser den tidigare historiken ut ungefär så här i Azure Table Storage (förkortad för illustration):

| PartitionKey (instanceId)                     | Eventtype             | Tidsstämpel               | Indata | Namn             | Resultat                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b (eaee885b) | KörningStartad      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b (eaee885b) | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | Aktivitetsschema         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b (eaee885b) | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | AktivitetKomtent         | 2017-05-05T18:45:34.201Z |       |                  | ""Hej Tokyo!""                                        |                     |
| eaee885b (eaee885b) | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | Aktivitetsschema         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b (eaee885b) | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | AktivitetKomtent         | 2017-05-05T18:45:34.763Z |       |                  | ""Hej Seattle!""                                      |                     |
| eaee885b (eaee885b) | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | Aktivitetsschema         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b (eaee885b) | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | AktivitetKomtent         | 2017-05-05T18:45:34.919Z |       |                  | ""Hej London!""                                       |                     |
| eaee885b (eaee885b) | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b (eaee885b) | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | """Hej Tokyo!"",""Hej Seattle!"",""Hej London!""]" | Slutfört           |

Några anteckningar om kolumnvärdena:

* **PartitionKey**: Innehåller instans-ID för orkestrering.
* **EventType**: Representerar typen av händelse. Kan vara någon av följande typer:
  * **OrchestrationStarted**: Orchestrator-funktionen återupptas från en väntar eller körs för första gången. Kolumnen `Timestamp` används för att fylla i det `CurrentUtcDateTime` deterministiska värdet `currentUtcDateTime` för API:erna (.NET) och (JavaScript).
  * **ExecutionStarted**: Orchestrator-funktionen började köras för första gången. Den här händelsen innehåller även `Input` funktionsinmatningen i kolumnen.
  * **AktivitetPlanerad**: En aktivitetsfunktion har schemalagts. Namnet på aktivitetsfunktionen fångas `Name` in i kolumnen.
  * **Aktivitetskomt:** En aktivitetsfunktion har slutförts. Resultatet av funktionen finns `Result` i kolumnen.
  * **TimerCreated**: En hållbar timer skapades. Kolumnen `FireAt` innehåller den schemalagda UTC-tiden då timern upphör att gälla.
  * **TimerFired**: En hållbar timer avfyrad.
  * **EventRaised**: En extern händelse skickades till orchestration-instansen. Kolumnen `Name` fångar namnet på händelsen och `Input` kolumnen fångar nyttolasten för händelsen.
  * **OrchestratorCompleted**: Orchestratoren fungerar väntat.
  * **ContinueAsNew**: Orchestrator-funktionen slutfördes och startade om sig själv med nytt tillstånd. Kolumnen `Result` innehåller värdet, som används som indata i den omstartade instansen.
  * **ExecutionCompleted**: Orchestrator-funktionen gick till slutförande (eller misslyckades). Utdata för funktionen eller feldetaljerna `Result` lagras i kolumnen.
* **Tidsstämpel**: UTC-tidsstämpeln för historikhändelsen.
* **Namn**: Namnet på den funktion som anropades.
* **Ingång**: Den JSON-formaterade ingången till funktionen.
* **Resultat**: Funktionens utgång. det vill än dess avkastningsvärde.

> [!WARNING]
> Även om det är användbart som ett felsökningsverktyg ska du inte vara beroende av den här tabellen. Det kan ändras i takt med att tillägget Varaktiga funktioner utvecklas.

Varje gång funktionen återupptas `await` från en `yield` (C#) eller (JavaScript) körs orchestrator-funktionen från grunden igen. Vid varje körning konsulterar den körningshistoriken för att avgöra om den aktuella async-åtgärden har ägt rum.  Om åtgärden genomfördes spelas ramverket upp utskriften av åtgärden omedelbart `await` och går `yield` vidare till nästa (C#) eller (JavaScript). Denna process fortsätter tills hela historien har spelats upp igen. När den aktuella historiken har spelats upp igen har de lokala variablerna återställts till sina tidigare värden.

## <a name="features-and-patterns"></a>Funktioner och mönster

I nästa avsnitt beskrivs funktionernas funktioners funktioners funktioners funktioner.

### <a name="sub-orchestrations"></a>Sub-orkestreringar

Orchestrator-funktioner kan anropa aktivitetsfunktioner, men även andra orchestratorfunktioner. Du kan till exempel skapa en större orkestrering av ett bibliotek med orchestrator-funktioner. Du kan också köra flera instanser av en orchestrator-funktion parallellt.

Mer information och exempel finns i artikeln [Underorkestreringar.](durable-functions-sub-orchestrations.md)

### <a name="durable-timers"></a>Hållbara timers

Orkestreringar kan schemalägga *varaktiga timers* för att implementera fördröjningar eller för att ställa in timeout-hantering på asynkronåtgärder. Använd varaktiga timers i `Thread.Sleep` orchestrator-funktioner i stället för och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (JavaScript).

Mer information och exempel finns i artikeln [Varaktiga timers.](durable-functions-timers.md)

### <a name="external-events"></a>Externa händelser

Orchestrator-funktioner kan vänta på att externa händelser ska uppdatera en orchestration-instans. Den här funktionen varaktiga funktioner är ofta användbar för hantering av en mänsklig interaktion eller andra externa motringningar.

Mer information och exempel finns i artikeln [Externa händelser.](durable-functions-external-events.md)

### <a name="error-handling"></a>Felhantering

Orchestrator-funktioner kan använda programmeringsspråkets felhanteringsfunktioner. Befintliga mönster `try` / `catch` som stöds i orchestration kod.

Orchestrator-funktioner kan också lägga till återförsöksprinciper till de aktivitets- eller underorkestrarfunktioner som de anropar. Om en aktivitets- eller underorkestrarfunktion misslyckas med ett undantag kan den angivna återförsöksprincipen automatiskt fördröja och försöka utföra körningen upp till ett angivet antal gånger.

> [!NOTE]
> Om det finns ett ohanterat undantag i en orchestrator-funktion `Failed` slutförs orchestration-instansen i ett tillstånd. Det går inte att försöka med en orkestreringsinstans igen när den har misslyckats.

Mer information och exempel finns i artikeln [Felhantering.](durable-functions-error-handling.md)

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritiska avsnitt (Varaktiga funktioner 2.x, för närvarande endast .NET)

Orkestreringsinstanser är enkeltrådade så det är inte nödvändigt att oroa sig för rasförhållanden *inom* en orkestrering. Tävlingsförhållanden är dock möjliga när orkestreringar interagerar med externa system. Om du vill minska konkurrensförhållandena när du interagerar med externa `LockAsync` system kan orchestrator-funktioner definiera kritiska *avsnitt* med en metod i .NET.

Följande exempelkod visar en orchestrator-funktion som definierar ett kritiskt avsnitt. Det går in i `LockAsync` det kritiska avsnittet med hjälp av metoden. Den här metoden kräver att en eller flera referenser vidarebefordras till en [varaktig entitet](durable-functions-entities.md), som varaktigt hanterar låstillståndet. Endast en enda instans av den här orkestreringen kan köra koden i det kritiska avsnittet åt gången.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Den `LockAsync` förvärvar de varaktiga låsen `IDisposable` och returnerar en som avslutar det kritiska avsnittet när det avyttras. Detta `IDisposable` resultat kan användas `using` tillsammans med ett block för att få en syntaktisk representation av det kritiska avsnittet. När en orchestrator-funktion anger ett kritiskt avsnitt kan bara en instans köra kodblocket. Alla andra instanser som försöker ange det kritiska avsnittet blockeras tills den föregående instansen avslutar det kritiska avsnittet.

Den kritiska sektionsfunktionen är också användbar för att samordna ändringar till varaktiga entiteter. Mer information om kritiska avsnitt finns i avsnittet [Varaktiga entiteter "Entitetssamordning".](durable-functions-entities.md#entity-coordination)

> [!NOTE]
> Kritiska avsnitt finns i Varaktiga funktioner 2.0 och högre. För närvarande implementerar endast .NET-orkestreringar den här funktionen.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Anropa HTTP-slutpunkter (varaktiga funktioner 2.x)

Orchestrator-funktioner är inte tillåtna att göra I/O, enligt beskrivningen i [orchestrator-funktionskodsbegränsningar](durable-functions-code-constraints.md). Den typiska lösningen för den här begränsningen är att radbryta alla koder som behöver göra I/O i en aktivitetsfunktion. Orkestreringar som interagerar med externa system använder ofta aktivitetsfunktioner för att ringa HTTP-anrop och returnera resultatet till orkestreringen.

# <a name="c"></a>[C#](#tab/csharp)

För att förenkla det här vanliga `CallHttpAsync` mönstret kan orchestrator-funktioner använda metoden för att anropa HTTP-API:er direkt.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Förutom att stödja grundläggande mönster för begäran/svar stöder metoden automatisk hantering av vanliga async HTTP 202-avsökningsmönster och stöder även autentisering med externa tjänster med hjälp av [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

Mer information och detaljerade exempel finns i [http-funktioners](durable-functions-http-features.md) artikel.

> [!NOTE]
> Anropa HTTP-slutpunkter direkt från orchestrator-funktioner är tillgängligt i Varaktiga funktioner 2.0 och högre.

### <a name="passing-multiple-parameters"></a>Skickar flera parametrar

Det går inte att skicka flera parametrar till en aktivitetsfunktion direkt. Rekommendationen är att skicka i en matris med objekt eller sammansatta objekt.

# <a name="c"></a>[C#](#tab/csharp)

I .NET kan du också använda [ValueTuples-objekt.](https://docs.microsoft.com/dotnet/csharp/tuples) Följande exempel använder nya funktioner [i ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) som lagts till med [C# 7:](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples)

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>Aktivitet

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Begränsningar för orkestrerarkod](durable-functions-code-constraints.md)
