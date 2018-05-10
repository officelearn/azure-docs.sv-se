---
title: Funktionen länkning i varaktiga funktioner – Azure
description: Lär dig hur du kör ett exempel på varaktiga funktioner som exekverar en följd av funktioner.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: e53b38bf336816ca670fad3ab70a43e5cc8b3437
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktionen länkning i varaktiga funktioner - Hello sekvens-exempel

Funktionen länkning avser mönstret för att köra en sekvens av funktioner i en viss ordning. Ofta måste resultatet av en funktion tillämpas på indata för en annan funktion. Den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) att implementera funktionen länkning.

## <a name="prerequisites"></a>Förutsättningar

* [Installera varaktiga funktioner](durable-functions-install.md).

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E1_HelloSequence`: En orchestrator-funktion som anropar `E1_SayHello` flera gånger i en sekvens. Den lagrar utdata från den `E1_SayHello` anropar och registrerar resultaten.
* `E1_SayHello`: En aktivitet funktion som läggs en sträng med ”Hello”.

I följande avsnitt beskrivs konfiguration och kod som används för C# skript och JavaScript. Kod för Visual Studio-utveckling visas i slutet av artikeln.

> [!NOTE]
> Beständiga funktioner finns i JavaScript i v2 funktioner körningsmiljön endast.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>Function.JSON fil

Om du använder Visual Studio-koden eller Azure-portalen för utveckling, här är innehållet i den *function.json* filen för orchestrator-funktionen. De flesta orchestrator *function.json* filer ut nästan precis så här.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Viktigt är den `orchestrationTrigger` bindningstyp. Alla orchestrator-funktioner måste använda den här typen av utlösare.

> [!WARNING]
> Om du vill följa av regeln ”ingen i/o” orchestrator-funktioner inte använda några indata eller utdata bindningar när du använder den `orchestrationTrigger` utlösa bindning.  Om andra indata eller utdata bindningar krävs, de bör i stället användas i kontexten för `activityTrigger` funktioner som anropas av orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#-skript (Visual Studio Code och Azure portal exempelkod) 

Här är källkoden:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alla C# orchestration-funktioner måste ha en parameter av typen `DurableOrchestrationContext`, som finns i den `Microsoft.Azure.WebJobs.Extensions.DurableTask` sammansättning. Om du använder C# skript för sammansättningen kan refereras med den `#r` notation. Context-objektet kan du anropa andra *aktiviteten* funktioner och pass indataparametrar med dess [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metod.

Koden anropar `E1_SayHello` tre gånger i följd med olika parametervärden. Returvärdet för varje anrop har lagts till i den `outputs` listan som returneras i slutet av funktionen.

### <a name="javascript"></a>Javascript

Här är källkoden:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla JavaScript orchestration-funktioner måste innehålla den `durable-functions` modul. Det här är ett JavaScript-bibliotek som omvandlar funktionen orchestration-åtgärder till Varaktigas körning av protokollet för utanför processen språk. Det finns tre betydande skillnader mellan en orchestration-funktionen och andra JavaScript-funktioner:

1. Funktionen är en [generator-funktionen.](https://docs.microsoft.com/en-us/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funktionen kapslas in i ett anrop till den `durable-functions` modul (här `df`).
3. Funktionen avslutas genom att anropa `return`, inte `context.done`.

Den `context` objektet innehåller en `df` objekt kan du anropa andra *aktiviteten* funktioner och pass indataparametrar med dess `callActivityAsync` metod. Koden anropar `E1_SayHello` tre gånger i följd med olika parametervärden som använder `yield` att ange körningen ska vänta på de asynkrona aktiviteten funktionsanrop som ska returneras. Returvärdet för varje anrop har lagts till i den `outputs` listan som returneras i slutet av funktionen.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>Function.JSON fil

Den *function.json* fil för aktiviteten funktionen `E1_SayHello` är samma som `E1_HelloSequence` förutom att den använder en `activityTrigger` bindningstyp i stället för en `orchestrationTrigger` bindningstyp.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> En funktion som anropas av en orchestration-funktion måste använda den `activityTrigger` bindning.

Implementeringen av `E1_SayHello` är en relativt trivial sträng formatering igen.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Den här funktionen har en parameter av typen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), som används för att hämta indata som skickades till den av funktionen orchestrator anrop till [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från ett JavaScript orchestration-funktionen måste en aktivitet JavaScript-funktion inga särskilda inställningar. Inmatningen som skickades till den av orchestrator-funktionen finns på den `context.bindings` objekt under namnet på den `activitytrigger` bindning – i det här fallet `context.bindings.name`. Bindningsnamnet kan anges som en parameter för funktionen exporterade och direkt åtkomst som exempelkoden har.

## <a name="run-the-sample"></a>Kör exemplet

Att köra den `E1_HelloSequence` orchestration, skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Om du kör exemplet i en funktionsapp med namnet ”myfunctionapp”, ersätter du till exempel ”{värd}” med ”myfunctionapp.azurewebsites.net”.

Resultatet är ett 202 HTTP-svar så här (trimmade planeringsaspekter):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Nu är i kö orchestration och börjar köras omedelbart. URL-Adressen i det `Location` huvudet kan användas för att kontrollera status för körningen.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Resultatet är status för orchestration. Det körs och är klar snabbt, så att du ser den i den *slutförd* tillstånd med ett svar som ser ut så här (trimmade planeringsaspekter):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Som du ser den `runtimeStatus` för instansen är *slutförd* och `output` innehåller JSON-serialiserad resultatet av körningen för orchestrator-funktionen.

> [!NOTE]
> HTTP POST-slutpunkt som startats av orchestrator-funktionen är implementerad i sample-appen som en HTTP-utlösa funktion med namnet ”HttpStart”. Du kan implementera liknande starter logik för andra typer av utlösare som `queueTrigger`, `eventHubTrigger`, eller `timerTrigger`.

Titta på körningsloggar funktion. Den `E1_HelloSequence` funktionen startades och slutfördes flera gånger på grund av replay-problemet som beskrivs i den [översikt](durable-functions-overview.md). Å andra sidan fanns det bara tre körningar av `E1_SayHello` eftersom dessa funktionen körningar inte hämta spelas.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-filen i Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat en enkel funktion länkning orchestration. I nästa exempel visas hur du implementerar fan-in/fan-i mönstret. 

> [!div class="nextstepaction"]
> [Köra Fan-in/fan-i exemplet](durable-functions-cloud-backup.md)
