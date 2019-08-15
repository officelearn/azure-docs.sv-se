---
title: Testning av Azure Durable Functions-enhet
description: Lär dig mer om att enhets test Durable Functions.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: glenga
ms.openlocfilehash: 0080365853e7a9c74d3ba0e5efb06ce5a3af2a21
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967105"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions enhets testning

Enhets testning är en viktig del av modern program utvecklings praxis. Enhets testerna verifierar affärs logik beteendet och skyddar mot att vi introducerar ändringar som inte uppmärksammas i framtiden. Durable Functions kan lätt växa i komplexitet så att enhets testerna kan undvika att förlora ändringar. I följande avsnitt beskrivs hur du enheten testar de tre funktions typerna-Orchestration-klient, Orchestrator-och aktivitets funktioner.

## <a name="prerequisites"></a>Förutsättningar

I exemplen i den här artikeln krävs kunskaper om följande begrepp och ramverk:

* Enhetstestning

* Durable Functions

* [xUnit](https://xunit.github.io/) -testnings ramverk

* [MOQ](https://github.com/moq/moq4) -modellerande ramverk

## <a name="base-classes-for-mocking"></a>Bas klasser för att modellera

Det finns stöd för att modellera genom tre abstrakta klasser i Durable Functions:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Dessa klasser är bas klasser för [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)och [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) som definierar metoder för Orchestration-klient, Orchestrator och aktivitet. De här modellerna anger förväntat beteende för Bask lass metoder, vilket innebär att enhets testet kan verifiera affärs logiken. Det finns ett arbets flöde i två steg för enhet som testar affärs logiken i Orchestration-klienten och Orchestrator:

1. Använd bas klasserna i stället för konkret implementering när du definierar Orchestration-klienten och Orchestrator-signaturer.
2. I enhets testerna har du till gång till bas klassernas beteende och kontrollerar affärs logiken.

Mer information finns i följande stycken för att testa funktioner som använder Dirigerings klient bindning och Orchestrator trigger-bindning.

## <a name="unit-testing-trigger-functions"></a>Enhets testning utlöser funktioner

I det här avsnittet kommer enhets testet att validera logiken för följande HTTP-utlösare för att starta nya dirigeringar.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Uppgiften enhets test kommer att verifiera värdet för `Retry-After` huvudet i svarets nytto Last. Därför kommer enhets testet att modellera några av [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) -metoderna för att säkerställa förutsägbara beteenden.

Först krävs en skiss av Bask Lassen, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). De blå kan vara en ny klass som implementerar [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Men med hjälp av ett modell ramverk som [MOQ](https://github.com/moq/moq4) fören klar processen:

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

Nu `CreateCheckStatusResponse` är det en tom http 200-svar att returnera.

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

`ILogger`är också modell:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();

```  

Nu anropas `Run` metoden från enhets testet:

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

I det här avsnittet kommer enhets testerna att verifiera utdata `E1_HelloSequence` från Orchestrator-funktionen:

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

Och enhets testerna kontrollerar utdataformatets format. Enhets testerna kan använda parameter typer direkt eller en blå [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) -klass:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Läs mer om MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
