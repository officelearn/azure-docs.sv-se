---
title: Mänsklig interaktion och timeout i varaktiga funktioner – Azure
description: Lär dig hur du hanterar mänsklig interaktion och timeout i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: 7bc9341d7e078b0ae69cc9a734c02f257df6d96a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643357"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Mänsklig interaktion i varaktiga funktioner - Phone verifiering exemplet

Detta exempel visar hur du skapar en [varaktiga funktioner](durable-functions-overview.md) orchestration som inbegriper mänsklig interaktion. När en verklig person finns som ingår i en automatiserad process, måste processen kunna skicka meddelanden till personen och ta emot svar asynkront. Det måste även tillåta möjlighet att personen är inte tillgänglig. (Det här sista delen är där tidsgränser blir viktiga.)

Det här exemplet implementerar en SMS-baserade phone verifieringssystem. Dessa typer av flöden används ofta vid verifiering av en kunds telefonnummer eller för multifaktorautentisering (MFA). Det här är en kraftfull exempel eftersom implementeringen av hela görs med hjälp av ett par små funktioner. Det krävs inga externa datalager, till exempel en databas.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Telefonverifiering används för att kontrollera att slutanvändare för ditt program inte är skräppostavsändare och att de är de säger att de är. Multi-Factor authentication är ett vanligt användningsfall för att skydda användarkonton från hackare. Den stora utmaningen med att implementera dina egna telefonverifiering är att det krävs en **tillståndskänsliga interaktion** med en vanlig människa. Slutanvändarna anges vanligtvis kod (till exempel ett 4-siffrig-tal) och måste svara **inom en rimlig tid**.

Vanliga Azure Functions är tillståndslösa (som är många andra molnslutpunkter på andra plattformar), så att dessa typer av interaktioner omfattar uttryckligen hantera i en databas eller några andra beständiga tillståndslager externt. Interaktionen måste dessutom delas upp i flera funktioner som kan vara samordnad tillsammans. Till exempel behöver du minst en funktion för att bestämma om en, spara den någonstans och skickas till användarens telefon. Dessutom behöver du minst en annan funktion att får ett svar från användaren och mappa den på något sätt tillbaka till det ursprungliga funktionsanropet för att kunna utföra kod-verifieringen. En tidsgräns som är också en viktig aspekt att garantera säkerheten. Detta kan få ganska komplex snabbt.

Komplexiteten i det här scenariot är minskar när du använder varaktiga funktioner. Som visas i det här exemplet, kan en orchestrator-funktion hantera den tillståndskänsliga interaktionen enkelt och utan hjälp från alla externa datalager. Eftersom orchestrator-funktioner är *varaktiga*, flödena interaktiva är också mycket pålitlig.

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funktionerna

Den här artikeln beskriver följande funktioner i exempelappen:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

I följande avsnitt beskrivs konfiguration och kod som används för C#-skript och JavaScript. Kod för Visual Studio-utveckling visas i slutet av artikeln.
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS verifiering orchestration (Visual Studio Code och Azure portal exempelkoden) 

Den **E4_SmsPhoneVerification** funktionen använder standard *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Här är den kod som implementerar funktionen:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

När startats gör orchestrator-funktion följande:

1. Hämtar ett telefonnummer som den ska *skicka* SMS-meddelande.
2. Anrop **E4_SendSmsChallenge** för att skicka ett SMS-meddelande till användaren och returnerar tillbaka den förväntade 4-siffrig kontrollkoden.
3. Skapar en hållbar timer som utlösare 90 sekunder från den aktuella tiden.
4. Parallellt med timern ska vänta tills en **SmsChallengeResponse** händelse från användaren.

Användaren får ett SMS-meddelande med en fyrsiffrig kod. De har 90 sekunder att skicka den samma 4-siffrig kod till funktionen-instans orchestrator för att slutföra verifieringen. Om de skickar fel kod får de en ytterligare tre försök att hämta det direkt (inom samma 90 sekunder fönster).

> [!NOTE]
> Det kanske inte är uppenbara på första, men den här orchestrator-funktionen är helt deterministisk. Detta beror på den `CurrentUtcDateTime` egenskapen används för att beräkna förfallotid timer och den här egenskapen returnerar samma värde på varje repetitionsattacker nu i orchestrator-koden. Detta är viktigt att se till att samma `winner` resultat av varje upprepade anrop till `Task.WhenAny`.

> [!WARNING]
> Det är viktigt att [Avbryt timers](durable-functions-timers.md) om du inte längre behöver dem upphöra, som i exemplet ovan då svar tas emot.

## <a name="send-the-sms-message"></a>Skicka SMS: et

Den **E4_SendSmsChallenge** Twilio-bindningen använder funktionen för att skicka SMS: et med 4-siffrig kod till slutanvändaren. Den *function.json* definieras så här:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Och här är koden som genererar 4-siffrig kontrollkoden och skickar SMS: et:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (endast funktioner v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Detta **E4_SendSmsChallenge** funktionen bara anropas en gång, även om processen kraschar eller hämtar återupprepas. Detta är bra eftersom du inte vill att användaren hämtar flera SMS-meddelanden. Den `challengeCode` returnera värdet sparas automatiskt, så orchestrator-funktion alltid vet vad som är rätt kod.

## <a name="run-the-sample"></a>Kör exemplet

Med de HTTP-utlösta funktioner som ingår i det här exemplet kan starta du dirigering genom att skicka följande HTTP POST-begäran:

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
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

   > [!NOTE]
   > För närvarande kan inte JavaScript orchestration starter-funktioner returnera instanshantering URI: er. Den här funktionen kommer att läggas till i en senare version.

Orchestrator-funktion som tar emot det angivna telefonnumret och omedelbart skickar ett SMS-meddelande med en slumpmässigt genererad 4-siffriga Verifieringskod &mdash; exempelvis *2168*. Funktionen väntar sedan i 90 sekunder för ett svar.

Om du vill svara med kod kan du använda `RaiseEventAsync` inuti ett annat fungera eller anropa den **sendEventUrl** HTTP POST-webhook som refereras i 202-svaret ovan ersätter `{eventName}` med namnet på händelsen, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Om du skickar detta innan timern upphör att gälla, dirigering är klar och `output` anges till `true`, som anger en lyckad verifiering.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Om du låter timern upphör att gälla, eller om du anger fel kod fyra gånger, kan du fråga efter status och se en `false` orchestration funktionen utdata som indikerar att telefonverifiering misslyckades.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-fil i ett Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat några av de avancerade funktionerna i varaktiga funktioner, särskilt `WaitForExternalEvent` och `CreateTimer`. Du har sett hur dessa kan kombineras med `Task.WaitAny` att implementera ett tillförlitlig timeout-system, vilket ofta är användbart för att interagera med riktiga människor. Du kan lära dig mer om hur du använder varaktiga funktioner genom att läsa en serie artiklar som erbjuder djupgående täckning av specifika ämnen.

> [!div class="nextstepaction"]
> [Gå till den första artikeln i serien](durable-functions-bindings.md)
