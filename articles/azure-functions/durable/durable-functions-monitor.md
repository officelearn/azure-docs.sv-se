---
title: Övervakare i varaktiga funktioner – Azure
description: Lär dig hur du implementerar en statusövervakaren med tillägget varaktiga funktioner för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: 7af8015e424b4a9169a9b80ed5e7070a8fa6de1c
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643322"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Övervaka scenariot i varaktiga funktioner - väder watcher exemplet

Mönstret övervakaren refererar till en flexibel *återkommande* processen i ett arbetsflöde – till exempel avsökning tills vissa villkor uppfylls. Den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) att implementera övervakning.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Det här exemplet övervakar en plats aktuella väderförhållanden och varnar en användare via SMS när skies är tydliga. Du kan använda en vanlig timerutlöst funktion för att kontrollera vädret och skicka aviseringar. Ett problem med den här metoden är dock **livstid kan hanteras**. Om det bara en avisering ska skickas, övervakaren måste inaktivera själva efter clear väder har identifierats. Mönstret övervakning avsluta en egen körning, bland annat:

* Övervakare som körs på intervall, inte schemalägger: en timer som utlösare *körs* varje timme, en Övervakare *väntar* en timme mellan åtgärder. En Övervakare åtgärder kommer inte överlappa varandra om inget anges, vilket kan vara viktigt för långvariga aktiviteter.
* Övervakare kan ha dynamiska intervall: väntetiden kan ändras baserat på vissa villkor.
* Övervakare kan avsluta när vissa villkor är uppfyllt eller avslutas med en annan process.
* Övervakare kan ta parametrar. Exemplet visar hur samma process för övervakning av väder kan tillämpas på alla begärda platsen och telefonnummer.
* Övervakare är skalbara. Eftersom varje Övervakare är en orchestration-instans, kan du skapa flera bildskärmar utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakare integrera enkelt i större arbetsflöden. En Övervakare kan vara en del av en mer komplex orchestration-funktion eller en [underordnade orchestration](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurera väder underjordiska integrering

Det här exemplet omfattar med väder underjordiska API för att kontrollera aktuella väderförhållanden för en plats.

Det första du behöver är ett väder underjordiska konto. Du kan skapa ett kostnadsfritt på [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). När du har ett konto måste du skaffa en API-nyckel. Du kan göra det genom att besöka [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), och sedan väljer nyckel inställningarna. Stratus utvecklaravtal är kostnadsfria och tillräcklig för att köra det här exemplet.

När du har en API-nyckel, lägger du till följande **appinställningen** till din funktionsapp.

| App inställningsnamn | Värdebeskrivning |
| - | - |
| **WeatherUndergroundApiKey**  | Väder underjordiska API-nyckeln. |

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E3_Monitor`: En orchestrator-funktion som anropar `E3_GetIsClear` med jämna mellanrum. Den anropar `E3_SendGoodWeatherAlert` om `E3_GetIsClear` returnerar true.
* `E3_GetIsClear`: En aktivitet-funktion som kontrollerar aktuella vädret för en plats.
* `E3_SendGoodWeatherAlert`: En aktivitet-funktion som skickar ett SMS-meddelande via Twilio.

I följande avsnitt beskrivs konfiguration och kod som används för C#-skript och JavaScript. Kod för Visual Studio-utveckling visas i slutet av artikeln.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Vädret övervakning orchestration (Visual Studio Code och Azure portal exempelkoden)

Den **E3_Monitor** funktionen använder standard *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Här är den kod som implementerar funktionen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Den här orchestrator-funktionen utför följande åtgärder:

1. Hämtar den **MonitorRequest** som består av den *plats* att övervaka och *telefonnummer* till som den ska skicka ett SMS-meddelande.
2. Anger förfallotid för övervakaren. Exemplet använder en hårdkodad värdet av utrymmesskäl.
3. Anrop **E3_GetIsClear** att avgöra om det finns tydligt skies på den begärda platsen.
4. Om vädret är tom anropar **E3_SendGoodWeatherAlert** att skicka ett SMS-meddelande till den begärda telefonnummer.
5. Skapar en hållbar timer för att återuppta orchestration vid nästa avsökningsintervall. Exemplet använder en hårdkodad värdet av utrymmesskäl.
6. Fortsätter att köras tills den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) pass övervakarens upphör att gälla, eller en SMS-avisering skickas.

Flera orchestrator-instanser kan köras samtidigt genom att skicka flera **MonitorRequests**. Plats för att övervaka och telefonnumret för att skicka ett SMS-avisering till kan anges.

## <a name="strongly-typed-data-transfer-net-only"></a>Starkt typifierade dataöverföring (.NET)

Orchestrator kräver flera typer av data, så [delade POCO objekt](../functions-reference-csharp.md#reusing-csx-code) används för starkt typifierade dataöverföring i C# och C#-skript: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

JavaScript-exemplet används reguljära JSON-objekt som parametrar.

## <a name="helper-activity-functions"></a>Hjälpfunktioner för aktivitet

Som med andra exempel hjälpfunktioner för aktiviteten är vanliga funktioner som använder den `activityTrigger` utlösa bindning. Den **E3_GetIsClear** funktionen hämtar de aktuella väderförhållanden med hjälp av underjordiska väder-API och avgör om finns inga gränser Rensa. Den *function.json* definieras så här:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Och här är implementeringen. Precis som POCOs som används för dataöverföring, logik för att hantera API: et anropa och parsa svaret JSON har abstraherats i en delad klassen i C#. Du kan hitta den som en del av den [Visual Studio-exempelkoden](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

Den **E3_SendGoodWeatherAlert** funktion använder Twilio-bindning för att skicka ett SMS-meddelande som meddelar användaren om att det är dags för en genomgång. Dess *function.json* är enkel:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Och här är den kod som skickar SMS: et:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Kör exemplet

Med de HTTP-utlösta funktioner som ingår i det här exemplet kan starta du dirigering genom att skicka följande HTTP POST-begäran:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

   > [!NOTE]
   > För närvarande kan inte JavaScript orchestration starter-funktioner returnera instanshantering URI: er. Den här funktionen kommer att läggas till i en senare version.

Den **E3_Monitor** instans startas och frågar de aktuella väderförhållandena för den begärda platsen. Om vädret är tom anropar en funktion för aktiviteten för att skicka en avisering; i annat fall anger en timer. När timern upphör att gälla återupptas för samordning.

Du kan se för samordning genom att titta på funktionen aktivitetsloggar i Azure Functions-portalen.

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

Dirigering kommer [avsluta](durable-functions-instance-management.md#terminating-instances) när dess tidsgräns har nått eller rensa skies identifieras. Du kan också använda `TerminateAsync` inuti ett annat fungera eller anropa den **terminatePostUri** HTTP POST-webhook som refereras i 202-svaret ovan ersätter `{text}` med orsaken uppsägning:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har du lärt dig för att övervaka status för en extern källa med hjälp av varaktiga funktioner med hjälp av [varaktiga timers](durable-functions-timers.md) och villkorslogik. I nästa exempel visas hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Kör exemplet mänsklig interaktion](durable-functions-phone-verification.md)
