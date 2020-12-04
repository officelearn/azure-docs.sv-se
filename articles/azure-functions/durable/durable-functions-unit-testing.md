---
title: Testning av Azure Durable Functions-enhet
description: Lär dig mer om att enhets test Durable Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 7786a0a2e2d31086e1938b70e63fe2374e16fe7f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601364"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions enhets testning

Enhets testning är en viktig del av modern program utvecklings praxis. Enhets testerna verifierar affärs logik beteendet och skyddar mot att vi introducerar ändringar som inte uppmärksammas i framtiden. Durable Functions kan lätt växa i komplexitet så att enhets testerna kan undvika att förlora ändringar. I följande avsnitt beskrivs hur du enheten testar de tre funktions typerna-Orchestration-klient, Orchestrator-och aktivitets funktioner.

> [!NOTE]
> Den här artikeln innehåller rikt linjer för enhets testning för Durable Functions appar som riktar in sig på Durable Functions 1. x. Den har ännu inte uppdaterats till ändringar som gjorts i Durable Functions 2. x. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

## <a name="prerequisites"></a>Förutsättningar

I exemplen i den här artikeln krävs kunskaper om följande begrepp och ramverk:

* Enhetstestning

* Bestående funktioner

* [xUnit](https://github.com/xunit/xunit) -testnings ramverk

* [MOQ](https://github.com/moq/moq4) -modellerande ramverk

## <a name="base-classes-for-mocking"></a>Bas klasser för att modellera

Det finns stöd för att modellera genom tre abstrakta klasser i Durable Functions 1. x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Dessa klasser är bas klasser för `DurableOrchestrationClient` , `DurableOrchestrationContext` och `DurableActivityContext` som definierar Orchestration-klient, Orchestrator-och aktivitets metoder. De här modellerna anger förväntat beteende för Bask lass metoder, vilket innebär att enhets testet kan verifiera affärs logiken. Det finns ett arbets flöde i två steg för enhet som testar affärs logiken i Orchestration-klienten och Orchestrator:

1. Använd bas klasserna i stället för konkret implementering när du definierar Dirigerings klienten och Orchestrator-funktionen signaturer.
2. I enhets testerna har du till gång till bas klassernas beteende och kontrollerar affärs logiken.

Mer information finns i följande stycken för att testa funktioner som använder Dirigerings klient bindning och Orchestrator trigger-bindning.

## <a name="unit-testing-trigger-functions"></a>Enhets testning utlöser funktioner

I det här avsnittet kommer enhets testet att validera logiken för följande HTTP-utlösare för att starta nya dirigeringar.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Uppgiften enhets test kommer att verifiera värdet för `Retry-After` huvudet i svarets nytto Last. Därför kommer enhets testet att modellera några av `DurableOrchestrationClientBase` metoderna för att säkerställa förutsägbara beteenden.

Först krävs en skiss av Bask Lassen `DurableOrchestrationClientBase` . De blå modellerna kan vara en ny klass som implementerar `DurableOrchestrationClientBase` . Men med hjälp av ett modell ramverk som [MOQ](https://github.com/moq/moq4) fören klar processen:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Sedan `StartNewAsync` är metoden fördelad för att returnera ett välkänt instans-ID.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Nu `CreateCheckStatusResponse` är det en tom HTTP 200-svar att returnera.

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

`ILogger` är också modell:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Nu `Run` anropas metoden från enhets testet:

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
        loggerMock.Object);
 ```

 Det sista steget är att jämföra utdata med det förväntade värdet:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Efter att ha kombinerat alla steg har enhets testet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Enhets testning för Orchestrator-funktioner

Orchestrator Functions är ännu mer intressant för enhets testning eftersom de ofta har mycket mer affärs logik.

I det här avsnittet kommer enhets testerna att verifiera utdata från `E1_HelloSequence` Orchestrator-funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Enhets test koden börjar med att skapa en modell:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Sedan blir aktivitets metod anropen blå:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Nästa enhets test anropar `HelloSequence.Run` metoden:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Slutligen kommer utdata att val IDE ras:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Efter att ha kombinerat alla steg har enhets testet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Enhets testning, aktivitets funktioner

Aktivitets funktioner kan vara en enhet testas på samma sätt som icke-varaktiga funktioner.

I det här avsnittet verifierar enhets testet beteendet för `E1_SayHello` funktionen activity:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Och enhets testerna kontrollerar utdataformatets format. Enhets testerna kan använda parameter typerna direkt eller en modell `DurableActivityContextBase` klass:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Läs mer om MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
