---
title: Funktionen länkning i varaktiga funktioner – Azure
description: Lär dig hur du kör ett varaktiga funktioner-exempel som exekverar en följd av funktioner.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4657bd136592c66b5dab9a712f5f1d6df898876c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730555"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktionen länkning i varaktiga funktioner - Hello sekvens-exempel

Funktionen länkning avser mönstret för att köra en serie funktioner i en viss ordning. Utdata för en funktion måste ofta tillämpas till indata för en annan funktion. Den här artikeln beskriver chaining ordningsföljden som du skapar när du har slutfört snabbstarten varaktiga funktioner ([ C# ](durable-functions-create-first-csharp.md) eller [JavaScript](quickstart-js-vscode.md)). Mer information om varaktiga funktioner finns i [varaktiga funktioner, mönster och tekniska begrepp](durable-functions-concepts.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E1_HelloSequence`: En orchestrator-funktion som anropar `E1_SayHello` flera gånger i en sekvens. Den lagrar utdata från den `E1_SayHello` anropar och registrerar resultaten.
* `E1_SayHello`: En aktivitet-funktion som läggs en sträng med ”Hello”.

I följande avsnitt beskrivs konfiguration och kod som används för C#-skript och JavaScript. Kod för Visual Studio-utveckling visas i slutet av artikeln.

> [!NOTE]
> JavaScript varaktiga funktioner är tillgängliga för Functions 2.x-körningen endast.

## <a name="e1hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Function.JSON fil

Om du använder Visual Studio Code eller Azure-portalen för utveckling, här är innehållet i den *function.json* -filen för orchestrator-funktion. De flesta orchestrator *function.json* filer nästan precis likadant ut som detta.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Viktigt är den `orchestrationTrigger` bindningstyp. Alla orchestrator-funktioner måste använda den här utlösartypen.

> [!WARNING]
> Om du vill följa regeln ”ingen i/o” orchestrator-funktioner, inte använda några indata eller utdatabindningar när du använder den `orchestrationTrigger` utlösa bindning.  Om andra indata eller utdatabindningar behövs, de ska i stället användas i kontexten för `activityTrigger` funktioner, som anropas av orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#-skript (Visual Studio Code och Azure portal exempelkoden)

Här är källkoden:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alla C# orchestration-funktioner måste ha en parameter av typen `DurableOrchestrationContext`, som finns i den `Microsoft.Azure.WebJobs.Extensions.DurableTask` sammansättningen. Om du använder C#-skript, sammansättningen kan refereras med den `#r` notation. Den här context-objektet kan du anropa andra *aktivitet* funktioner och pass indataparametrar med hjälp av dess [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metod.

Koden anropar `E1_SayHello` tre gånger i följd med olika parametervärden. Returvärdet för varje anrop har lagts till i den `outputs` listan som returneras i slutet av funktionen.

### <a name="javascript"></a>Javascript

Här är källkoden:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alla funktioner i JavaScript orchestration måste innehålla den [ `durable-functions` modulen](https://www.npmjs.com/package/durable-functions). Det här är ett bibliotek som hjälper dig att skriva varaktiga funktioner i JavaScript. Det finns tre viktiga skillnader mellan en orchestration-funktion och andra JavaScript-funktioner:

1. Funktionen är en [generator-funktionen.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funktionen är inneslutna i ett anrop till den `durable-functions` modulens `orchestrator` metod (här `df`).
3. Funktionen måste vara synkrona. Eftersom metoden ”orchestrator” hanterar anropande context.done, ska funktionen bara 'Returnera'.

Den `context` objektet innehåller en `df` objekt kan du anropa andra *aktivitet* funktioner och pass indataparametrar med hjälp av dess `callActivity` metod. Koden anropar `E1_SayHello` tre gånger i följd med olika parametervärden, med hjälp av `yield` att visa körningen ska vänta på de asynkrona aktiviteten funktionsanrop som ska returneras. Returvärdet för varje anrop har lagts till i den `outputs` listan som returneras i slutet av funktionen.

## <a name="e1sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Function.JSON fil

Den *function.json* fil för funktionen aktivitet `E1_SayHello` är ungefär som `E1_HelloSequence` förutom att den använder en `activityTrigger` bindningstyp i stället för en `orchestrationTrigger` bindningstyp.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> En funktion som anropas av en orchestration-funktion måste använda den `activityTrigger` bindning.

Implementeringen av `E1_SayHello` är en relativt trivial sträng formatering igen.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Den här funktionen har en parameter av typen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), som används för att hämta indata som skickades till den av orchestrator-funktionens anrop till [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Till skillnad från en JavaScript-funktion orchestration behöver en aktivitet-funktionen ingen särskild konfiguration. Inmatningen som skickades till den av orchestrator-funktionen finns på den `context.bindings` objekt under namnet på den `activityTrigger` bindning – i det här fallet `context.bindings.name`. Bindningsnamn kan anges som en parameter för funktionen exporterade och öppnas direkt, vilket är vad exempelkoden gör.

## <a name="run-the-sample"></a>Kör exemplet

Att köra den `E1_HelloSequence` orchestration, skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Föregående HTTP-kodavsnitt förutsätter att det finns en post i den `host.json` fil som tar bort standard `api/` prefixet från alla HTTP-utlösaren functions URL: er. Du hittar koden för den här konfigurationen i den `host.json` filen i exemplen.

Till exempel om du kör exemplet i en funktionsapp med namnet ”myfunctionapp”, Ersätt ”{värd}” med ”myfunctionapp.azurewebsites.net”.

Resultatet är ett HTTP 202-svar, så här (tas bort av utrymmesskäl):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Nu är i kö för samordning och börjar köras omedelbart. URL: en i den `Location` huvudet kan användas för att kontrollera status för körningen.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Resultatet är status för dirigering. Det körs och är klar snabbt, så att du ser det i den *slutförd* tillstånd med ett svar som ser ut så här (tas bort av utrymmesskäl):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Som du ser den `runtimeStatus` på-instansen är *slutförd* och `output` innehåller JSON-serialiserat resultatet av körning för orchestrator-funktion.

> [!NOTE]
> HTTP POST-slutpunkten som startats i orchestrator-funktion har implementerats i exempelappen som en HTTP-Utlösarfunktion med namnet ”HttpStart”. Du kan implementera liknande starter logik för andra typer av utlösare som `queueTrigger`, `eventHubTrigger`, eller `timerTrigger`.

Titta på loggarna för jobbkörning av funktionen. Den `E1_HelloSequence` funktionen startades och slutfördes flera gånger på grund av repetitionsattacker problemet som beskrivs i den [översikt](durable-functions-concepts.md). Å andra sidan, fanns det bara tre körningar av `E1_SayHello` eftersom dessa funktionskörningar inte hämta återupprepas.

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat en enkel orchestration för länkning av funktionen. I nästa exempel visas hur du implementerar bläddra in/bläddra in-mönstret.

> [!div class="nextstepaction"]
> [Kör Fan-in/fan-i exemplet](durable-functions-cloud-backup.md)
