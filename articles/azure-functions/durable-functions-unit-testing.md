---
title: Azure varaktiga funktioner enhet testning
description: "Lär dig hur testa varaktiga funktioner till enheten."
services: functions
author: kadimitr
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: a8d2a2281dcaf6e5e308ad4a2aafb167f9ba9121
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="durable-functions-unit-testing"></a>Den beständiga funktioner enhet testning

Testa enheten är en viktig del av moderna software development praxis. Kontroller verifiera business logic beteende och skydda från att införa oupptäckta viktiga förändringar i framtiden. Beständiga funktioner kan enkelt växa komplexitet så introduktion till kontroller hjälper till att undvika att bryta ändringar. I följande avsnitt beskrivs hur testa tre funktionstyper - Orchestration-klient, Orchestrator och aktiviteten till enhet funktioner. 

## <a name="prerequisites"></a>Förutsättningar

Exemplen i den här artikeln kräver kunskap om följande begrepp och ramverk: 

* Testa enhet

* Bestående funktioner 

* [xUnit](https://xunit.github.io/) -tester framework

* [moq](https://github.com/moq/moq4) -Mocking framework


## <a name="base-classes-for-mocking"></a>Basklasser för mocking 

Mocking stöds via två abstrakta klasser i varaktiga funktioner:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

De här klasserna är basklasser för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) och [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) som definierar Orchestration-klienten och Orchestrator-metoder. Mocks anger förväntat beteende för basklassmetoder så att lagringsenheten testet kan kontrollera affärslogiken. Det finns ett tvåstegsverifiering arbetsflöde för testning av affärslogiken i Orchestration-klienten och Orchestrator:

1. Använda basklasserna i stället konkret implementering när du definierar Orchestration-klienten och Orchestrators signaturer.
2. Mock beteendet för basklasserna och kontrollera affärslogiken i enheten testerna. 

Mer information finns i följande stycken för att testa funktioner som använder orchestration-klienten bindning och orchestrator utlösa bindning.

## <a name="unit-testing-trigger-functions"></a>Enhet tester utlösaren funktioner

I det här avsnittet verifierar enhet testet logiken för följande HTTP-Utlösarfunktion för att starta nya orkestreringarna.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Test-aktivitet enheter är att kontrollera värdet för den `Retry-After` huvudet i svaret nyttolast. Så testet enhet kommer mock några av [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metoder för att kontrollera förutsägbart beteende. 

Först en mock i basklassen krävs [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Mock kan vara en ny klass som implementerar [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Men med ett mocking ramverk som [moq](https://github.com/moq/moq4) förenklar processen:    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Sedan `StartNewAsync` metoden mocked för att returnera ett välkänt instans-ID.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Nästa `CreateCheckStatusResponse` är mocked alltid returnera ett tomt 200 HTTP-svar.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

`TraceWriter` även mocked:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Nu den `Run` metoden anropas från enhetstest:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 Det sista steget är att jämföra resultatet med det förväntade värdet:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Efter att kombinera alla steg har enhet testet följande kod: 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Enhet testning orchestrator-funktioner

Orchestrator-funktioner är ännu mer intressant för testning eftersom de har vanligtvis mer affärslogik. Orchestrator-funktioner kan för närvarande kan implementeras i C#.

I det här avsnittet enheten tester validerar utdata från den `E1_HelloSequence` Orchestrator-funktion:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Testkod enhet börjar med att skapa en mock:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Sedan kommer du mocked metodanrop för aktiviteten:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Nästa enhet testet ska ringa `HelloSequence.Run` metoden:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Och slutligen utdata verifieras:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Efter att kombinera alla steg har enhet testet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Enhet tester aktivitet funktioner

Funktioner för aktiviteten kan vara enhet testas på samma sätt som icke-beständiga funktioner. Aktiviteten funktioner har inte en basklass för mocking. Därför använder enhet testerna parametertyperna direkt.

I det här avsnittet enhet testet verifierar beteendet för den `E1_SayHello` aktiviteten funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Och testet enhet ska kontrollera formatet på utdata:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [Mer information om moq](https://github.com/Moq/moq4/wiki/Quickstart)