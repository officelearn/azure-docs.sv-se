---
title: Timers i Durable Functions – Azure
description: Lär dig hur du implementerar varaktiga timers i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 463d5e6c253643c82935c82c7dee5996c8e44b5f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706108"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) tillhandahåller *varaktiga timers* för användning i Orchestrator-funktioner för att implementera fördröjningar eller ställa in timeout för asynkrona åtgärder. Varaktiga timers bör användas i Orchestrator-funktioner i stället för `Thread.Sleep` ochC#`Task.Delay` (), eller `setTimeout()` och `setInterval()` (Java Script).

Du skapar en varaktig timer genom att anropa metoden `CreateTimer` (.NET) eller metoden `createTimer` (JavaScript) i [bindningen för Orchestration-utlösaren](durable-functions-bindings.md#orchestration-trigger). Metoden returnerar en uppgift som slutförs vid en angiven tidpunkt.

## <a name="timer-limitations"></a>Timer-begränsningar

När du skapar en timer som upphör att gälla 4:30 PM kommer det underliggande ständiga aktivitets ramverket att köa ett meddelande som bara är synligt på 4:30 PM. När du kör i Azure Functions förbruknings planen ser det nyligen synliga timer-meddelandet till att Function-appen aktive ras på en lämplig virtuell dator.

> [!NOTE]
> * Varaktiga timers är för närvarande begränsade till sju dagar. Om längre fördröjningar behövs kan de simuleras med hjälp av timer-API: er i en `while` slinga.
> * Använd alltid `CurrentUtcDateTime` i stället för `DateTime.UtcNow` i .NET eller `currentUtcDateTime` i stället för `Date.now` eller `Date.UTC` i Java Script när du beräknar brand tiden för varaktiga timers. Mer information finns i artikeln om [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Användning för fördröjning

I följande exempel visas hur du använder varaktiga timers för att fördröja körningar. Exemplet utfärdar ett fakturerings meddelande varje dag i 10 dagar.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Föregående C# exempel är mål Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Undvik oändlig loop i Orchestrator-funktioner. Information om hur du på ett säkert och effektivt sätt implementerar oändliga upprepnings scenarier finns i [Eternal-dirigeringar](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Användning för timeout

Det här exemplet illustrerar hur du använder varaktiga timers för att implementera tids gränser.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Föregående C# exempel är mål Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

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
> Använd en `CancellationTokenSource` om du vill avbryta en beständig timer (.NET) eller anropa `cancel()` på den returnerade `TimerTask` (Java Script) om din kod inte väntar på att den ska slutföras. Det varaktiga aktivitets ramverket ändrar inte dirigeringens status till slutförd förrän alla utestående aktiviteter har slutförts eller avbrutits.

Den här avbrotts mekanismen avslutar inte aktivitets funktionen eller under Orchestration-körningar. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Om din Function-app använder förbruknings planen debiteras du fortfarande för all tid och minne som används av funktionen övergivna aktiviteter. Som standard har funktioner som körs i förbruknings planen en tids gräns på fem minuter. Om den här gränsen överskrids återvinns Azure Functionss värden för att stoppa all körning och förhindra en uppräknings situation. [Tids gränsen för funktionen kan konfigureras](../functions-host-json.md#functiontimeout).

Ett mer djupgående exempel på hur du implementerar tids gränser i Orchestrator-funktioner finns i artikeln om [tids gränser för mänsklig interaktion &-telefon verifiering](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du höjer och hanterar externa händelser](durable-functions-external-events.md)
