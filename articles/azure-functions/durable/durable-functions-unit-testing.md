---
title: Enhetstestning Azure varaktiga funktioner
description: Lär dig hur testa varaktiga funktioner till enheten.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 38db54889c1fff0406f4e21cea80e16b6fdc25d1
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105516"
---
# <a name="durable-functions-unit-testing"></a>Enhetstestning varaktiga funktioner

Enhetstestning är en viktig del av moderna programutvecklingsrutiner. Enhetstester verifiera business logic beteende och skydda mot introduktion till obemärkt förbi större ändringar i framtiden. Varaktiga funktioner kan enkelt växer i komplexitet så introducerar enhetstester hjälper till att undvika större ändringar. I följande avsnitt beskrivs hur testa tre funktionstyper - Orchestration-klienten, Orchestrator och aktivitet till enheten funktioner.

## <a name="prerequisites"></a>Förutsättningar

Exemplen i den här artikeln kräver kunskaper om följande begrepp och ramverk:

* Enhetstestning

* Bestående funktioner

* [xUnit](https://xunit.github.io/) -testning framework

* [moq](https://github.com/moq/moq4) -simulerade framework

## <a name="base-classes-for-mocking"></a>Basklasser för simulerade

Simulerade stöds via tre abstrakta klasser i varaktiga funktioner:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

De här klasserna är basklasser för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html), och [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som definierar Orkestreringsklient , Orchestrator och metoder för aktiviteten. Mocks anger förväntat beteende för basklassmetoder så att enhetstest kan kontrollera affärslogik. Det finns ett arbetsflöde för tvåstegsverifiering för Enhetstestning affärslogiken i Orkestreringsklient och Orchestrator:

1. Använd basklasserna i stället för konkreta implementeringen när du definierar Orkestreringsklient och Orchestrators signaturer.
2. Simulera beteende basklasserna och kontrollera affärslogiken i enhetstesterna.

Mer information finns i följande stycken för att testa funktioner som använder orkestreringsklient bindning och orchestrator utlösa bindning.

## <a name="unit-testing-trigger-functions"></a>Testa utlösarfunktionerna-enhet

I det här avsnittet verifierar test jednotky logiken för följande HTTP-Utlösarfunktion för att starta nya orkestreringar.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Test-aktivitet enheter kommer att kontrollera värdet för den `Retry-After` rubrik som angetts i svarets nyttolast. Så enhetstest ska simulera några av [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metoder för att säkerställa förutsägbar beteende.

Först ett utkast av basklassen krävs, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Utkast kan vara en ny klass som implementerar [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Men att använda ett simulerade ramverk som [moq](https://github.com/moq/moq4) förenklar processen:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Sedan `StartNewAsync` metoden mockade för att returnera ett välkänt instans-ID.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Nästa `CreateCheckStatusResponse` är simulerat att alltid returnera ett tomt HTTP 200-svar.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` även mockade:

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

När du kombinerar alla steg har i enhetstest följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Orchestrator-funktioner för Enhetstestning

Orchestrator-funktioner är ännu mer intressant för Enhetstestning eftersom de har vanligtvis mycket mer affärslogik.

I det här avsnittet enheten kommer testerna Validera utdata för den `E1_HelloSequence` Orchestrator-funktion:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Testa enhetskoden börjar med att skapa ett utkast:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Sedan kommer du mockade metodanrop aktivitet:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Bredvid enhetstest anropar `HelloSequence.Run` metoden:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Och slutligen utdata kommer att valideras:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

När du kombinerar alla steg har i enhetstest följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testa Aktivitetsfunktioner-enhet

Aktivitetsfunktioner kan vara enhet som har testats på samma sätt som icke-varaktiga funktioner.

I det här avsnittet test jednotky verifierar beteendet för den `E1_SayHello` aktivitet funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Och enhetstesterna ska kontrollera formatet för utdata. Enhetstesterna kan använda parametertyper direkt eller mock [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) klass:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)

> [Mer information om moq](https://github.com/Moq/moq4/wiki/Quickstart)
