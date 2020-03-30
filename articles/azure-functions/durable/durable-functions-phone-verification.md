---
title: Mänsklig interaktion och timeout i varaktiga funktioner - Azure
description: Lär dig hur du hanterar mänsklig interaktion och timeout i tillägget Varaktiga funktioner för Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335745"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Mänsklig interaktion i varaktiga funktioner - Telefonverifieringsprov

Det här exemplet visar hur du skapar en varaktig funktionsorkestrering som involverar mänsklig interaktion. [Durable Functions](durable-functions-overview.md) När en verklig person är involverad i en automatiserad process måste processen kunna skicka meddelanden till personen och få svar asynkront. Det måste också göra det möjligt att personen inte är tillgänglig. (Den sista delen är där timeout blir viktiga.)

Det här exemplet implementerar ett SMS-baserat telefonverifieringssystem. Dessa typer av flöden används ofta när du verifierar en kunds telefonnummer eller för multifaktorautentisering (MFA). Det är ett kraftfullt exempel eftersom hela genomförandet görs med hjälp av ett par små funktioner. Inget externt datalager, till exempel en databas, krävs.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Telefonverifiering används för att verifiera att slutanvändare av ditt program inte är spammare och att de är de som de säger att de är. Multifaktorautentisering är ett vanligt användningsfall för att skydda användarkonton från hackare. Utmaningen med att implementera din egen telefon verifiering är att det kräver en **tillståndskänslig interaktion** med en människa. En slutanvändare får vanligtvis viss kod (till exempel ett fyrsiffrigt nummer) och måste svara **inom rimlig tid.**

Vanliga Azure-funktioner är tillståndslösa (liksom många andra molnslutpunkter på andra plattformar), så dessa typer av interaktioner innebär att explicit hantera tillstånd externt i en databas eller någon annan beständig butik. Dessutom måste interaktionen delas upp i flera funktioner som kan samordnas tillsammans. Du behöver till exempel minst en funktion för att bestämma en kod, behålla den någonstans och skicka den till användarens telefon. Dessutom behöver du minst en annan funktion för att få ett svar från användaren och på något sätt mappa tillbaka den till det ursprungliga funktionsanropet för att göra kodvalideringen. En timeout är också en viktig aspekt för att garantera säkerheten. Det kan bli ganska komplicerat snabbt.

Komplexiteten i det här scenariot minskar avsevärt när du använder varaktiga funktioner. Som du kommer att se i det här exemplet kan en orchestrator-funktion hantera den tillståndskänsliga interaktionen enkelt och utan att involvera några externa datalager. Eftersom orchestrator funktioner är *hållbara,* dessa interaktiva flöden är också mycket tillförlitliga.

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funktionerna

I den här artikeln går du igenom följande funktioner i exempelappen:

* `E4_SmsPhoneVerification`: En [orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som utför telefonverifieringsprocessen, inklusive hantering av timeout och återförsök.
* `E4_SendSmsChallenge`: En [aktivitetsfunktion](durable-functions-bindings.md#activity-trigger) som skickar en kod via sms.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification orchestrator-funktionen

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Det kanske inte är uppenbart i början, men denna orchestrator funktion är helt deterministisk. Det är deterministiskt eftersom egenskapen `CurrentUtcDateTime` används för att beräkna timerns förfallotid och returnerar samma värde på varje repris vid denna punkt i orchestrator-koden. Det här beteendet är `winner` viktigt för att `Task.WhenAny`säkerställa att samma resultat från varje upprepad anrop till .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funktionen **E4_SmsPhoneVerification** använder *standardfunktionen.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Här är koden som implementerar funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Det kanske inte är uppenbart i början, men denna orchestrator funktion är helt deterministisk. Det är deterministiskt eftersom egenskapen `currentUtcDateTime` används för att beräkna timerns förfallotid och returnerar samma värde på varje repris vid denna punkt i orchestrator-koden. Det här beteendet är `winner` viktigt för att `context.df.Task.any`säkerställa att samma resultat från varje upprepad anrop till .

---

När den här orchestrator-funktionen har startats:

1. Får ett telefonnummer som det kommer att *skicka* SMS-aviseringen.
2. Samtal **E4_SendSmsChallenge** för att skicka ett SMS till användaren och returnerar den förväntade fyrsiffriga utmaningskoden.
3. Skapar en hållbar timer som utlöser 90 sekunder från den aktuella tiden.
4. Parallellt med timern väntar en **SmsChallengeResponse-händelse** från användaren.

Användaren får ett SMS-meddelande med en fyrsiffrig kod. De har 90 sekunder på sig att skicka tillbaka samma fyrsiffriga kod till orchestrator-funktionsinstansen för att slutföra verifieringsprocessen. Om de skickar in fel kod, får de ytterligare tre försök att få det rätt (inom samma 90-sekunders fönster).

> [!WARNING]
> Det är viktigt att [avbryta timers](durable-functions-timers.md) om du inte längre behöver dem för att upphöra att gälla, som i exemplet ovan när ett utmaningssvar accepteras.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge aktivitetsfunktion

Funktionen **E4_SendSmsChallenge** använder Twilio-bindningen för att skicka SMS-meddelandet med den fyrsiffriga koden till slutanvändaren.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-paketet för att köra exempelkoden.

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Funktionen.json* definieras på följande sätt:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

Och här är koden som genererar den fyrsiffriga utmaningskoden och skickar SMS-meddelandet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Kör exemplet

Med hjälp av de HTTP-utlösta funktioner som ingår i exemplet kan du starta orkestreringen genom att skicka följande HTTP POST-begäran:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Orchestrator-funktionen tar emot det angivna telefonnumret och skickar omedelbart ett SMS-meddelande &mdash; med en slumpmässigt genererad 4-siffrig verifieringskod till exempel *2168*. Funktionen väntar sedan 90 sekunder på ett svar.

Om du vill svara med koden kan `{eventName}` du använda `SmsChallengeResponse` [ `RaiseEventAsync` (.NET) eller `raiseEvent` (JavaScript)](durable-functions-instance-management.md) i en annan funktion eller anropa **sendEventUrl** HTTP POST webhook som refereras i 202-svaret ovan och ersätta med namnet på händelsen:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Om du skickar detta innan timern går ut slutförs orkestreringen och `output` fältet är inställt `true`på , vilket indikerar en lyckad verifiering.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Om du låter timern gå ut, eller om du anger fel kod fyra `false` gånger, kan du fråga efter status och se en orchestration-funktionsutdata som anger att telefonverifieringen misslyckades.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat några av de avancerade `WaitForExternalEvent` `CreateTimer` funktionerna i varaktiga funktioner, särskilt och API:er. Du har sett hur dessa `Task.WaitAny` kan kombineras med för att implementera ett tillförlitligt timeout-system, vilket ofta är användbart för att interagera med riktiga människor. Du kan läsa mer om hur du använder varaktiga funktioner genom att läsa en serie artiklar som erbjuder djupgående täckning av specifika ämnen.

> [!div class="nextstepaction"]
> [Gå till den första artikeln i serien](durable-functions-bindings.md)
