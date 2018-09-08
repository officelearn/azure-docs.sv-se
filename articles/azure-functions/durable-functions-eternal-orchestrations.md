---
title: Eternal-orkestreringar i varaktiga funktioner – Azure
description: Lär dig hur du implementerar eternal-orkestreringar med hjälp av tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 98504534332b6faa7a7019aea9ab7b534d4c3faa
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094458"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal-orkestreringar i varaktiga funktioner (Azure Functions)

*Eternal-orkestreringar* finns orchestrator-funktioner som aldrig slutar. De är användbara när du vill använda [varaktiga funktioner](durable-functions-overview.md) för aggregatorer och scenarier som kräver en oändlig loop.

## <a name="orchestration-history"></a>Orchestration-historik

Enligt beskrivningen i [kontrollpunkter och återuppspelning](durable-functions-checkpointing-and-replay.md), hållbar uppgift ramverket håller reda på historiken för varje funktion orchestration. Denna historik växer kontinuerligt så länge som orchestrator-funktion som fortsätter att schemalägga nytt arbete. Om orchestrator-funktion hamnar i en oändlig loop och schemalägger kontinuerligt arbete, kan denna historik ytterst växa och orsaka betydande prestandaproblem. Den *eternal orchestration* konceptet har utformats för att undvika dessa typer av problem för program som behöver oändliga slingor.

## <a name="resetting-and-restarting"></a>Återställa och starta om

I stället för att använda oändlig slingor kan återställa orchestrator-funktioner deras tillstånd genom att anropa den [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metod. Den här metoden tar ett enda JSON-serialiserbara parametern, som blir den nya indata för nästa generations för orchestrator-funktion.

När `ContinueAsNew` anropas, instans placerar det i kö ett meddelande till sig själv innan den avslutas. Meddelandet startar om instansen med det nya värdet för indata. Samma instans-ID är kvar men orchestrator-funktion historik trunkeras effektivt.

> [!NOTE]
> Hållbar uppgift ramverket har samma instans-ID men internt skapar en ny *körnings-ID* för orchestrator-funktion som hämtar återställa genom `ContinueAsNew`. Den här körnings-ID Allmänt exponeras inte externt, men det kan vara praktiskt att känna till när felsökning orchestration-körning.

> [!NOTE]
> Den `ContinueAsNew` metoden är ännu inte tillgänglig i JavaScript.

## <a name="periodic-work-example"></a>Periodiska arbete-exempel

Användningsfall för eternal-orkestreringar är kod som behöver utföra periodiska arbete på obestämd tid.

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

Skillnaden mellan det här exemplet och en timerutlöst funktion är att rensa utlösaren här gånger inte enligt ett schema. Till exempel ett CRON-schema som körs varje timme för en funktion ska köra den 1:00, 2:00, 3:00 osv och köra överlappning problem. I det här exemplet, men om rensningen tar 30 minuter sedan den schemaläggs vid 1:00, 2:30, 4:00 osv och det finns ingen risk för överlappar varandra.

## <a name="exit-from-an-eternal-orchestration"></a>Avsluta en eternal orkestrering

Om en orchestrator-funktion måste kommer att slutföras, så allt du behöver göra är att *inte* anropa `ContinueAsNew` och låt funktionen Avsluta.

Om en orchestrator-funktion finns i en oändlig loop och måste stoppas, använder du den [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metod för att stoppa den. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar singleton-orkestreringar](durable-functions-singletons.md)
