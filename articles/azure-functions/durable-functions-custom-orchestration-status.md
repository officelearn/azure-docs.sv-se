---
title: Anpassad orkestreringsstatus i varaktiga funktioner – Azure
description: Lär dig hur du konfigurerar och använder anpassad orkestreringsstatus för varaktiga funktioner.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: b8017288adb75c990113b0f2ff5ba29a1f1e0a18
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986686"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Anpassad orkestreringsstatus i varaktiga funktioner (Azure Functions)

Anpassad orkestreringsstatus kan du ange ett värde för anpassad status för orchestrator-funktion. Den här statusen tillhandahålls via HTTP GetStatus-API eller `DurableOrchestrationClient.GetStatusAsync` API.

> [!NOTE]
> Anpassade orchestration-status för JavaScript blir tillgänglig i en kommande version.

## <a name="sample-use-cases"></a>Exemplet användningsfall 

### <a name="visualize-progress"></a>Visualisera pågår

Klienter kan söka slutpunkten status och visa förloppet användargränssnitt som hjälper dig att visualiserar det aktuella steget för körning. I följande exempel visar förloppet delning:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

Och sedan tar klienten emot utdata från orchestration endast när `CustomStatus` anges till ”London”:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

### <a name="output-customization"></a>Utdata-anpassning 

En annan intressant scenario är att segmentera användare genom att returnera anpassade utdata baserat på unika egenskaper eller interaktioner. Med hjälp av anpassad orkestreringsstatus förblir klientkod Allmänt. Alla huvudsakliga ändringar sker på serversidan som visas i följande exempel:

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
} 
```

### <a name="instruction-specification"></a>Instruktionen-specifikation 

Orchestrator kan ge unika instruktioner till klienter via anpassade tillstånd. Anpassade status anvisningarna ska mappas till stegen i orchestration-kod:

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

## <a name="sample"></a>Exempel

I följande exempel och anges anpassade status först.

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

När orchestration körs, kan externa klienter hämta den här anpassade status:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienter kommer att få följande svar: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  Anpassade nyttolasten är begränsad till 16 KB av UTF-16 JSON-texten eftersom den måste kunna få plats i en kolumn för Azure Table Storage. Utvecklare kan använda extern lagring om det behövs större nyttolast.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om HTTP-API: er i varaktiga funktioner](durable-functions-http-api.md)


