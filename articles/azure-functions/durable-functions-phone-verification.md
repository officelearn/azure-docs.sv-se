---
title: "Mänsklig interaktion och timeout i varaktiga funktioner – Azure"
description: "Lär dig mer om att hantera mänsklig interaktion och timeout i tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: cfb6758703ebf3ce0458a4e1ad74324a4ccc2ece
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Mänsklig interaktion i varaktiga funktioner - Phone verifiering exempel

Det här exemplet visar hur du skapar en [varaktiga funktioner](durable-functions-overview.md) orchestration som inbegriper mänsklig interaktion. När en person är inblandad i en automatiserad process måste processen kunna skicka meddelanden till personen och få svar asynkront. Det måste även tillåta möjlighet att personen är inte tillgänglig. (Det här sista delen är där tidsgränser blir viktiga).

Det här exemplet implementerar en SMS-baserade phone verifieringssystem. Dessa typer av flöden används ofta när du verifierar en kunds telefonnummer eller för multifaktorautentisering (MFA). Det här är en kraftfull exempel eftersom hela implementeringen görs med hjälp av ett par små funktioner. Det krävs inga externa datalager, till exempel en databas.

## <a name="prerequisites"></a>Krav

* Följ instruktionerna i [installera varaktiga funktioner](durable-functions-install.md) att ställa in provet.
* Den här artikeln förutsätter att du redan har gått igenom de [Hello sekvens](durable-functions-sequence.md) exempel genomgången.

## <a name="scenario-overview"></a>Scenarioöversikt

Telefonverifiering är en används för att kontrollera att slutanvändare för ditt program inte är skickar skräppost och att de är de som de säger att de är. Multifaktorautentisering är ett vanligt användningsfall för att skydda användarkonton från hackare. Utmaningen med implementera phone verifieringen är att det krävs en **tillståndskänslig interaktion** med en människa som. En användare anges vanligtvis kod (t.ex. en 4 siffror) och måste svara **inom en rimlig tid**.

Vanlig Azure Functions tillståndslös (som är många andra molntjänster slutpunkter för andra plattformar), så att dessa typer av interaktioner måste du uttryckligen hantera i en databas eller några andra beständiga tillståndslager externt. Interaktionen måste dessutom delas i flera funktioner som kan samordnas tillsammans. Till exempel behöver du minst en funktion för att bestämma om en, spara den någonstans och skicka det till användarens telefon. Dessutom måste minst en annan funktion att ta emot ett svar från användaren och mappa det på något sätt tillbaka till det ursprungliga funktionsanropet för att göra validering kod. En tidsgräns är också en viktig aspekt att säkerställa säkerheten. Detta kan få ganska komplex ganska snabbt.

Det här scenariot komplexitet minskas avsevärt när du använder beständiga funktioner. Som du ser i det här exemplet kan en orchestrator-funktion hantera tillståndskänslig interaktionen lätt och utan att inbegripa eventuella externa datakällor. Eftersom orchestrator-funktioner är *varaktiga*, flödena interaktiva är också mycket pålitliga.

## <a name="configuring-twilio-integration"></a>Konfigurera Twilio-integrering

Det här exemplet är med hjälp av den [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobiltelefon. Azure Functions redan har stöd för Twilio via den [Twilio bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), och funktionen används.

Den först öppna du du behöver är ett Twilio-konto. Du kan skapa ett kostnadsfritt på https://www.twilio.com/try-twilio. När du har ett konto kan du lägga till följande tre **appinställningar** i projektet.

| Appen inställningsnamn | Värdebeskrivning |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnumret som är associerade med ditt Twilio-konto. Det här används för att skicka SMS-meddelanden. |

## <a name="the-functions"></a>Funktionerna

Den här artikeln innehåller stegvisa följande funktioner i exempelappen:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

I följande avsnitt beskrivs konfiguration och kod som används för utveckling av Azure portal. Kod för Visual Studio-utveckling visas i slutet av artikeln.
 
## <a name="the-sms-verification-orchestration"></a>SMS verifiering orchestration

Den **E4_SmsPhoneVerification** funktion använder vanlig *function.json* för orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Här är den kod som implementerar funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

När den har startat, gör orchestrator funktionen följande:

1. Hämtar ett telefonnummer som den ska *skicka* SMS-meddelande.
2. Anrop **E4_SendSmsChallenge** för att skicka ett SMS-meddelande till användare och returnerar tillbaka det förväntade 4-siffrig kontrollkoden.
3. Skapar en beständig timer som utlösare 90 sekunder från den aktuella tiden.
4. Parallellt med timern väntar på att en **SmsChallengeResponse** händelse från användaren.

Användaren får ett SMS-meddelande med ett fyrsiffrigt kod. De har 90 sekunder att skicka samma 4-siffriga koden tillbaka till orchestrator funktionen instans att slutföra verifieringsprocessen. Om de skickar fel kod kan de få en ytterligare tre försök att hämta höger (inom samma 90 andra fönstret).

> [!NOTE]
> Det kanske inte är uppenbara vid första, men den här orchestrator funktionen är helt deterministisk. Detta beror på att den `CurrentUtcDateTime` egenskapen används för att beräkna förfallotiden timer och den här egenskapen returnerar samma värde på alla Repetera nu i orchestrator-koden. Det är viktigt att se till att samma `winner` resultat av varje upprepade anrop till `Task.WhenAny`.

> [!WARNING]
> Det är viktigt att [Avbryt timers med hjälp av en CancellationTokenSource](durable-functions-timers.md) om du inte längre behöver dem att upphöra, som i exemplet ovan då svar tas emot.

## <a name="send-the-sms-message"></a>Skicka SMS-meddelande

Den **E4_SendSmsChallenge** funktionen använder Twilio-bindning för att skicka SMS: et med 4-siffrig kod till slutanvändaren. Den *function.json* definieras enligt följande:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Och här är koden som genererar 4-siffrig kontrollkoden och skickar SMS-meddelande:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

Detta **E4_SendSmsChallenge** funktionen endast anropas en gång, även om processen kraschar eller hämtar spelas. Det är bra eftersom du inte vill att användaren hämtar flera SMS-meddelanden. Den `challengeCode` returnera värdet sparas automatiskt, så att funktionen orchestrator alltid vet vad som är rätt kod.

## <a name="run-the-sample"></a>Köra exemplet

Du använder HTTP-utlösta funktioner som ingår i exemplet, kan du starta orchestration genom att skicka följande HTTP POST-begäran.

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

Funktionen orchestrator tar emot det angivna telefonnummer och skickar omedelbart ett SMS-meddelande med en slumpmässigt genererad 4-siffrig Verifieringskod &mdash; exempelvis *2168*. Funktionen väntar sedan 90 sekunder för svar.

Du kan använda för att svara med kod `RaiseEventAsync` inuti ett annat fungera eller anropa den **sendEventUrl** HTTP POST webhook som refereras i 202 svaret ovan ersätter `{eventName}` med namnet på händelsen, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Om du skickar detta innan timern upphör att gälla, orchestration har slutförts och `output` fältet är inställt på `true`, som anger en lyckad verifiering.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Om du låta timern ut, eller om du anger fel kod fyra gånger, kan du fråga efter status och se en `false` orchestration funktionen utdata, som indikerar att telefonverifiering misslyckades.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-exempelkod

Här är orchestration som en enda C#-filen i Visual Studio-projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat att vissa av de avancerade funktionerna i varaktiga funktion, särskilt `WaitForExternalEvent` och `CreateTimer`. Du har sett hur dessa kan kombineras med `Task.WaitAny` att implementera en tillförlitlig timeout-systemet, vilket ofta är användbart för att interagera med verkliga personer. Mer information om hur du använder beständiga funktioner genom att läsa ett antal artiklar som ger mer information om specifika ämnen.

> [!div class="nextstepaction"]
> [Gå till den första artikeln i serien](durable-functions-bindings.md)
