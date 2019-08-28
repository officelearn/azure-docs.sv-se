---
title: Funktions länkning i Durable Functions – Azure
description: Lär dig hur du kör ett Durable Functions-exempel som kör en sekvens med funktioner.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1168963c0698c6bdafe20babe2e5143585bf90a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087107"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktions länkning i Durable Functions-Hello Sequence-exempel

Funktions länkning syftar på mönstret för att köra en sekvens med funktioner i en viss ordning. Ofta måste utdata från en funktion tillämpas på indata för en annan funktion. I den här artikeln beskrivs den länknings ordning som du skapar när du slutför Durable Functions snabb[C#](durable-functions-create-first-csharp.md) start (eller [Java Script](quickstart-js-vscode.md)). Mer information om Durable Functions finns i [Durable Functions mönster och tekniska begrepp](durable-functions-concepts.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E1_HelloSequence`: En Orchestrator-funktion som `E1_SayHello` anropar flera gånger i en sekvens. Den lagrar utdata från `E1_SayHello` anropen och registrerar resultatet.
* `E1_SayHello`: En aktivitets funktion som prepends en sträng med "Hello".

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript och Java Script. Koden för Visual Studio-utveckling visas i slutet av artikeln.

> [!NOTE]
> JavaScript-Durable Functions är endast tillgängliga för funktionerna 2. x-körning.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function. JSON-fil

Om du använder Visual Studio Code eller Azure Portal för utveckling så är innehållet i *Function. JSON* -filen för Orchestrator-funktionen. De flesta Orchestrator- *funktionen. JSON* -filer ser nästan exakt likadana ut.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Det viktiga är `orchestrationTrigger` bindnings typen. Alla Orchestrator-funktioner måste använda den här typen av utlösare.

> [!WARNING]
> Om du vill följa regeln "ingen i/O" för Orchestrator-funktioner ska du inte använda några indata-eller utdata-bindningar när du `orchestrationTrigger` använder trigger-bindningen.  Om andra indata-eller utdata-bindningar behövs bör de i stället användas i kontexten `activityTrigger` för functions som anropas av Orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#skript (Visual Studio Code och Azure Portal exempel kod)

Här är käll koden:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alla C# Orchestration-funktioner måste ha en parameter av typen `DurableOrchestrationContext`, `Microsoft.Azure.WebJobs.Extensions.DurableTask` som finns i sammansättningen. Om du använder C# ett skript kan du referera till sammansättningen med hjälp `#r` av notationen. Med det här kontext objektet kan du anropa andra *aktivitets* funktioner och skicka indataparametrar med hjälp av dess [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) -metod.

Koden anropar `E1_SayHello` tre gånger i följd med olika parameter värden. Returvärdet för varje anrop läggs till `outputs` i listan, som returneras i slutet av funktionen.

### <a name="javascript"></a>Javascript

Här är käll koden:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla JavaScript-Orchestration-funktioner måste innehålla [ `durable-functions` modulen](https://www.npmjs.com/package/durable-functions). Det här är ett bibliotek som du kan använda för att skriva Durable Functions i Java Script. Det finns tre viktiga skillnader mellan en Orchestration-funktion och andra JavaScript-funktioner:

1. Funktionen är en [Generator-funktion.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funktionen omges av ett anrop till `durable-functions` `orchestrator` modulens metod (här `df`).
3. Funktionen måste vara synkron. Eftersom Orchestrator-metoden hanterar anrop av Context. klar, ska funktionen bara returnera.

Objektet innehåller ett `df` objekt som låter dig anropa andra *aktivitets* `callActivity` funktioner och skicka indataparametrar med hjälp av metoden. `context` Koden anropar `E1_SayHello` tre gånger i följd med olika parameter värden, med `yield` hjälp av för att indikera att körningen ska vänta på att de asynkrona aktivitets funktions anropen ska returneras. Returvärdet för varje anrop läggs till `outputs` i listan, som returneras i slutet av funktionen.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function. JSON-fil

*Function. JSON* -filen för aktivitets `E1_SayHello` funktionen `E1_HelloSequence` liknar den, förutom att den använder en `activityTrigger` bindnings typ i stället för en `orchestrationTrigger` bindnings typ.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Alla funktioner som anropas av en Orchestration-funktion måste `activityTrigger` använda bindningen.

Implementeringen av `E1_SayHello` är en relativt trivial sträng formaterings åtgärd.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Den här funktionen har en parameter av typen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), som används för att hämta de inloggade ingången som skickades till den av [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)Orchestrator-funktionens anrop till.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från en JavaScript-Orchestration-funktion behöver en aktivitets funktion inga särskilda inställningar. Indatamängden som skickas till den av Orchestrator `context.bindings` `context.bindings.name`-funktionen finns på objektet under namnet `activityTrigger` på bindningen – i det här fallet. Bindnings namnet kan anges som en parameter för den exporterade funktionen och nås direkt, vilket är vad exempel koden gör.

## <a name="run-the-sample"></a>Kör exemplet

Skicka följande http `E1_HelloSequence` post-begäran för att köra dirigeringen.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Det tidigare http-kodfragmentet förutsätter att det finns `host.json` en post i filen som `api/` tar bort standardprefixet från alla URL: er för http-utlösare. Du hittar koden för den här konfigurationen i `host.json` filen i exemplen.

Om du till exempel kör exemplet i en Function-app med namnet "myfunctionapp" ersätter du {Host} med "myfunctionapp.azurewebsites.net".

Resultatet är ett HTTP 202-svar, som detta (trimmat för det kortfattat):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

I det här läget köas dirigeringen och börjar köras omedelbart. URL: en i `Location` rubriken kan användas för att kontrol lera status för körningen.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Resultatet är dirigeringens status. Den körs och slutförs snabbt, så du ser den i *slutfört* tillstånd med ett svar som ser ut så här (putsad för det kortfattat):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Som du kan se `runtimeStatus` `output` slutförs instansen och innehåller det JSON-serialiserade resultatet av Orchestrator Function-körningen.

> [!NOTE]
> Den HTTP POST-slutpunkt som startade Orchestrator-funktionen implementeras i exempel programmet som en HTTP-utlösare med namnet "HttpStart". Du kan implementera liknande start logik för andra utlösare, `queueTrigger`t `eventHubTrigger`. ex `timerTrigger`., eller.

Titta på funktions körnings loggarna. Funktionen startade och slutfördes flera gånger på grund av uppspelnings beteendet som beskrivs i översikten. [](durable-functions-concepts.md) `E1_HelloSequence` Å andra sidan fanns det bara tre körningar `E1_SayHello` eftersom dessa funktion körningar inte spelas upp.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat sig vara en enkel genom koppling av funktions länkning. I nästa exempel visas hur du implementerar mönstret för fläkt-out/fläktning.

> [!div class="nextstepaction"]
> [Köra fläkt-out/fläkt-in-exemplet](durable-functions-cloud-backup.md)
