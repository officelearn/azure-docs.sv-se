---
title: Timers i varaktiga funktioner - Azure
description: Lär dig hur du implementerar varaktiga timers i tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261491"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers i varaktiga funktioner (Azure-funktioner)

[Varaktiga funktioner](durable-functions-overview.md) ger *varaktiga timers* för användning i orchestrator-funktioner för att implementera fördröjningar eller för att ställa in tidsutgångar på asynkronåtgärder. Varaktiga timers bör användas i `Thread.Sleep` orchestrator-funktioner i stället för och `Task.Delay` (C#), eller `setTimeout()` och `setInterval()` (JavaScript).

Du skapar en varaktig timer genom att `CreateTimer` anropa `createTimer` metoden (.NET) eller (JavaScript)-metoden för [orkesterreleringsutlösaren](durable-functions-bindings.md#orchestration-trigger). Metoden returnerar en aktivitet som slutförs på ett angivet datum och en viss tid.

## <a name="timer-limitations"></a>Timer begränsningar

När du skapar en timer som upphör att gälla 16:30, följer det underliggande varaktiga aktivitetsramverket ett meddelande som blir synligt först klockan 16.30. När du kör i Azure Functions Consumption plan, kommer det nyligen synliga timermeddelandet att se till att funktionsappen aktiveras på en lämplig virtuell dator.

> [!NOTE]
> * Varaktiga timers är för närvarande begränsade till 7 dagar. Om längre fördröjningar behövs kan de simuleras med timer-API:erna i en `while` loop.
> * Använd `CurrentUtcDateTime` alltid `DateTime.UtcNow` i stället `currentUtcDateTime` för `Date.now` i `Date.UTC` .NET eller i stället för eller i JavaScript när du beräknar brandtiden för hållbara timers. Mer information finns i artikeln [orchestrator funktionskodbegränsningar.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Användning för fördröjning

Följande exempel illustrerar hur du använder varaktiga timers för att fördröja körningen. Exemplet är att utfärda en faktureringsavisering varje dag i 10 dagar.

# <a name="c"></a>[C#](#tab/csharp)

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
> Det föregående C#-exemplet är inriktat på varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Undvik oändliga loopar i orchestrator funktioner. Information om hur du på ett säkert och effektivt sätt implementerar oändliga loopscenarier finns i [Eviga orkestreringar](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Användning för timeout

Det här exemplet illustrerar hur du använder varaktiga timers för att implementera timeout.

# <a name="c"></a>[C#](#tab/csharp)

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
> Det föregående C#-exemplet är inriktat på varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Använd `CancellationTokenSource` en (.NET) `cancel()` eller ring `TimerTask` på den returnerade (JavaScript) för att avbryta en varaktig timer om koden inte väntar på att den ska slutföras. Det varaktiga aktivitetsramverket ändrar inte en orkestrerings status till "slutförd" förrän alla utestående aktiviteter har slutförts eller avbrutits.

Den här annulleringsmekanismen avslutar inte pågående aktivitetsfunktion eller underorkestreringskörningar. Snarare tillåter det helt enkelt orchestrator funktionen att ignorera resultatet och gå vidare. Om funktionsappen använder förbrukningsplanen debiteras du fortfarande när som helst och minne som förbrukas av funktionen övergiven aktivitet. Som standard har funktioner som körs i förbrukningsplanen en timeout på fem minuter. Om den här gränsen överskrids återvinns Azure Functions-värden för att stoppa all körning och förhindra en tillfällig faktureringssituation. [Tidsgränsen för funktionen kan konfigureras](../functions-host-json.md#functiontimeout).

Ett mer djupgående exempel på hur du implementerar timeout i orchestrator-funktioner finns i artikeln [Human Interaction & Timeouts - Phone Verification.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du höjer och hanterar externa händelser](durable-functions-external-events.md)
