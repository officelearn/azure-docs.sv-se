---
title: "Tillståndskänsliga singletons i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar en tillståndskänslig singleton i tillägget varaktiga funktioner för Azure Functions."
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
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Tillståndskänsliga singletons i varaktiga funktioner - räknaren exempel

> [!NOTE]
> Vi skriva om det här exemplet. Kodavsnitten har tagits bort och en ny artikel för en ny exemplet ersätts av den.

Tillståndskänsliga singletons är långvariga (potentiellt eternal) orchestrator funktioner som kan lagra tillstånd och anropas och med andra funktioner. Tillståndskänsliga singletons liknar den [aktören modellen](https://en.wikipedia.org/wiki/Actor_model) i distribuerad datoranvändning.

När inte en korrekt ”aktör”-implementering har orchestrator-funktioner många av samma runtime-egenskaper. De är till exempel tillståndskänslig, tillförlitlig, Enkeltrådig, plats-transparent och globalt adresserbara. Dessa är egenskaper som gör verkliga aktören implementeringar användbart, men utan att behöva en separat ram.

Den här artikeln visar hur du kör den *räknaren* exempel. Exemplet visar en singletonobjekt som stöder *ökning* och *minska* åtgärder och uppdateras det interna tillståndet.

## <a name="prerequisites"></a>Förutsättningar

* Följ instruktionerna i [installera varaktiga funktioner](durable-functions-install.md) att ställa in provet.
* Den här artikeln förutsätter att du redan har gått igenom de [Hello sekvens](durable-functions-sequence.md) exempel genomgången.

## <a name="scenario-overview"></a>Scenarioöversikt

Räknaren scenario är förstås svåra att implementera vanliga tillståndslös funktionerna. En av de största utmaningarna som du hanterar **samtidighet**. Åtgärder som *ökning* och *minska* måste vara atomiska eller annan kan det finnas konkurrenstillstånd som orsakar åtgärder att skriva över varandra.

Med hjälp av en enda virtuell dator som värd för räknardata är en alternativ, men det är dyrt och hantera **tillförlitlighet** kan vara en utmaning eftersom en enda virtuell dator kan startas med jämna mellanrum. Du kan också använda en distribuerad plattform med synkronisering verktyg som blob-lån för att hantera samtidighet, men det medför en massa **komplexitet**.

Beständiga funktioner gör den här typen av scenario trivial att implementera eftersom orchestration-instanser är tillhör en enskild virtuell dator och körningen av orchestrator-funktionen alltid är enkeltrådad. Inte bara den, men de är långvariga och tillståndskänsliga och kan reagera på externa händelser. Exempelkoden nedan visar hur du implementerar dessa räknare som en tidskrävande orchestrator-funktion.

## <a name="the-sample-function"></a>Funktionen sample

Den här artikeln beskriver hur den **E3_Counter** funktion i sample-appen.



## <a name="the-counter-orchestration"></a>Räknaren orchestration

I följande avsnitt beskrivs den kod som används för utveckling av Visual Studio Code och Azure-portalen.

### <a name="c-script"></a>C#-skript

Filen function.json:

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

Filen run.csx:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>Precompiled C# 

I följande avsnitt beskrivs den kod som används för utveckling av Visual Studio.

Här är den kod som implementerar funktionen orchestrator:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>Förklaring av koden

Den här funktionen för orchestrator i grunden gör följande:

1. Lyssnar efter en extern händelse med namnet *åtgärden* med [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Steg eller minskar den `counterState` lokal variabel beroende på den begärda åtgärden.
3. Startar om orchestrator med de [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metod, ange det senaste värdet för `counterState` som nya indata.
4. Fortsätter att köra alltid eller tills en *end* meddelandet tas emot.

Det här är ett exempel på en *eternal orchestration* &mdash; som är en potentiellt aldrig tar slut. Den svarar på meddelanden som skickas till av den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metod kan anropas för en icke-orchestrator-funktion.

En unik egenskap för den här funktionen för orchestrator är effektivt har ingen historik: den `ContinueAsNew` metoden återställer historiken efter varje bearbetade händelse. Detta är det bästa sättet att implementera en orchestrator som har en godtycklig livstid. Med hjälp av en `while` loop kan leda till att funktionen orchestrator historik att växa unbounded, vilket resulterar i onödan hög minnesanvändning.

> [!NOTE]
> Den `ContinueAsNew` metoden har andra användningsfall förutom eternal orkestreringarna. Mer information finns i [Eternal orkestreringarna](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Kör exemplet

Du kan starta orchestration genom att skicka följande HTTP POST-begäran. Att tillåta `counterState` ska börja på noll (standardvärdet för `int`), det finns inget innehåll i denna begäran.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Den **E3_Counter** instans startar och sedan omedelbart väntar en händelse som ska skickas till den med hjälp av `RaiseEventAsync` eller med hjälp av den **sendEventUrl** HTTP POST webhook som refereras i 202 svaret. Giltig `eventName` värden är *incr*, *decr*, och *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Du kan se resultatet av åtgärden ”incr” genom att titta på funktionsloggar i Azure Functions-portalen.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

På samma sätt om du kontrollera status för orchestrator, visas den `input` fältet har ställts in på det uppdaterade värdet (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Du kan fortsätta att skicka nya funktioner till den här instansen och studera dess status uppdateras i enlighet därmed. Om du vill avsluta instansen måste du göra detta genom att skicka en *end* igen.

> [!WARNING]
> Vid tidpunkten för skrivning, det finns kända konkurrenstillstånd vid anrop av `ContinueAsNew` under bearbetning av meddelanden, till exempel externa händelser eller avslutning begäranden samtidigt. Den senaste informationen om dessa konkurrenstillstånd finns [GitHub problemet](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Nästa steg

Det här exemplet har du lärt dig att hantera [externa händelser](durable-functions-external-events.md) och implementera [eternal orkestreringarna](durable-functions-eternal-orchestrations.md) i [tillståndskänslig singletons](durable-functions-singletons.md). I nästa exempel visas hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Köra exemplet mänsklig interaktion](durable-functions-phone-verification.md)
