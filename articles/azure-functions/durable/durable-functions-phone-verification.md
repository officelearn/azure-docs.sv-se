---
title: Mänsklig interaktion och tids gränser i Durable Functions – Azure
description: Lär dig hur du hanterar mänsklig interaktion och tids gränser i Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0c1c92dde2d698fb2c92fb3680ab05393a25573d
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614743"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Mänsklig interaktion i Durable Functions-telefon verifierings exempel

Det här exemplet visar hur du skapar ett [Durable Functions](durable-functions-overview.md) Orchestration som involverar mänsklig interaktion. När en verklig person är inblandad i en automatiserad process måste processen kunna skicka meddelanden till personen och ta emot svar asynkront. Det måste också tillåtas att personen inte är tillgänglig. (Den sista delen är de tids gränser som blir viktiga.)

Det här exemplet implementerar ett SMS-baserat telefon verifierings system. Dessa typer av flöden används ofta när du verifierar en kunds telefonnummer eller Multi-Factor Authentication (MFA). Det är ett kraftfullt exempel eftersom hela implementeringen görs med hjälp av ett par små funktioner. Det krävs inget externt data lager, till exempel en databas.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

Telefon verifiering används för att kontrol lera att slutanvändare av ditt program inte är avsändare och att de är de som de säger att de är. Multi-Factor Authentication är ett vanligt användnings fall för att skydda användar konton från hackare. Utmaningen med att implementera din egen telefon verifiering är att det krävs en **tillstånds känslig interaktion** med mänsklig. En slutanvändare tillhandahåller vanligt vis kod (till exempel ett fyrsiffrigt tal) och måste svara **på en rimlig tids period**.

Vanliga Azure Functions är tillstånds lösa (som är många andra moln slut punkter på andra plattformar), så dessa typer av interaktioner innebär uttryckligen hantering av tillstånd externt i en databas eller något annat beständigt arkiv. Dessutom måste interaktionen delas upp i flera funktioner som kan samordnas tillsammans. Du behöver till exempel minst en funktion för att bestämma en kod, spara den någonstans och skicka den till användarens telefon. Dessutom behöver du minst en annan funktion för att få svar från användaren och på annat sätt mappa den tillbaka till det ursprungliga funktions anropet för att göra kod verifieringen. En timeout är också en viktig aspekt för att garantera säkerheten. Det kan snabbt bli ganska komplicerat.

Komplexiteten i det här scenariot minskar avsevärt när du använder Durable Functions. Som du ser i det här exemplet kan en Orchestrator-funktion hantera tillstånds känsliga interaktioner enkelt och utan att involvera externa data lager. Eftersom Orchestrator-funktioner är *varaktiga*är dessa interaktiva flöden också mycket pålitliga.

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funktionerna

Den här artikeln vägleder dig genom följande funktioner i exempel appen:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

I följande avsnitt beskrivs den konfiguration och kod som används för C# skript och Java Script. Koden för Visual Studio-utveckling visas i slutet av artikeln.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS-verifierings dirigering (Visual Studio Code och Azure Portal exempel kod)

Funktionen **E4_SmsPhoneVerification** använder standard *funktionen. JSON* för Orchestrator functions.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Här är den kod som implementerar funktionen:

### <a name="c-script"></a>C#Över

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

När den här Orchestrator-funktionen har startats gör du följande:

1. Hämtar ett telefonnummer som det ska *Skicka* SMS-aviseringen till.
2. Anropar **E4_SendSmsChallenge** för att skicka ett SMS-meddelande till användaren och återgår till den förväntade 4-siffriga utmanings koden.
3. Skapar en varaktig timer som utlöser 90 sekunder från den aktuella tiden.
4. Parallellt med timern väntar på en **SmsChallengeResponse** -händelse från användaren.

Användaren får ett SMS-meddelande med en fyrsiffrig kod. De har 90 sekunder på sig att skicka samma 4-siffriga kod tillbaka till Orchestrator-funktions instansen för att slutföra verifierings processen. Om de skickar fel kod får de ytterligare tre försök att hämta den åt höger (inom samma 90-sekund-fönster).

> [!NOTE]
> Det kanske inte är uppenbart först, men den här Orchestrator-funktionen är helt deterministisk. Det är entydigt eftersom egenskaperna `CurrentUtcDateTime` (.NET) och `currentUtcDateTime` (Java Script) används för att beräkna timerns förfallo tid och dessa egenskaper returnerar samma värde vid varje uppspelning vid den här punkten i Orchestrator-koden. Detta är viktigt för att säkerställa att samma `winner` resultat från varje upprepat anrop till `Task.WhenAny` (.NET) eller `context.df.Task.any` (Java Script).

> [!WARNING]
> Det är viktigt att [avbryta timers](durable-functions-timers.md) om du inte längre behöver dem att förfalla, precis som i exemplet ovan när ett utmanings svar godkänns.

## <a name="send-the-sms-message"></a>Skicka SMS-meddelandet

Funktionen **E4_SendSmsChallenge** använder Twilio-bindningen för att skicka SMS-meddelandet med den 4-siffriga koden till slutanvändaren. *Funktionen. JSON* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Här är koden som genererar den 4-siffriga utmanings koden och skickar SMS-meddelandet:

### <a name="c-script"></a>C#Över

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Den här **E4_SendSmsChallenge** -funktionen anropas bara en gång, även om processen kraschar eller om den spelas upp. Detta är bra eftersom du inte vill att slutanvändaren ska hämta flera SMS-meddelanden. Det `challengeCode` returvärdet sparas automatiskt, så att Orchestrator-funktionen alltid vet vad rätt kod är.

## <a name="run-the-sample"></a>Kör exemplet

Genom att använda HTTP-utlösta funktioner som ingår i exemplet kan du starta dirigeringen genom att skicka följande HTTP POST-begäran:

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

Orchestrator-funktionen tar emot det angivna telefonnumret och skickar omedelbart ett SMS till ett SMS med en slumpmässigt genererad, 4-siffrig verifierings kod &mdash; till exempel *2168*. Funktionen väntar sedan 90 sekunder för ett svar.

Om du vill svara med koden kan du använda [`RaiseEventAsync` (.net) eller `raiseEvent` (Java Script)](durable-functions-instance-management.md) i en annan funktion eller anropa **sendEventUrl** http post webhook som refereras i 202-svaret ovan, och ersätta `{eventName}` med namnet på händelsen `SmsChallengeResponse`:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Om du skickar detta innan timern upphör att gälla, slutförs dirigeringen och `output` fältet är inställt på `true`, vilket indikerar en lyckad verifiering.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Om du låter timern upphöra att gälla, eller om du anger fel kod fyra gånger, kan du fråga efter statusen och se en `false` Dirigerings funktion som anger att det inte gick att verifiera telefonen.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempel kod

Här är dirigeringen som en enskild C# fil i ett Visual Studio-projekt:

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet-paketet för att köra exempel koden nedan.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat några av de avancerade funktionerna i Durable Functions, särskilt `WaitForExternalEvent` och `CreateTimer` API: er. Du har sett hur dessa kan kombineras med `Task.WaitAny` för att implementera ett tillförlitligt tids gräns system, vilket ofta är användbart för att interagera med riktiga personer. Du kan lära dig mer om hur du använder Durable Functions genom att läsa en serie artiklar som erbjuder djupgående täckning av specifika ämnen.

> [!div class="nextstepaction"]
> [Gå till den första artikeln i serien](durable-functions-bindings.md)
