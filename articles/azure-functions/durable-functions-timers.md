---
title: Timers i varaktiga funktioner – Azure
description: Lär dig hur du implementerar varaktiga timers i tillägget varaktiga funktioner för Azure Functions.
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
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 4fd86b70965a7be84c72e265af798292819cbe96
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762276"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i varaktiga funktioner (Azure-funktioner)

[Beständiga funktioner](durable-functions-overview.md) ger *varaktiga timers* för användning i orchestrator-funktioner att implementera fördröjningar eller ställa in timeout för asynkrona åtgärder. Beständiga timers ska användas i orchestrator-funktioner i stället för `Thread.Sleep` och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (JavaScript).

Du skapar en beständig timer genom att anropa den [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metod i [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Metoden returnerar en uppgift som återupptar på ett angivet datum och tid.

## <a name="timer-limitations"></a>Timer-begränsningar

När du skapar en timer som upphör att gälla på 4:30 pm, underliggande beständiga aktiviteten Framework enqueues ett meddelande som visas bara på 4:30 pm. När den körs i Azure Functions förbrukning planen säkerställer nyligen synliga tidsinställt meddelande att funktionen appen hämtar aktiveras på en lämplig virtuell dator.

> [!NOTE]
> * Beständiga timers sista kan inte längre än 7 dagar på grund av begränsningar i Azure Storage. Vi arbetar på en [funktionsbegäran om att utöka timers utöver 7 dagar](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Använd alltid [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) i stället för `DateTime.UtcNow` som visas i exemplen nedan när du beräknar en relativ tidsgräns i en beständig timer.

## <a name="usage-for-delay"></a>Användning för fördröjning

Följande exempel visar hur du använder beständiga timers för att fördröja körningen. Exemplet utfärdar ett fakturering meddelande varje dag för tio dagar.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> Undvik oändliga slingor i orchestrator-funktioner. Information om hur du implementerar slinga scenarier på ett säkert och effektivt finns [Eternal orkestreringarna](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Användning för timeout

Det här exemplet illustrerar hur du använder beständiga timers för att implementera timeout.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

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
> Använd en `CancellationTokenSource` att avbryta en beständig timer (C#) eller ett anrop `cancel()` på den returnerade `TimerTask` (JavaScript) om din kod inte ska vänta tills den är klar. Beständiga aktiviteten Framework ändrar inte en orchestration status till ”slutfört” tills alla utestående aktiviteter slutförts eller annullerats.

Den här mekanismen avslutar inte faktiskt funktionen för pågående aktivitetskörningen. I stället bara kan orchestrator-funktionen för att ignorera resultatet och gå vidare. Om funktionen appen använder förbrukning plan, debiteras du fortfarande för tids- och minne som används av funktionen övergivna aktivitet. Funktioner som körs i förbrukning planen har som standard en tidsgräns på fem minuter. Om den här gränsen överskrids återanvänds Azure Functions värden att stoppa alla körning och förhindra en skenande fakturering situation. Den [funktionen för timeout är konfigurerbar](functions-host-json.md#functiontimeout).

En mer ingående exempel på hur du implementerar tidsgränser i orchestrator-funktioner finns i [mänsklig interaktion & timeout - Telefonverifiering](durable-functions-phone-verification.md) genomgången.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att öka och hantera externa händelser](durable-functions-external-events.md)

