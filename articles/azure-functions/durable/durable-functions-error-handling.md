---
title: Hantering av fel i varaktiga funktioner - Azure
description: Lär dig hur du hanterar fel i tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277861"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hantering av fel i varaktiga funktioner (Azure-funktioner)

Varaktig funktion orkestreringar implementeras i kod och kan använda programmeringsspråkets inbyggda felhanteringsfunktioner. Det finns verkligen inte några nya begrepp som du behöver lära dig att lägga till felhantering och kompensation i dina orkestreringar. Det finns dock några beteenden som du bör vara medveten om.

## <a name="errors-in-activity-functions"></a>Fel i aktivitetsfunktioner

Alla undantag som genereras i en aktivitetsfunktion konverteras tillbaka till `FunctionFailedException`orchestrator-funktionen och genereras som en . Du kan skriva felhantering och kompensationskod som passar dina behov i orchestrator-funktionen.

Tänk dig till exempel följande orchestrator-funktion som överför medel från ett konto till ett annat:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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

> [!NOTE]
> De tidigare C#-exemplen är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Om det första CreditAccount-funktionsanropet misslyckas kompenserar orchestrator-funktionen genom att kreditera tillbaka pengarna till källkontot. **CreditAccount**

## <a name="automatic-retry-on-failure"></a>Automatiskt återförsök vid fel

När du anropar aktivitetsfunktioner eller underorkestreringsfunktioner kan du ange en princip för automatiskt återförsök. I följande exempel försöker du anropa en funktion upp till tre gånger och väntar 5 sekunder mellan varje nytt försök:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> De tidigare C#-exemplen är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Aktivitetsfunktionsanropet i föregående exempel tar en parameter för att konfigurera en automatisk återförsöksprincip. Det finns flera alternativ för att anpassa principen för automatiskt återförsök:

* **Max antal försök**: Det maximala antalet försök för återförsök.
* **Första återförsöksintervallet**: Den tid som ska vänta innan det första försöket försöker igen.
* **Backoff koefficient**: Den koefficient som används för att bestämma ökningstakten för backoff. Standard till 1.
* **Maximalt intervall för återförsök**: Den maximala tiden att vänta mellan försöken att försöka igen.
* **Timeout för återförsök:** Den maximala tiden att spendera på att göra återförsök. Standardbeteendet är att försöka igen på obestämd tid.
* **Handtag**: En användardefinierad motringning kan anges för att avgöra om en funktion ska göras på nytt.

## <a name="function-timeouts"></a>Tidsutgångar för funktion

Du kanske vill överge ett funktionsanrop i en orchestrator-funktion om det tar för lång tid att slutföra. Det korrekta sättet att göra detta idag är `context.CreateTimer` genom att `context.df.createTimer` skapa en [hållbar timer](durable-functions-timers.md) med (.NET) eller (JavaScript) tillsammans med `Task.WhenAny` (.NET) eller `context.df.Task.any` (JavaScript), som i följande exempel:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De tidigare C#-exemplen är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Den här mekanismen avslutar faktiskt inte körning av aktivitetsfunktionen under pågående. Snarare tillåter det helt enkelt orchestrator funktionen att ignorera resultatet och gå vidare. Mer information finns i [timers-dokumentationen.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Ohanterade undantag

Om en orchestrator-funktion misslyckas med ett ohanterat undantag loggas informationen om `Failed` undantaget och instansen slutförs med status.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om eviga orkestreringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Läs om hur du diagnostiserar problem](durable-functions-diagnostics.md)
