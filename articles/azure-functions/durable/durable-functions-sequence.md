---
title: Funktions länkning i Durable Functions – Azure
description: Lär dig hur du kör ett Durable Functions-exempel som kör en sekvens med funktioner.
author: cgillum
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: de2fd1a46d931c5d1b625094940a981509bf1488
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769565"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktions länkning i Durable Functions-Hello Sequence-exempel

Funktions länkning syftar på mönstret för att köra en sekvens med funktioner i en viss ordning. Ofta måste utdata från en funktion tillämpas på indata för en annan funktion. I den här artikeln beskrivs den länknings ordning som du skapar när du slutför Durable Functions snabb[C#](durable-functions-create-first-csharp.md) start (eller [Java Script](quickstart-js-vscode.md)). Mer information om Durable Functions finns i [Durable Functions översikt](durable-functions-overview.md).

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E1_HelloSequence`: en Orchestrator-funktion som anropar `E1_SayHello` flera gånger i en sekvens. Den lagrar utdata från `E1_SayHello` anrop och registrerar resultatet.
* `E1_SayHello`: en aktivitets funktion som prepends en sträng med "Hello".

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript och Java Script. Koden för Visual Studio-utveckling visas i slutet av artikeln.

> [!NOTE]
> JavaScript-Durable Functions är endast tillgängliga för funktionerna 2,0-körning.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function. JSON-fil

Om du använder Visual Studio Code eller Azure Portal för utveckling så är innehållet i *Function. JSON* -filen för Orchestrator-funktionen. De flesta Orchestrator- *funktionen. JSON* -filer ser nästan exakt likadana ut.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Det viktiga är den `orchestrationTrigger` bindnings typen. Alla Orchestrator-funktioner måste använda den här typen av utlösare.

> [!WARNING]
> Om du vill följa regeln "ingen I/O" för Orchestrator-funktioner ska du inte använda indata eller utgående bindningar när du använder `orchestrationTrigger` trigger-bindning.  Om andra indata-eller utdata-bindningar behövs bör de i stället användas i samband med `activityTrigger` funktioner som anropas av Orchestrator. Mer information finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#skript (Visual Studio Code och Azure Portal exempel kod)

Här är käll koden:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alla C# Orchestration-funktioner måste ha en parameter av typen `DurableOrchestrationContext`, som finns i `Microsoft.Azure.WebJobs.Extensions.DurableTask` sammansättning. Om du använder C# ett skript kan du referera till sammansättningen med hjälp av `#r` notation. Med det här kontext objektet kan du anropa andra *aktivitets* funktioner och skicka indataparametrar med hjälp av dess `CallActivityAsync`-metod.

Kod anrop `E1_SayHello` tre gånger i följd med olika parameter värden. Returvärdet för varje anrop läggs till i `outputs`s listan, som returneras i slutet av funktionen.

### <a name="javascript"></a>JavaScript

Här är käll koden:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla JavaScript-Orchestration-funktioner måste innehålla [`durable-functions`-modulen](https://www.npmjs.com/package/durable-functions). Det är ett bibliotek som du kan använda för att skriva Durable Functions i Java Script. Det finns tre viktiga skillnader mellan en Orchestration-funktion och andra JavaScript-funktioner:

1. Funktionen är en [Generator-funktion.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funktionen är omsluten i ett anrop till `durable-functions` modulens `orchestrator` metod (här `df`).
3. Funktionen måste vara synkron. Eftersom Orchestrator-metoden hanterar anrop av Context. klar, ska funktionen bara returnera.

`context`-objektet innehåller ett `df`-objekt som låter dig anropa andra *aktivitets* funktioner och skicka indataparametrar med hjälp av dess `callActivity`-metod. Kod anropen `E1_SayHello` tre gånger i följd med olika parameter värden, med `yield` för att ange att körningen ska vänta på att de asynkrona aktivitets funktions anropen ska returneras. Returvärdet för varje anrop läggs till i `outputs`s listan, som returneras i slutet av funktionen.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function. JSON-fil

*Function. JSON* -filen för aktivitets funktionen `E1_SayHello` liknar den `E1_HelloSequence`, förutom att den använder en `activityTrigger` bindnings typ i stället för en `orchestrationTrigger` bindnings typ.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> En funktion som anropas av en Orchestration-funktion måste använda `activityTrigger` bindningen.

Implementeringen av `E1_SayHello` är en relativt trivial sträng formatering.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Den här funktionen har en parameter av typen `DurableActivityContext`som används för att hämta de inloggade inskickade objektet av Orchestrator-funktionens anrop till `CallActivityAsync<T>`.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från en JavaScript-Orchestration-funktion behöver en aktivitets funktion inga särskilda inställningar. Indatamängden som skickas till den av Orchestrator-funktionen finns på `context.bindings`-objektet under namnet på `activityTrigger` bindningen – i det här fallet `context.bindings.name`. Bindnings namnet kan anges som en parameter för den exporterade funktionen och nås direkt, vilket är vad exempel koden gör.

## <a name="run-the-sample"></a>Kör exemplet

Om du vill köra `E1_HelloSequence` Orchestration skickar du följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Föregående HTTP-kodfragment förutsätter att det finns en post i `host.json`-filen som tar bort standard `api/`-prefixet från alla URL: er för HTTP-utlösare. Du hittar koden för den här konfigurationen i `host.json`-filen i exemplen.

Om du till exempel kör exemplet i en Function-app med namnet "myfunctionapp" ersätter du {Host} med "myfunctionapp.azurewebsites.net".

Resultatet är ett HTTP 202-svar, som detta (trimmat för det kortfattat):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

I det här läget köas dirigeringen och börjar köras omedelbart. URL: en i `Location`s rubriken kan användas för att kontrol lera status för körningen.

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

Som du kan se är `runtimeStatus` av instansen *slutförd* och `output` innehåller JSON-serialiserat resultat från Orchestrator Function-körningen.

> [!NOTE]
> Den HTTP POST-slutpunkt som startade Orchestrator-funktionen implementeras i exempel programmet som en HTTP-utlösare med namnet "HttpStart". Du kan implementera liknande start logik för andra typer av utlösare, t. ex. `queueTrigger`, `eventHubTrigger`eller `timerTrigger`.

Titta på funktions körnings loggarna. Funktionen `E1_HelloSequence` startas och slutförs flera gånger på grund av uppspelnings beteendet som beskrivs i avsnittet om [Dirigerings tillförlitlighet](durable-functions-orchestrations.md#reliability) . Å andra sidan fanns det bara tre körningar av `E1_SayHello` eftersom dessa funktions körningar inte spelas upp.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat sig vara en enkel genom koppling av funktions länkning. I nästa exempel visas hur du implementerar mönstret för fläkt-out/fläktning.

> [!div class="nextstepaction"]
> [Köra fläkt-out/fläkt-in-exemplet](durable-functions-cloud-backup.md)
