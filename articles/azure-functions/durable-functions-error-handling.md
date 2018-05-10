---
title: Hantera fel i varaktiga funktioner – Azure
description: Lär dig mer om att hantera fel i tillägget varaktiga funktioner för Azure Functions.
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
ms.openlocfilehash: 944fab5ccc55bc9a697e870208338bd0e697672d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hantera fel i varaktiga funktioner (Azure-funktioner)

Beständiga funktionen orkestreringarna implementeras i kod och kan använda funktionerna i programmeringsspråket felhantering. Med detta i åtanke det verkligen inte finns några nya begrepp som du behöver lära dig om när du använder felhantering och ersättning i din orkestreringarna. Det finns dock några problem som du bör känna till.

## <a name="errors-in-activity-functions"></a>Fel i aktiviteten funktioner

Alla undantag utlöstes i en aktivitet funktionen ordnas tillbaka till orchestrator-funktionen och sig som en `FunctionFailedException`. Du kan skriva hantering och ersättning felkoden som passar dina behov i orchestrator-funktionen.

Tänk dig följande funktion i orchestrator som överför medel från ett konto till en annan:

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

Om anropet till den **CreditAccount** misslyckas åtgärden för mål-kontot, funktionen orchestrator kompenserar för detta genom kreditering medel tillbaka till källkonto.

## <a name="automatic-retry-on-failure"></a>Automatiska försök vid fel

När du anropar aktivitet funktioner eller underordnade orchestration-funktioner som du kan ange en automatisk försök princip. I följande exempel försöker anropa en funktion upp till 3 gånger och väntar 5 sekunder mellan varje försök:

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

Den `CallActivityWithRetryAsync` API tar en `RetryOptions` parameter. Underordnade orchestration anrop med hjälp av den `CallSubOrchestratorWithRetryAsync` API kan använda dessa principer med samma försök igen.

Det finns flera alternativ för att anpassa principen automatiska försök igen. Dessa inkluderar:

* **Max antal försök**: det maximala antalet nya försök.
* **Första återförsöket**: hur lång tid att vänta innan det första förnyade försök.
* **Backoff värde**: värde används för att avgöra ökningstakt för backoff. Standardvärdet är 1.
* **Max återförsöksintervall**: maximal mängd väntetiden mellan ett nytt försök görs.
* **Gör timeout**: den maximala mängden tid för detta återförsök. Standardinställningen är att försöka igen under obestämd tid.
* **Anpassad**: en användardefinierad motringning kan anges som bestämmer om ett funktionsanrop ska göras.

## <a name="function-timeouts"></a>Funktionen tidsgränser

Du kanske vill Avbryt ett funktionsanrop i en orchestrator-funktion om det tar för lång tid att slutföra. Det korrekta sättet att göra detta idag är genom att skapa en [varaktiga timer](durable-functions-timers.md) med `context.CreateTimer` tillsammans med `Task.WhenAny`, som i följande exempel:

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

> [!NOTE]
> Den här mekanismen avslutar inte faktiskt funktionen för pågående aktivitetskörningen. I stället bara kan orchestrator-funktionen för att ignorera resultatet och gå vidare. Finns det [Timers](durable-functions-timers.md#usage-for-timeout) dokumentationen för mer information.

## <a name="unhandled-exceptions"></a>Ohanterat undantag

Om en orchestrator-funktion misslyckas med ett ohanterat undantag loggas information om undantaget och instansen är klar med en `Failed` status.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att felsöka problem](durable-functions-diagnostics.md)
