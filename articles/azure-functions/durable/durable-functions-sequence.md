---
title: Funktions länkning i Durable Functions – Azure
description: Lär dig hur du kör ett Durable Functions-exempel som kör en sekvens med funktioner.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: b117fca23b26919f3c404dd32ba64c0c89d66ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033572"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktions länkning i Durable Functions-Hello Sequence-exempel

Funktions länkning syftar på mönstret för att köra en sekvens med funktioner i en viss ordning. Ofta måste utdata från en funktion tillämpas på indata för en annan funktion. I den här artikeln beskrivs den länknings ordning som du skapar när du slutför Durable Functions snabb start ([C#](durable-functions-create-first-csharp.md) eller [Java Script](quickstart-js-vscode.md)). Mer information om Durable Functions finns i [Durable Functions översikt](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E1_HelloSequence`: En [Orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E1_SayHello` flera gånger i följd. Den lagrar utdata från `E1_SayHello` anropen och registrerar resultatet.
* `E1_SayHello`: En [aktivitets funktion](durable-functions-bindings.md#activity-trigger) som prepends en sträng med "Hello".
* `HttpStart`: En HTTP-utlöst funktion som startar en instans av Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator-funktion

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Alla C#-Orchestration-funktioner måste ha en parameter av typen `DurableOrchestrationContext` , som finns i `Microsoft.Azure.WebJobs.Extensions.DurableTask` sammansättningen. Med det här kontext objektet kan du anropa andra *aktivitets* funktioner och skicka indataparametrar med `CallActivityAsync` metoden.

Koden anropar `E1_SayHello` tre gånger i följd med olika parameter värden. Returvärdet för varje anrop läggs till i `outputs` listan, som returneras i slutet av funktionen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript-Durable Functions är endast tillgängliga för funktionerna 2,0-körning.

#### <a name="functionjson"></a>function.json

Om du använder Visual Studio Code eller Azure Portal för utveckling, är det här innehållet i *function.js* filen för Orchestrator-funktionen. De flesta Orchestrator- *function.jspå* filer ser ut ungefär så här.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Det viktiga är `orchestrationTrigger` bindnings typen. Alla Orchestrator-funktioner måste använda den här typen av utlösare.

> [!WARNING]
> Om du vill följa regeln "ingen I/O" för Orchestrator-funktioner ska du inte använda några indata-eller utdata-bindningar när du använder `orchestrationTrigger` trigger-bindningen.  Om andra indata-eller utdata-bindningar behövs bör de i stället användas i kontexten för `activityTrigger` Functions som anropas av Orchestrator. Mer information finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js

Här är funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla JavaScript-Orchestration-funktioner måste innehålla [ `durable-functions` modulen](https://www.npmjs.com/package/durable-functions). Det är ett bibliotek som du kan använda för att skriva Durable Functions i Java Script. Det finns tre viktiga skillnader mellan en Orchestration-funktion och andra JavaScript-funktioner:

1. Funktionen är en [Generator-funktion.](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Funktionen omges av ett anrop till `durable-functions` modulens `orchestrator` metod (här `df` ).
3. Funktionen måste vara synkron. Eftersom Orchestrator-metoden hanterar anrop av Context. klar, ska funktionen bara returnera.

`context`Objektet innehåller ett beständigt `df` Orchestration-kontext objekt som gör att du kan anropa andra *aktivitets* funktioner och skicka indataparametrar med hjälp av `callActivity` metoden. Koden anropar `E1_SayHello` tre gånger i följd med olika parameter värden, med hjälp av `yield` för att indikera att körningen ska vänta på att de asynkrona aktivitets funktions anropen ska returneras. Returvärdet för varje anrop läggs till i `outputs` matrisen, som returneras i slutet av funktionen.

---

### <a name="e1_sayhello-activity-function"></a>Funktionen E1_SayHello aktivitet

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Aktiviteter använder `ActivityTrigger` attributet. Använd för `IDurableActivityContext` att utföra åtgärder som är relaterade till aktiviteter, till exempel åtkomst till indatavärdet med hjälp av `GetInput<T>` .

Implementeringen av `E1_SayHello` är en relativt trivial sträng formaterings åtgärd.

I stället för att binda till en `IDurableActivityContext` kan du binda direkt till den typ som skickas till funktionen aktivitet. Exempel:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jspå

*function.jspå* fil för funktionen aktivitet `E1_SayHello` liknar detta `E1_HelloSequence` , förutom att den använder en `activityTrigger` bindnings typ i stället för en `orchestrationTrigger` bindnings typ.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Alla funktioner som anropas av en Orchestration-funktion måste använda `activityTrigger` bindningen.

Implementeringen av `E1_SayHello` är en relativt trivial sträng formaterings åtgärd.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från en JavaScript-Orchestration-funktion behöver en aktivitets funktion inga särskilda inställningar. Indatamängden som skickas till den av Orchestrator-funktionen finns på `context.bindings` objektet under namnet på `activityTrigger` bindningen – i det här fallet `context.bindings.name` . Bindnings namnet kan anges som en parameter för den exporterade funktionen och nås direkt, vilket är vad exempel koden gör.

---

### <a name="httpstart-client-function"></a>HttpStart-klient funktion

Du kan starta en instans av Orchestrator-funktionen med hjälp av en klient funktion. Du kommer att använda `HttpStart` funktionen http-utlöst för att starta instanser av `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

För att interagera med Dirigerare måste funktionen innehålla en `DurableClient` Indatamask. Du kan använda klienten för att starta en dirigering. Det kan också hjälpa dig att returnera ett HTTP-svar som innehåller URL: er för att kontrol lera status för den nya dirigeringen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jspå

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

För att interagera med Dirigerare måste funktionen innehålla en `durableClient` Indatamask.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Används `df.getClient` för att hämta ett `DurableOrchestrationClient` objekt. Du kan använda klienten för att starta en dirigering. Det kan också hjälpa dig att returnera ett HTTP-svar som innehåller URL: er för att kontrol lera status för den nya dirigeringen.

---

## <a name="run-the-sample"></a>Kör exemplet

Om du vill köra `E1_HelloSequence` dirigeringen skickar du följande http post-begäran till `HttpStart` funktionen.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Det tidigare HTTP-kodfragmentet förutsätter att det finns en post i `host.json` filen som tar bort `api/` standardprefixet från alla URL: er för http-utlösare. Du hittar koden för den här konfigurationen i `host.json` filen i exemplen.

Om du till exempel kör exemplet i en Function-app med namnet "myfunctionapp" ersätter du {Host} med "myfunctionapp.azurewebsites.net".

Resultatet är ett HTTP 202-svar, som detta (trimmat för det kortfattat):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

I det här läget köas dirigeringen och börjar köras omedelbart. URL: en i `Location` rubriken kan användas för att kontrol lera status för körningen.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Resultatet är dirigeringens status. Den körs och slutförs snabbt, så du ser den i *slutfört* tillstånd med ett svar som ser ut så här (putsad för det kortfattat):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Som du kan se `runtimeStatus` *slutförs* instansen och `output` innehåller det JSON-serialiserade resultatet av Orchestrator Function-körningen.

> [!NOTE]
> Du kan implementera liknande start logik för andra utlösare, `queueTrigger` t `eventHubTrigger` . ex `timerTrigger` ., eller.

Titta på funktions körnings loggarna. `E1_HelloSequence`Funktionen startade och slutfördes flera gånger på grund av uppspelnings beteendet som beskrivs i avsnittet om [Dirigerings tillförlitlighet](durable-functions-orchestrations.md#reliability) . Å andra sidan fanns det bara tre körningar `E1_SayHello` eftersom dessa funktion körningar inte spelas upp.

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat sig vara en enkel genom koppling av funktions länkning. I nästa exempel visas hur du implementerar mönstret för fläkt-out/fläktning.

> [!div class="nextstepaction"]
> [Köra fläkt-out/fläkt-in-exemplet](durable-functions-cloud-backup.md)
