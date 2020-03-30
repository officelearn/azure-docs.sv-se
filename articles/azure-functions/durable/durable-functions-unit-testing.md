---
title: Testning av Azure Durable Functions-enhet
description: Lär dig hur du enhetstesta varaktiga funktioner.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231227"
---
# <a name="durable-functions-unit-testing"></a>Provning av enheten för varaktiga funktioner

Enhetstestning är en viktig del av modern mjukvaruutvecklingspraxis. Enhetstester verifierar affärslogikbeteende och skyddar mot att införa obemärkt brytande förändringar i framtiden. Varaktiga funktioner kan lätt växa i komplexitet så att införa enhetstester kommer att bidra till att undvika att bryta förändringar. I följande avsnitt beskrivs hur du enhetstestar de tre funktionstyperna - Orchestration-klient, orchestrator och aktivitetsfunktioner.

> [!NOTE]
> Den här artikeln innehåller vägledning för enhetstestning för appar med varaktiga funktioner som riktar sig till varaktiga funktioner 1.x. Det har ännu inte uppdaterats för att ta hänsyn till ändringar som införts i Varaktiga Funktioner 2.x. Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

## <a name="prerequisites"></a>Krav

Exemplen i den här artikeln kräver kunskap om följande begrepp och ramverk:

* Enhetstestning

* Bestående funktioner

* [xUnit](https://xunit.github.io/) - Testramverk

* [moq](https://github.com/moq/moq4) - Hånfulla ram

## <a name="base-classes-for-mocking"></a>Basklasser för hån

Hån stöds via tre abstrakta klasser i Varaktiga funktioner 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Dessa klasser är `DurableOrchestrationClient`basklasser för , `DurableOrchestrationContext`och `DurableActivityContext` som definierar metoder för Orchestration Client, Orchestrator och Activity. Den hånar kommer att ange förväntat beteende för basklass metoder så att enheten testet kan verifiera affärslogiken. Det finns ett arbetsflöde i två steg för enhet som testar affärslogiken i Orchestration Client och Orchestrator:

1. Använd basklasserna i stället för den konkreta implementeringen när orchestration-klienten och orchestrator-funktionssignaturerna ska definieras.
2. I enhetstesterna hånar basklassernas beteende och verifierar affärslogiken.

Mer information finns i följande stycken för att testa funktioner som använder orchestration-klientbindningen och orchestrator-utlösarbindningen.

## <a name="unit-testing-trigger-functions"></a>Utlösande funktioner för enhetstestning

I det här avsnittet validerar enhetstestet logiken i följande HTTP-utlösarfunktion för att starta nya orkestreringar.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Enhetstestaktiviteten är att verifiera `Retry-After` värdet för huvudet som anges i svarsnyttolasten. Så enhetstestet kommer `DurableOrchestrationClientBase` att håna några av metoderna för att säkerställa förutsägbart beteende.

Först krävs ett hån mot basklassen. `DurableOrchestrationClientBase` Den håna kan vara en `DurableOrchestrationClientBase`ny klass som implementerar . Men med hjälp av en hånfull ram som [moq](https://github.com/moq/moq4) förenklar processen:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Sedan `StartNewAsync` används metoden för att returnera ett välkänt instans-ID.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Nästa `CreateCheckStatusResponse` är hånad att alltid returnera ett tomt HTTP 200-svar.

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

`ILogger`är också hånad:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Nu `Run` anropas metoden från enhetstestet:

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

När du har kombinerat alla steg har enhetstestet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Enhetstestorkesorketorfunktioner

Orchestrator funktioner är ännu mer intressant för enhetstestning eftersom de oftast har mycket mer affärslogik.

I det här avsnittet validerar enhetstester utdata för `E1_HelloSequence` Orchestrator-funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Enhetstestkoden börjar med att skapa ett utkast:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Då kommer anropar aktivitetsmetoden:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Nästa enhetstest `HelloSequence.Run` kommer att anropa metod:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Och slutligen produktionen kommer att valideras:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

När du har kombinerat alla steg har enhetstestet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Aktivitetsfunktioner för enhetstestning

Aktivitetsfunktioner kan enhetstestas på samma sätt som icke-hållbara funktioner.

I det här avsnittet validerar enhetstestet beteendet för `E1_SayHello` funktionen Aktivitet:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Och enhetstesterna kommer att verifiera formatet på utdata. Enhetstesterna kan använda parametertyperna `DurableActivityContextBase` direkt eller håna klass:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Läs mer om moq](https://github.com/Moq/moq4/wiki/Quickstart)
