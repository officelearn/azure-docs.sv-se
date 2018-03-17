---
title: "Övervakare i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar en statusövervakaren med beständiga funktioner för Azure Functions."
services: functions
author: kashimiz
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2018
ms.author: azfuncdf
ms.openlocfilehash: 617b33a9f860ce3b06ff560de22824037eab8332
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Övervaka scenariot i varaktiga funktioner - väder watcher exempel

Övervakaren mönstret refererar till en flexibel *återkommande* process i ett arbetsflöde – till exempel avsökning tills vissa villkor är uppfyllda. Den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) att implementera övervakning.

## <a name="prerequisites"></a>Förutsättningar

* Följ instruktionerna i [installera varaktiga funktioner](durable-functions-install.md) att ställa in provet.
* Den här artikeln förutsätter att du redan har gått igenom de [Hello sekvens](durable-functions-sequence.md) exempel genomgången.

## <a name="scenario-overview"></a>Scenarioöversikt

Det här exemplet övervakar aktuella väder för en plats och en användare av SMS-varningar när skies är tydliga. Du kan använda en vanlig funktion som utlöses av timer för att kontrollera väder och skicka aviseringar. Ett problem med den här metoden är dock **livstidshantering**. Om bara en avisering ska skickas, övervakaren måste inaktivera sig själv efter Rensa väder har identifierats. Övervakning mönstret avsluta en egen körning bland andra fördelar:

* Övervakare som körs på intervall, inte schemalägger: en timer som utlösare *kör* varje timme; en Övervakare *väntar* en timme mellan åtgärder. Åtgärder för en Övervakare kan inte överlappa om inget anges, som kan vara viktigt för tidskrävande uppgifter.
* Övervakare kan ha dynamiskt intervall: väntetiden kan ändras baserat på vissa villkor.
* Övervakare kan säga upp när vissa villkor är uppfyllt eller avslutas med en annan process.
* Övervakare kan ta parametrar. Exemplet visar hur samma process för övervakning av väder kan tillämpas på alla begärda platsen och telefonnummer.
* Övervakare kan skalas. Eftersom varje Övervakare är en orchestration-instans, kan du skapa flera bildskärmar utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakare integrera enkelt större arbetsflöden. En Övervakare kan vara en del av en mer komplex orchestration-funktion eller en [underordnade orchestration](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurera lagringsutrymmen väder integrering

Det här exemplet omfattar med väder lagringsutrymmen API för att kontrollera aktuella väder för en plats.

Det första du behöver är en väder lagringsutrymmen konto. Du kan skapa ett kostnadsfritt på [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). När du har ett konto kan behöver du skaffa en API-nyckel. Du kan göra det genom att besöka [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), därefter välja inställningar för nyckeln. Stratus Developer-planen är ledigt och tillräcklig för att köra det här exemplet.

När du har en API-nyckel, lägger du till följande **appinställningen** i appen funktion.

| Appen inställningsnamn | Värdebeskrivning |
| - | - |
| **WeatherUndergroundApiKey**  | Din väder lagringsutrymmen API-nyckel. |

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* `E3_Monitor`: En orchestrator-funktion som anropar `E3_GetIsClear` med jämna mellanrum. Anropar `E3_SendGoodWeatherAlert` om `E3_GetIsClear` returnerar true.
* `E3_GetIsClear`: En aktivitet funktion som kontrollerar de aktuella väder villkor för en plats.
* `E3_SendGoodWeatherAlert`: En aktivitet-funktion som skickar ett SMS-meddelande via Twilio.

I följande avsnitt beskrivs konfiguration och kod som används för utveckling av Azure portal. Kod för Visual Studio-utveckling visas i slutet av artikeln.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Väder övervakning orchestration (Visual Studio Code och Azure portal exempelkod)

Den **E3_Monitor** funktion använder vanlig *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Här är den kod som implementerar funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Den här funktionen för orchestrator utförs följande åtgärder:

1. Hämtar den **MonitorRequest** som består av den *plats* att övervaka och *telefonnummer* som den ska skicka ett SMS-meddelande.
2. Anger förfallotid för övervakaren. Ett hårdkodat värde används för planeringsaspekter.
3. Anrop **E3_GetIsClear** att avgöra om det finns en tydlig skies på den begärda platsen.
4. Om väder är avmarkerad, anropar **E3_SendGoodWeatherAlert** att skicka ett SMS-meddelande till den begärda telefonnummer.
5. Skapar en beständig timer för att återuppta orchestration vid nästa avsökningsintervall. Ett hårdkodat värde används för planeringsaspekter.
6. Fortsätter att köras tills den [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) överför övervakarens förfallotid eller en SMS-avisering skickas.

Flera instanser av orchestrator kan köras samtidigt genom att skicka flera **MonitorRequests**. Plats för att övervaka och telefonnummer för att skicka en SMS-avisering till kan anges.

## <a name="strongly-typed-data-transfer"></a>Strikt typkontroll dataöverföring

Orchestrator kräver flera uppgifter av data, så [delade POCO objekt](functions-reference-csharp.md#reusing-csx-code) används för att överföra data med strikt typkontroll: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Hjälpfunktioner för aktiviteten

Som med andra exempel hjälpfunktioner för aktiviteten är vanliga funktioner som använder den `activityTrigger` utlösa bindning. Den **E3_GetIsClear** funktionen hämtar de aktuella väder villkor med hjälp av lagringsutrymmen väder-API och avgör om sky är klar. Den *function.json* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Här är implementeringen. Precis som de POCOs som används för att överföra data logik för att hantera API: et anropa och tolka svaret JSON representeras i en delad klass. Du hittar den som en del av den [exempelkod för Visual Studio](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

Den **E3_SendGoodWeatherAlert** funktionen använder Twilio-bindning för att skicka ett SMS-meddelande som anger användaren att det är dags för en Gå. Dess *function.json* är enkel:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Och här är den kod som skickar SMS-meddelande:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Kör exemplet

Du använder HTTP-utlösta funktioner som ingår i exemplet, kan du starta orchestration genom att skicka följande HTTP POST-begäran:

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

Den **E3_Monitor** instans startar och frågar aktuella väder villkoren för den begärda platsen. Om väder är tydligt anropar en funktion för aktiviteten att skicka en avisering; Annars anges en timer. Orchestration återupptas när timern upphör att gälla.

Du kan se den orchestration aktiviteten genom att titta på funktionen loggar i Azure Functions-portalen.

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

Orchestration kommer [avsluta](durable-functions-instance-management.md#terminating-instances) när dess tidsgräns har nått eller avmarkera skies identifieras. Du kan också använda `TerminateAsync` inuti ett annat fungera eller anropa den **terminatePostUri** HTTP POST webhook som refereras i 202 svaret ovan ersätter `{text}` med orsaken uppsägning:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-filen i Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har du lärt dig för att övervaka status för en extern källa med hjälp av beständiga funktioner med hjälp av [varaktiga timers](durable-functions-timers.md) och villkorlig logik. I nästa exempel visas hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Köra exemplet mänsklig interaktion](durable-functions-phone-verification.md)
