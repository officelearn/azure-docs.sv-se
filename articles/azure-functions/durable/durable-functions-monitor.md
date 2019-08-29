---
title: Övervakare i Durable Functions – Azure
description: Lär dig hur du implementerar en status övervakare med Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ae6c2bd27e9192966ecffb4d4296063201fca970
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098021"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Övervaka scenario i Durable Functions – väder vakts exempel

Övervaknings mönstret avser en flexibel *återkommande* process i ett arbets flöde – till exempel avsökningen tills vissa villkor är uppfyllda. I den här artikeln beskrivs ett exempel som använder [Durable Functions](durable-functions-overview.md) för att implementera övervakning.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Det här exemplet övervakar en platss aktuella väder förhållanden och varnar en användare av SMS när Skies är klar. Du kan använda en vanlig timer-utlöst funktion för att kontrol lera väder och skicka aviseringar. Ett problem med den här metoden är dock **livs längds hantering**. Om endast en avisering ska skickas måste övervakaren stänga av sig själv när tydliga väder har upptäckts. Övervaknings mönstret kan avsluta sin egen körning, bland andra fördelar:

* Övervakare körs i intervall, inte scheman: en timer-utlösare *körs* varje timme. en övervakare *väntar* en timme mellan åtgärder. En övervaknings åtgärd överlappar inte om inget anges, vilket kan vara viktigt för långvariga uppgifter.
* Övervakare kan ha dynamiska intervall: vänte tiden kan ändras baserat på ett villkor.
* Övervakare kan avslutas när ett villkor är uppfyllt eller avslutas av en annan process.
* Övervakare kan ta parametrar. Exemplet visar hur samma väder övervaknings process kan tillämpas på alla begärda platser och telefonnummer.
* Övervakare är skalbara. Eftersom varje övervakare är en Dirigerings instans kan flera övervakare skapas utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakar lätt att integrera i större arbets flöden. En övervakare kan vara en del av en mer komplex Orchestration-funktion eller en [under dirigering](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurera väder underjords integrering

Det här exemplet förutsätter att du använder väder-API: et för att kontrol lera aktuella väder förhållanden för en plats.

Det första du behöver är ett väderlek underjords konto. Du kan skapa ett kostnads fritt på [https://www.wunderground.com/signup](https://www.wunderground.com/signup). När du har ett konto måste du skaffa en API-nyckel. Du kan göra det genom att [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)besöka och sedan välja nyckel inställningar. Stratus Developer plan är gratis och tillräckligt för att köra det här exemplet.

När du har en API-nyckel lägger du till följande **app-inställning** i din Function-app.

| Namn på App-inställning | Värde Beskrivning |
| - | - |
| **WeatherUndergroundApiKey**  | Din API-nyckel för väder under jord. |

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E3_Monitor`: En Orchestrator-funktion som `E3_GetIsClear` anropar regelbundet. Den `E3_SendGoodWeatherAlert` anropas `E3_GetIsClear` om returnerar true.
* `E3_GetIsClear`: En aktivitets funktion som kontrollerar aktuella väder förhållanden för en plats.
* `E3_SendGoodWeatherAlert`: En aktivitets funktion som skickar ett SMS-meddelande via Twilio.

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript och Java Script. Koden för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Väder övervaknings dirigering (Visual Studio Code och Azure Portal exempel kod)

Funktionen **E3_Monitor** använder standard *funktionen. JSON* för Orchestrator functions.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Här är den kod som implementerar funktionen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Den här Orchestrator-funktionen utför följande åtgärder:

1. Hämtar den **MonitorRequest** som består av *platsen* som ska övervakas och telefonnumret som den ska skicka ett SMS-meddelande till.
2. Anger övervakningens förfallo tid. Exemplet använder ett hårdkodat värde för det kortfattat.
3. Anropar **E3_GetIsClear** för att avgöra om det finns en rensning av Skies på den begärda platsen.
4. Om väderet är klart anropar **E3_SendGoodWeatherAlert** för att skicka ett SMS-meddelande till det begärda telefonnumret.
5. Skapar en varaktig timer för att återuppta dirigeringen vid nästa avsöknings intervall. Exemplet använder ett hårdkodat värde för det kortfattat.
6. Fortsätter att köras tills [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (C#) eller `currentUtcDateTime` (Java Script) kan passera förfallo tiden för övervakaren eller också skickas en SMS-avisering.

Flera Orchestrator-instanser kan köras samtidigt genom att skicka flera **MonitorRequests**. Den plats som ska övervakas och telefonnumret som en SMS-avisering ska skickas till kan anges.

## <a name="strongly-typed-data-transfer-net-only"></a>Starkt inskriven data överföring (endast .NET)

Orchestrator kräver flera data mängder, så [delade Poco-objekt](../functions-reference-csharp.md#reusing-csx-code) används för starkt inskriven data överföring i C# och C# skript:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

JavaScript-exemplet använder vanliga JSON-objekt som parametrar.

## <a name="helper-activity-functions"></a>Hjälp aktivitetens funktioner

Som med andra exempel är hjälp aktivitetens funktioner vanliga funktioner som använder sig av `activityTrigger` trigger-bindningen. Funktionen **E3_GetIsClear** hämtar aktuella väder förhållanden med hjälp av väder-API: et och avgör om himmelen är klar. *Funktionen. JSON* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Här är implementeringen. Precis som den POCOs som används för data överföring, är logik för att hantera API-anropet och parsa API-anropet som C#är abstrakt till en delad klass i. Du kan hitta det som en del av [Visual Studio-exempel koden](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

Funktionen **E3_SendGoodWeatherAlert** använder Twilio-bindningen för att skicka ett SMS-meddelande som meddelar slutanvändaren att det är en lämplig tid för en guidad användning. Dess *Function. JSON* är enkel:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Här är koden som skickar SMS-meddelandet:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Kör exemplet

Genom att använda HTTP-utlösta funktioner som ingår i exemplet kan du starta dirigeringen genom att skicka följande HTTP POST-begäran:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** -instansen startar och frågar efter aktuella väder förhållanden för den begärda platsen. Om väderet är klart anropas en aktivitets funktion för att skicka en avisering. annars anges en timer. När timern upphör att gälla återupptas dirigeringen.

Du kan se dirigeringens aktivitet genom att titta på funktions loggarna i Azure Functions portalen.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Dirigeringen [avslutas](durable-functions-instance-management.md) när tids gränsen nås eller Clear-Skies har identifierats. Du kan också använda `TerminateAsync` (.net) eller `terminate` (Java Script) i en annan funktion eller anropa **terminatePostUri** http post webhook som refereras i 202-svaret ovan `{text}` , vilket ersätter med orsaken till uppsägningen:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet-paketet så att du kan köra exempel koden nedan.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du använder Durable Functions för att övervaka en extern källas [](durable-functions-timers.md) status med hjälp av varaktiga timers och villkorlig logik. Nästa exempel visar hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) för att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Kör exempel på mänsklig interaktion](durable-functions-phone-verification.md)
