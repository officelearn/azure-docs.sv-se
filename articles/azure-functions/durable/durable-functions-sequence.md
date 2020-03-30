---
title: Funktionskedja i varaktiga funktioner - Azure
description: Lär dig hur du kör ett exempel på varaktiga funktioner som kör en sekvens av funktioner.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562077"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktionskedja i varaktiga funktioner - Hello-sekvensprov

Funktionskedja avser mönstret för att utföra en sekvens av funktioner i en viss ordning. Ofta måste utdata från en funktion tillämpas på inmatningen av en annan funktion. I den här artikeln beskrivs den kedjesekvens som du skapar när du slutför snabbstarten Varaktiga funktioner ([C#](durable-functions-create-first-csharp.md) eller [JavaScript](quickstart-js-vscode.md)). Mer information om varaktiga funktioner finns i [Översikt över varaktiga funktioner](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempelappen:

* `E1_HelloSequence`: En [orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E1_SayHello` flera gånger i en sekvens. Den lagrar utdata `E1_SayHello` från anrop och registrerar resultaten.
* `E1_SayHello`: En [aktivitetsfunktion](durable-functions-bindings.md#activity-trigger) som förbereder en sträng med "Hello".
* `HttpStart`: En HTTP-utlöst funktion som startar en förekomst av orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence orchestrator-funktionen

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Alla C#-orkestreringsfunktioner `DurableOrchestrationContext`måste ha en `Microsoft.Azure.WebJobs.Extensions.DurableTask` parameter av typen , som finns i sammansättningen. Med det här kontextobjektet kan du `CallActivityAsync` anropa andra *aktivitetsfunktioner* och skicka indataparametrar med hjälp av dess metod.

Koden anropar `E1_SayHello` tre gånger i följd med olika parametervärden. Returvärdet för varje samtal läggs `outputs` till i listan, som returneras i slutet av funktionen.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> JavaScript varaktiga funktioner är endast tillgängliga för 2.0-körningen för funktioner.

#### <a name="functionjson"></a>function.json

Om du använder Visual Studio Code eller Azure-portalen för utveckling, här är innehållet i *function.json-filen* för orchestrator-funktionen. De flesta orchestrator *function.json* filer ser nästan exakt ut så här.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Det viktiga är `orchestrationTrigger` bindningstypen. Alla orchestrator-funktioner måste använda den här utlösartypen.

> [!WARNING]
> Om du vill följa regeln "ingen I/O" för orchestrator-funktioner ska du `orchestrationTrigger` inte använda några indata- eller utdatabindningar när du använder utlösarbindningen.  Om andra indata- eller utdatabindningar behövs bör `activityTrigger` de i stället användas i samband med funktioner som anropas av orchestrator. Mer information finns i artikeln [orchestrator funktionskodbegränsningar.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Här är funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla JavaScript-orkestreringsfunktioner måste innehålla [ `durable-functions` modulen](https://www.npmjs.com/package/durable-functions). Det är ett bibliotek som gör att du kan skriva varaktiga funktioner i JavaScript. Det finns tre signifikanta skillnader mellan en orkestreringsfunktion och andra JavaScript-funktioner:

1. Funktionen är en [generatorfunktion.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript). .
2. Funktionen är insvept i ett `durable-functions` anrop `orchestrator` till modulens metod (här). `df`
3. Funktionen måste vara synkron. Eftersom metoden "orchestrator" hanterar anropet "context.done" ska funktionen helt enkelt "returnera".

Objektet `context` innehåller ett `df` varaktigt orkestreringskontextobjekt som gör att `callActivity` du kan anropa andra *aktivitetsfunktioner* och skicka indataparametrar med hjälp av metoden. Koden anropar `E1_SayHello` tre gånger i följd `yield` med olika parametervärden, med hjälp av för att ange att körningen ska vänta på asynkrona aktivitetsfunktionsanrop som ska returneras. Returvärdet för varje anrop `outputs` läggs till i matrisen, som returneras i slutet av funktionen.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello aktivitetsfunktion

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Aktiviteter använder `ActivityTrigger` attributet. Använd den `IDurableActivityContext` medföljande för att utföra aktivitetsrelaterade `GetInput<T>`åtgärder, till exempel åtkomst till indatavärdet med .

Genomförandet av `E1_SayHello` är en relativt trivial sträng formatering operation.

I stället för `IDurableActivityContext`bindning till en kan du binda direkt till den typ som skickas till aktivitetsfunktionen. Ett exempel:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

*Function.json-filen* för `E1_SayHello` aktivitetsfunktionen liknar `E1_HelloSequence` den för förutom `activityTrigger` att den `orchestrationTrigger` använder en bindningstyp i stället för en bindningstyp.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Alla funktioner som anropas av `activityTrigger` en orkestreringsfunktion måste använda bindningen.

Genomförandet av `E1_SayHello` är en relativt trivial sträng formatering operation.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från en JavaScript-orkestreringsfunktion behöver en aktivitetsfunktion ingen särskild inställning. Indata som skickas till den av orchestrator-funktionen finns på `context.bindings` objektet under namnet på bindningen `activityTrigger` - i det här fallet `context.bindings.name`. Bindningsnamnet kan anges som en parameter för den exporterade funktionen och nås direkt, vilket är vad exempelkoden gör.

---

### <a name="httpstart-client-function"></a>Funktionen HttpStart-klient

Du kan starta en förekomst av orchestrator-funktion med hjälp av en klientfunktion. Du kommer `HttpStart` att använda funktionen HTTP-utlöst `E1_HelloSequence`för att starta instanser av .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Om du vill interagera med orchestrators måste funktionen innehålla en `DurableClient` indatabindning. Du använder klienten för att starta en orkestrering. Det kan också hjälpa dig att returnera ett HTTP-svar som innehåller webbadresser för att kontrollera status för den nya orkestreringen.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Om du vill interagera med orchestrators måste funktionen innehålla en `durableClient` indatabindning.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Används `df.getClient` för `DurableOrchestrationClient` att hämta ett objekt. Du använder klienten för att starta en orkestrering. Det kan också hjälpa dig att returnera ett HTTP-svar som innehåller webbadresser för att kontrollera status för den nya orkestreringen.

---

## <a name="run-the-sample"></a>Kör exemplet

Om du `E1_HelloSequence` vill köra orkestrering skickar `HttpStart` du följande HTTP POST-begäran till funktionen.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Det tidigare HTTP-kodavsnittet förutsätter att `host.json` det finns en `api/` post i filen som tar bort standardprefixet från alla HTTP-utlösarfunktioners URL:er. Du hittar markeringen för den `host.json` här konfigurationen i filen i exemplen.

Om du till exempel kör exemplet i en funktionsapp med namnet "minfunktionsapp" ersätter du {host}" med "myfunctionapp.azurewebsites.net".

Resultatet är ett HTTP 202-svar, så här (trimmat för korthet):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Nu står orkestreringen i kö och börjar köras omedelbart. URL:en `Location` i sidhuvudet kan användas för att kontrollera körningens status.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Resultatet är status för orkestreringen. Den körs och slutförs snabbt, så du ser den i *slutfört* tillstånd med ett svar som ser ut så här (trimmad för korthet):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Som du kan `runtimeStatus` se är förekomsten *slutförd* och `output` innehåller JSON-serialiserade resultatet av orkesterfunktionens körning.

> [!NOTE]
> Du kan implementera liknande startlogik `queueTrigger`för `eventHubTrigger`andra `timerTrigger`utlösartyper, till exempel , eller .

Titta på funktionskörningsloggarna. Funktionen `E1_HelloSequence` startades och slutfördes flera gånger på grund av reprisbeteendet som beskrivs i [tillförlitlighetsämnet orkestrering.](durable-functions-orchestrations.md#reliability) Å ena sidan fanns det endast `E1_SayHello` tre utföranden av, sedan de fungerar utföranden inte får replayed.

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat en enkel funktionskedjad orkestrering. Nästa prov visar hur du implementerar fan-out/fan-in-mönstret.

> [!div class="nextstepaction"]
> [Kör fan-out/fan-in-provet](durable-functions-cloud-backup.md)
