---
title: Timers i varaktiga funktioner – Azure
description: Lär dig hur du implementerar varaktiga timers i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: a05f75a7e38ee7cd4dc056629d9acaacad875e08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730233"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i varaktiga funktioner (Azure Functions)

[Varaktiga funktioner](durable-functions-overview.md) ger *varaktiga timers* för användning i orchestrator-funktioner att implementera fördröjningar eller ställa in timeouter på asynkrona åtgärder. Hållbar timers ska användas i orchestrator-funktioner i stället för `Thread.Sleep` och `Task.Delay` (C#) eller `setTimeout()` och `setInterval()` (JavaScript).

Du skapar en hållbar timer genom att anropa den [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) -metoden för [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) i .NET, eller `createTimer` -metoden för `DurableOrchestrationContext` i JavaScript. Metoden returnerar en uppgift som återupptar på ett angivet datum och tid.

## <a name="timer-limitations"></a>Begränsningar för timer

När du skapar en timer som upphör att gälla 4:30 pm, den underliggande varaktiga uppgift Framework placerar det i kö ett meddelande som visas endast på 4:30 pm. När du kör i Azure Functions-förbrukningsplanen säkerställer nyligen synliga timer-meddelande att appen hämtar aktiverats på en lämplig virtuell dator.

> [!NOTE]
> * Hållbar timers kan inte räcker längre än 7 dagar på grund av begränsningar i Azure Storage. Vi arbetar på en [funktionsförfrågan att utöka timers längre än 7 dagar](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Använd alltid [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) i stället för `DateTime.UtcNow` i .NET och `currentUtcDateTime` i stället för `Date.now` eller `Date.UTC` i JavaScript som visas i exemplen nedan när du beräknar en tidsgräns som är relativ i en hållbar timer .

## <a name="usage-for-delay"></a>Användning för fördröjning

I följande exempel visas hur du använder varaktiga timers om man vill senarelägga körning. I exempel utfärdar ett fakturering meddelande varje dag under tio dagar.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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
> Undvika oändliga slingor i orchestrator-funktioner. Information om hur du säkert och effektivt implementerar oändlig loop-scenarier finns i [Eternal-Orkestreringar](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Användning för tidsgräns

Det här exemplet illustrerar hur du använder varaktiga timers för att implementera tidsgränser.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

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
> Använd en `CancellationTokenSource` att avbryta en hållbar timer (C#) eller ett anrop `cancel()` på den returnerade `TimerTask` (JavaScript) om din kod inte väntar på att den är klar. Hållbar uppgift ramverket ändras inte en orkestrering status som ”slutförd” tills alla utestående aktiviteter är slutförda eller avbrutna.

Den här mekanismen avslutar inte faktiskt pågående aktivitetskörning av funktion. Det helt enkelt står orchestrator-funktion att ignorera resultatet och gå vidare. Om din funktionsapp använder förbrukningsplanen, debiteras du ändå för tids- och minne som används av funktionen övergivet aktivitet. Som standard har funktioner som körs i förbrukningsplanen en tidsgräns på fem minuter. Om den här gränsen överskrids, återanvänds Azure Functions-värden att stoppa alla körning och förhindra en lång körningstid fakturering situation. Den [funktionen timeout är konfigurerbara](../functions-host-json.md#functiontimeout).

En mer ingående exempel på hur du implementerar tidsgränser i orchestrator-funktioner finns i den [mänsklig interaktion & timeout - Telefonverifiering](durable-functions-phone-verification.md) genomgången.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att öka och hantera externa händelser](durable-functions-external-events.md)
