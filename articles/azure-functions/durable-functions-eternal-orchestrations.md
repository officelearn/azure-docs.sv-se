---
title: "Eternal orkestreringarna i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar eternal orkestreringarna med filnamnstillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 8560921734e5037182955d8fe9605cc9831bf1b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal orkestreringarna i varaktiga funktioner (Azure-funktioner)

*Eternal orkestreringarna* orchestrator-funktioner som aldrig avslutas. De är användbara när du vill använda [varaktiga funktioner](durable-functions-overview.md) för aggregatorer och alla scenario som kräver en oändlig loop.

## <a name="orchestration-history"></a>Orchestration-historik

Enligt beskrivningen i [kontrollpunkter och omsändning](durable-functions-checkpointing-and-replay.md), beständiga aktiviteten Framework håller reda på historiken för varje funktion orchestration. Denna historik växer kontinuerligt så länge funktionen orchestrator fortsätter att schemalägga nytt arbete. Om funktionen orchestrator hamnar i en oändlig loop och kontinuerligt schemalägger arbete, kan denna historik ytterst växa och orsaka betydande prestandaproblem. Den *eternal orchestration* konceptet har utformats för att minimera dessa typer av problem för program som behöver oändliga slingor.

## <a name="resetting-and-restarting"></a>Återställa och starta om

Istället för att använda oändliga slingor, Återställ orchestrator-funktioner deras tillstånd genom att anropa den [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metod. Den här metoden tar en JSON-serialiserbara parameter, som blir den nya indata för nästa generations för orchestrator-funktionen.

När `ContinueAsNew` anropas, instans enqueues ett meddelande till sig själv innan den avslutas. Meddelandet startar om instansen med nya indatavärdet. Samma instans-ID är kvar men funktionen orchestrator historik trunkeras effektivt.

> [!NOTE]
> Beständiga aktiviteten Framework behålls samma instans-ID men internt skapar en ny *körnings-ID* för orchestrator-funktionen som hämtar återställs av `ContinueAsNew`. Körnings-ID är vanligtvis inte exponerad externt, men det kan vara bra att känna till när du felsöker orchestration-körning.

## <a name="periodic-work-example"></a>Periodiska arbetsuppgifter exempel

Användningsfall för eternal orkestreringarna är kod som behöver utföra regelbundna arbete på obestämd tid.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

Skillnaden mellan det här exemplet och en timer-utlösta funktion är att rensa utlösaren gånger här inte är baserade på ett schema. Till exempel ett CRON-schema som utför en funktion i timmen körs den 1:00, 2:00, 3:00 etc. och köra till problem med överlappar varandra. I det här exemplet, men om rensningen tar 30 minuter sedan den kommer att schemaläggas 1:00, 2:30, 4:00, etc. och det finns ingen risk för överlappar varandra.

## <a name="counter-example"></a>Räknaren exempel

Här är ett förenklat exempel på en *räknaren* funktion som lyssnar efter *ökning* och *minska* händelser för evigt.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Avsluta en eternal orchestration

Om en orchestrator-funktion måste kommer att slutföras, så du behöver *inte* anropa `ContinueAsNew` och låta funktionen Avsluta.

Om en orchestrator-funktionen är i en oändlig loop och måste stoppas använder den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metod för att stoppa den. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar singleton orkestreringarna](durable-functions-singletons.md)

> [!div class="nextstepaction"]
> [Kör ett exempel eternal orchestration](durable-functions-counter.md)
