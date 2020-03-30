---
title: Övervakare i varaktiga funktioner – Azure
description: Lär dig hur du implementerar en statusövervakare med tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562130"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Övervaka scenario i varaktiga funktioner - Väder bevakare exempel

Monitormönstret refererar till en flexibel *återkommande* process i ett arbetsflöde - till exempel avsökning tills vissa villkor är uppfyllda. I den här artikeln beskrivs ett exempel som använder [varaktiga funktioner](durable-functions-overview.md) för att implementera övervakning.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Det här exemplet övervakar en platss aktuella väderförhållanden och varnar en användare via SMS när himlen är klar. Du kan använda en vanlig timer-utlöst funktion för att kontrollera vädret och skicka varningar. Ett problem med den här metoden är dock **livstidshantering**. Om endast en avisering ska skickas måste övervakaren inaktivera sig själv när klart väder har upptäckts. Övervakningsmönstret kan avsluta sitt eget utförande, bland andra fördelar:

* Övervakare körs med intervall, inte scheman: en timerutlösare *körs* varje timme; en bildskärm *väntar* en timme mellan åtgärderna. En övervakares åtgärder överlappar inte om inte anges, vilket kan vara viktigt för tidskrävande aktiviteter.
* Bildskärmar kan ha dynamiska intervall: väntetiden kan ändras baserat på vissa villkor.
* Övervakare kan avslutas när något villkor uppfylls eller avslutas av en annan process.
* Monitorer kan ta parametrar. Exemplet visar hur samma väderövervakningsprocess kan tillämpas på en begärd plats och telefonnummer.
* Bildskärmar är skalbara. Eftersom varje övervakare är en orkestreringsinstans kan flera övervakare skapas utan att behöva skapa nya funktioner eller definiera mer kod.
* Övervakare integreras enkelt i större arbetsflöden. En övervakare kan vara en del av en mer komplex orkestreringsfunktion eller en [underorkestrering](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfiguration

### <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurera väder underground integration

Det här exemplet innebär att du använder VÄDER UNDERGROUND API för att kontrollera aktuella väderförhållanden för en plats.

Det första du behöver är ett Weather Underground-konto. Du kan skapa en [https://www.wunderground.com/signup](https://www.wunderground.com/signup)gratis på . När du har ett konto måste du skaffa en API-nyckel. Du kan göra [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)det genom att besöka och sedan välja Tangentinställningar. Stratus Developer-planen är kostnadsfri och tillräcklig för att köra det här exemplet.

När du har en API-nyckel lägger du till följande **appinställning** i funktionsappen.

| Namn på appinställning | Beskrivning av värde |
| - | - |
| **VäderUndergroundApiKey**  | Din Väder Underground API-nyckel. |

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempelappen:

* `E3_Monitor`: En [orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E3_GetIsClear` med jämna mellanrum. Den `E3_SendGoodWeatherAlert` anropar om `E3_GetIsClear` returnerar sant.
* `E3_GetIsClear`: En [aktivitetsfunktion](durable-functions-bindings.md#activity-trigger) som kontrollerar de aktuella väderförhållandena för en plats.
* `E3_SendGoodWeatherAlert`: En aktivitetsfunktion som skickar ett SMS-meddelande via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor orchestrator-funktionen

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Orchestrator kräver en plats för att övervaka och ett telefonnummer för att skicka ett meddelande till när om det blir klart på platsen. Dessa data skickas till orchestrator som `MonitorRequest` ett starkt maskinskrivet objekt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funktionen **E3_Monitor** använder *standardfunktionen.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Här är koden som implementerar funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Den här orchestrator-funktionen utför följande åtgärder:

1. Får **MonitorRequest** består av *platsen* att övervaka och *telefonnumret* som den kommer att skicka ett SMS-meddelande.
2. Bestämmer övervakarens utgångstid. I exemplet används ett hårdkodat värde för korthet.
3. Samtal **E3_GetIsClear** för att avgöra om det finns klar himmel på den begärda platsen.
4. Om vädret är klart **ringer E3_SendGoodWeatherAlert** för att skicka ett SMS-meddelande till det begärda telefonnumret.
5. Skapar en varaktig timer för att återuppta orkestreringen vid nästa avsökningsintervall. I exemplet används ett hårdkodat värde för korthet.
6. Fortsätter att köras tills den aktuella UTC-tiden passerar övervakarens utgångstid eller en SMS-varning skickas.

Flera orchestrator-instanser kan köras samtidigt genom att anropa orchestrator-funktionen flera gånger. Platsen för att övervaka och telefonnumret för att skicka en SMS-varning till kan anges.

### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear aktivitetsfunktion

Precis som med andra exempel är hjälpaktivitetsfunktionerna `activityTrigger` vanliga funktioner som använder utlösarbindningen. Funktionen **E3_GetIsClear** får de aktuella väderförhållandena med hjälp av Weather Underground API och avgör om himlen är klar.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Funktionen.json* definieras på följande sätt:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

Och här är genomförandet.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert aktivitetsfunktion

Funktionen **E3_SendGoodWeatherAlert** använder Twilio-bindningen för att skicka ett SMS-meddelande som meddelar slutanvändaren att det är en bra tid för en promenad.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-paketet för att köra exempelkoden.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Dess *function.json* är enkel:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

Och här är koden som skickar SMS-meddelandet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Kör exemplet

Med hjälp av de HTTP-utlösta funktioner som ingår i exemplet kan du starta orkestreringen genom att skicka följande HTTP POST-begäran:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Den **E3_Monitor** instansen startar och frågar efter aktuella väderförhållanden för den begärda platsen. Om vädret är klart anropas en aktivitetsfunktion för att skicka en avisering. Annars ställer den in en timer. När timern upphör att gälla återupptas orkestreringen.

Du kan se orkestreringaktiviteten genom att titta på funktionsloggarna i Azure Functions-portalen.

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

Orkestreringen [avslutas](durable-functions-instance-management.md) när tidsgränsen har nåtts eller klar himmel har upptäckts. Du kan `TerminateAsync` också använda (.NET) eller `terminate` (JavaScript) i en annan funktion eller anropa den **terminatePostUri** HTTP `{text}` POST webhook som refereras i 202-svaret ovan och ersätta med orsaken till uppsägningen:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du använder varaktiga funktioner för att övervaka en extern källstatus med [varaktiga timers](durable-functions-timers.md) och villkorsstyrd logik. Nästa exempel visar hur du använder externa händelser och [varaktiga timers](durable-functions-timers.md) för att hantera mänsklig interaktion.

> [!div class="nextstepaction"]
> [Kör det mänskliga interaktionsprovet](durable-functions-phone-verification.md)
