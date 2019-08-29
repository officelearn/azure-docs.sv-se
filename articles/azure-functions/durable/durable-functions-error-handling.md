---
title: Hantera fel i Durable Functions – Azure
description: Lär dig hur du hanterar fel i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 33d1b410119e631e0ccc9941beac1062d4ec30f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087334"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hantera fel i Durable Functions (Azure Functions)

Varaktiga funktions dirigering implementeras i kod och kan använda fel hanterings funktionerna i programmeringsspråket. Med detta i åtanke är det egentligen inga nya koncept som du behöver lära dig om att införliva fel hantering och kompensation i dina samordningar. Det finns dock några beteenden som du bör vara medveten om.

## <a name="errors-in-activity-functions"></a>Fel i aktivitets funktioner

Alla undantag som genereras i en aktivitets funktion ordnas tillbaka till Orchestrator-funktionen och genereras som en `FunctionFailedException`. Du kan skriva fel hantering och kompensations kod som passar dina behov i Orchestrator-funktionen.

Anta till exempel följande Orchestrator-funktion som överför fonder från ett konto till ett annat:

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Om anropet till funktionen **CreditAccount** Miss lyckas för mål kontot kompenserar Orchestrator-funktionen för detta genom att kreditera pengarna tillbaka till käll kontot.

## <a name="automatic-retry-on-failure"></a>Automatiskt försök vid fel

När du anropar aktivitets funktioner eller under Orchestration-funktioner kan du ange en princip för automatisk återförsök. Följande exempel försöker anropa en funktion upp till tre gånger och väntar 5 sekunder mellan varje nytt försök:

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

API: et `callActivityWithRetry` `RetryOptions` (.net) eller (Java Script) tar en parameter. `CallActivityWithRetryAsync` Under Dirigerings anrop med hjälp `CallSubOrchestratorWithRetryAsync` av (.net) `callSubOrchestratorWithRetry` eller (Java Script) API: et kan använda samma principer för återförsök.

Det finns flera alternativ för att anpassa principen för automatiskt återförsök. Dessa inkluderar:

* **Maximalt antal försök**: Maximalt antal nya försök.
* **Första återförsöksintervall**: Vänte tiden innan det första försöket.
* **Backoff-koefficient**: Den koefficient som används för att fastställa frekvensen av ökningen av backoff. Standardvärdet är 1.
* **Max återförsöksintervall**: Maximal vänte tid för att vänta på mellan återförsök.
* **Timeout för återförsök**: Maximal tid det tar att spendera nya försök. Standard beteendet är att försöka på obestämd tid.
* **Referens**: Du kan ange ett användardefinierat motanrop som avgör om ett funktions anrop ska göras eller inte.

## <a name="function-timeouts"></a>Funktions tids gränser

Du kanske vill överge ett funktions anrop i en Orchestrator-funktion om det tar för lång tid att slutföra. Det är ett bra sätt att göra detta idag genom att skapa en [varaktig timer](durable-functions-timers.md) med `context.CreateTimer` (.net `context.df.createTimer` ) eller (Java Script) `Task.WhenAny` tillsammans med (.net `context.df.Task.any` ) eller (Java Script), som i följande exempel:

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Den här mekanismen avslutar egentligen inte körningen av aktivitets funktionen. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Mer information finns i dokumentationen för [timers](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Ohanterade undantag

Om en Orchestrator-funktion Miss lyckas med ett ohanterat undantag loggas detaljerna för undantaget och instansen slutförs med en `Failed` status.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du diagnostiserar problem](durable-functions-diagnostics.md)
