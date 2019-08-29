---
title: Timers i Durable Functions – Azure
description: Lär dig hur du implementerar varaktiga timers i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087044"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) tillhandahåller *varaktiga timers* för användning i Orchestrator-funktioner för att implementera fördröjningar eller ställa in timeout för asynkrona åtgärder. Varaktiga timers bör användas i Orchestrator-funktioner i `Thread.Sleep` stället `Task.Delay` förC#och () `setTimeout()` , `setInterval()` eller och (Java Script).

Du skapar en varaktig timer genom att anropa [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) `createTimer` -metoden för [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) i .net eller med metoden `DurableOrchestrationContext` i Java Script. Metoden returnerar en uppgift som återupptas vid en angiven tidpunkt.

## <a name="timer-limitations"></a>Timer-begränsningar

När du skapar en timer som upphör att gälla 4:30 PM kommer det underliggande ständiga aktivitets ramverket att köa ett meddelande som bara är synligt på 4:30 PM. När du kör i Azure Functions förbruknings planen ser det nyligen synliga timer-meddelandet till att Function-appen aktive ras på en lämplig virtuell dator.

> [!NOTE]
> * Varaktiga timers kan inte vara längre än 7 dagar på grund av begränsningar i Azure Storage. Vi arbetar på en [funktions förfrågan för att förlänga timers efter sju dagar](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Använd alltid [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) i stället `DateTime.UtcNow` för i .net `currentUtcDateTime` och i `Date.now` stället `Date.UTC` för eller i Java Script som du ser i exemplen nedan när du beräknar en relativ deadline för en varaktig timer.

## <a name="usage-for-delay"></a>Användning för fördröjning

I följande exempel visas hur du använder varaktiga timers för att fördröja körningar. Exemplet utfärdar ett fakturerings meddelande varje dag i tio dagar.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Undvik oändlig loop i Orchestrator-funktioner. Information om hur du på ett säkert och effektivt sätt implementerar oändliga upprepnings scenarier finns i [Eternal](durable-functions-eternal-orchestrations.md)-dirigeringar.

## <a name="usage-for-timeout"></a>Användning för timeout

Det här exemplet illustrerar hur du använder varaktiga timers för att implementera tids gränser.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Använd en `CancellationTokenSource` för att avbryta en varaktig timerC#() eller `cancel()` anropa den returnerade `TimerTask` (Java Script) om din kod inte väntar på att den ska slutföras. Det varaktiga aktivitets ramverket ändrar inte dirigeringens status till slutförd förrän alla utestående aktiviteter har slutförts eller avbrutits.

Den här mekanismen avslutar egentligen inte körningen av aktivitets funktionen. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Om din Function-app använder förbruknings planen kommer du fortfarande att faktureras för all tid och minne som används av funktionen övergivna aktiviteter. Som standard har funktioner som körs i förbruknings planen en tids gräns på fem minuter. Om den här gränsen överskrids återvinns Azure Functionss värden för att stoppa all körning och förhindra en uppräknings situation. [Tids gränsen för funktionen kan konfigureras](../functions-host-json.md#functiontimeout).

Ett mer djupgående exempel på hur du implementerar tids gränser i Orchestrator-funktioner finns i genom gång av [mänsklig interaktion & timeout-telefon verifiering](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du höjer och hanterar externa händelser](durable-functions-external-events.md)
