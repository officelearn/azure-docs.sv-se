---
title: Translator Speech API referens
titleSuffix: Azure Cognitive Services
description: Referens dokumentation för Translator Speech API.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965377"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Den här tjänsten erbjuder ett strömnings-API för att skriva över samtals tal från ett språk till text för ett annat språk. API: et integrerar också text till tal-funktioner för att tala om översatta text. Translator Speech API möjliggör scenarier som översättning i real tid av konversationer som visas i Skype Translator.

Med Translator Speech API direktuppspelar klient program tal ljudet till tjänsten och får tillbaka en ström med textbaserade resultat som innehåller den tolkade texten på käll språket och dess översättning på mål språket. Textresultatet produceras genom automatisk taligenkänning (ASR) som drivs av djupa neurala nätverk för den inkommande ljudströmmen. RAW ASR-utdata förbättras ytterligare med en ny teknik som kallas TrueText för att bättre återspegla användar avsikten. TrueText tar till exempel bort disfluencies (hmms och coughs) och återställer korrekt interpunktion och Skift läge. Möjligheten att maskera eller undanta svordomar ingår också. Igenkänningsmotorn och översättningsmotorn är särskilt tränade för att hantera samtal. Tal översättnings tjänsten använder tystnads avkänning för att fastställa slutet på en uttryck. Efter en paus i röstaktiviteten återger tjänsten ett slutresultat för slutförda påståenden. Tjänsten kan också skicka tillbaka ofullständiga resultat, som ger mellanliggande igenkänningar och översättningar av ett pågående påstående. För slutliga resultat ger tjänsten möjlighet att syntetisera tal (text till tal) från den talade texten på mål språken. Text till tal-ljud skapas i det format som anges av klienten. WAV- och MP3-format är tillgängliga.

Translator Speech API utnyttjar WebSocket-protokollet för att tillhandahålla en kommunikations kanal med fullständig duplex mellan klienten och servern. Ett program behöver de här stegen för att använda tjänsten:

## <a name="1-getting-started"></a>1. Komma igång
För att få åtkomst till Translator Text API måste du [Registrera dig för Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Använd prenumerations nyckeln för att autentisera. Translator Speech API stöder två autentiserings lägen:

* **Använda en åtkomsttoken:** Hämta en åtkomsttoken från token-tjänsten i ditt program. Använd din Translator Speech API prenumerations nyckel för att hämta en åtkomsttoken från tjänsten Azure Cognitive Services-autentisering. Åtkomsttoken är giltig i 10 minuter. Hämta en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomsttoken för upprepade begär Anden inom 10 minuter.

* **Använda en prenumerations nyckel direkt:** I ditt program skickar du prenumerations nyckeln som ett värde i `Ocp-Apim-Subscription-Key` sidhuvudet.

Behandla din prenumerations nyckel och åtkomsttoken som hemligheter som ska döljas från vyn.

## <a name="3-query-languages"></a>3. Frågespråk
**Fråga språk resursen efter den aktuella uppsättningen språk som stöds.** [Språk resursen](languages-reference.md) visar en uppsättning språk och röster som är tillgängliga för tal igenkänning, för text översättning och för text till tal. Varje språk eller röst får en identifierare som Translator Speech API använder för att identifiera samma språk eller röst.

## <a name="4-stream-audio"></a>4. Strömma ljud
**Öppna en anslutning och börja strömma ljud till tjänsten.** Tjänstens URL är `wss://dev.microsofttranslator.com/speech/translate`. Parametrar och ljud format som förväntas av tjänsten beskrivs nedan i `/speech/translate` åtgärden. En av parametrarna används för att skicka åtkomsttoken från steg 2 ovan.

## <a name="5-process-the-results"></a>5. Bearbetar resultaten
**Bearbeta resultaten strömmas tillbaka från tjänsten.** Formatet för partiella resultat, slutgiltiga resultat och text till tal-ljud segment beskrivs i dokumentationen för `/speech/translate` åtgärden nedan.

Kod exempel som demonstrerar användningen av Translator Speech API är tillgängliga från [webbplatsen Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementerings anteckningar

Hämta/Speech/translate upprättar en session för tal Översättning

### <a name="connecting"></a>Ansluter
Innan du ansluter till tjänsten bör du gå igenom listan med parametrar som beskrivs senare i det här avsnittet. En exempel förfrågan är:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Begäran anger att talade engelska ska strömmas till tjänsten och översättas till italienska. Varje slutligt igenkännings resultat genererar ett text till tal-ljud med den kvinna röst som heter Elsa. Observera att begäran innehåller autentiseringsuppgifter i `Ocp-Apim-Subscription-Key header`. Begäran följer också bästa praxis genom att ange en globalt unik identifierare i huvudet `X-ClientTraceId`. Ett klient program ska logga spårnings-ID: t så att det kan användas för att felsöka problem när de inträffar.

### <a name="sending-audio"></a>Skickar ljud
När anslutningen har upprättats börjar klienten strömma ljud till tjänsten. Klienten skickar ljud i segment. Varje segment överförs med ett WebSocket-meddelande av typen Binary.

Ljud indata är i waveform audio-filformat (WAVE eller ofta kallat WAV på grund av fil namns tillägget). Klient programmet måste strömma en enskild kanal, signerat bitarsläge PCM-ljud med 16 kHz. Den första uppsättningen byte som strömmas av klienten kommer att inkludera WAV-huvudet. En 44 byte-rubrik för en enkanaligt signerad 16-bitars PCM-dataström samplas med 16 kHz:

|Offset|Value|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|LJUD|
|12 - 15|fmt|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 – 33|2|
|34 – 35|16|
|36 – 39|"data"|
|40 – 43|0|

Observera att den totala fil storleken (byte 4-7) och storleken på "data" (byte 40-43) är inställd på noll. Det här är OK för det strömmande scenariot där den totala storleken inte nödvändigt vis är känd.

När du har skickat WAV-huvudet (RIFF) skickar klienten segment av ljud data. Klienten direktuppspelar vanligt vis fast storleks segment som representerar en fast varaktighet (t. ex. Stream-100 ms för ljud i taget).

### <a name="signal-the-end-of-the-utterance"></a>Signalera slutet på uttryck
Translator Speech API returnerar avskriften och översättningen av ljud strömmen när du skickar ljudet. Den slutliga avskriften, den slutliga översättningen och det översatta ljudet returneras till dig bara efter slutet av uttryck. I vissa fall kanske du vill framtvinga slutet av uttryck. Skicka 2,5 sekunders tystnad för att framtvinga uttryck-slutet.

### <a name="final-result"></a>Slut resultat
Ett avslutande tal igenkännings resultat genereras i slutet av en uttryck. Ett resultat överförs från tjänsten till klienten med ett WebSocket-meddelande av typen text. Meddelande innehållet är JSON-serialiseringen för ett objekt med följande egenskaper:

* `type`: Strängkonstant för att identifiera typen av resultat. Värdet är final för slutgiltiga resultat.
* `id`: Sträng identifierare som tilldelas till igenkännings resultatet.
* `recognition`: Identifierad text i käll språket. Texten kan vara en tom sträng om en falsk igenkänning skulle returneras.
* `translation`: Identifierad text har översatts på mål språket.
* `audioTimeOffset`: Tids förskjutning för början av igenkänningen i Tick (1 Tick = 100 nanosekunder). Förskjutningen är relativ till början av streaming.
* `audioTimeSize`: Varaktighet i Tick (100 nanosekunder) av igenkänningen.
* `audioStreamPosition`: Byte förskjutning för början av igenkänningen. Offset är i förhållande till början av data strömmen.
* `audioSizeBytes`: Storlek i byte för igenkänning.

Observera att placeringen av igenkänningen i ljud strömmen inte ingår som standard i resultatet. Funktionen måste väljas av klienten (se `features` parameter). `TimingInfo`

Ett exempel på slut resultatet är följande:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Partiellt resultat
Partiella eller mellanliggande tal igenkännings resultat strömmas inte till klienten som standard. Klienten kan använda features-Frågeparametern för att begära dem.

Ett partiellt resultat överförs från tjänsten till klienten med ett WebSocket-meddelande av typen text. Meddelande innehållet är JSON-serialiseringen för ett objekt med följande egenskaper:

* `type`: Strängkonstant för att identifiera typen av resultat. Värdet är delvis för partiella resultat.
* `id`: Sträng identifierare som tilldelas till igenkännings resultatet.
* `recognition`: Identifierad text i käll språket.
* `translation`: Identifierad text har översatts på mål språket.
* `audioTimeOffset`: Tids förskjutning för början av igenkänningen i Tick (1 Tick = 100 nanosekunder). Förskjutningen är relativ till början av streaming.
* `audioTimeSize`: Varaktighet i Tick (100 nanosekunder) av igenkänningen.
* `audioStreamPosition`: Byte förskjutning för början av igenkänningen. Offset är i förhållande till början av data strömmen.
* `audioSizeBytes`: Storlek i byte för igenkänning.

Observera att placeringen av igenkänningen i ljud strömmen inte ingår som standard i resultatet. Funktionen TimingInfo måste väljas av klienten (se features-parametern).

Ett exempel på slut resultatet är följande:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Text till tal
När text till tal-funktionen är aktive rad (se `features` parametern nedan), följs det slutliga resultatet av ljudet av den talade översatta texten. Ljud data delas och skickas från tjänsten till klienten som en sekvens med WebSocket-meddelanden av typen Binary. En klient kan identifiera slutet på data strömmen genom att kontrol lera FIN biten för varje meddelande. Det sista binära meddelandet har en FIN-bit inställd på ett för att ange data strömmens slut. Formatet för data strömmen beror på `format` parameterns värde.

### <a name="closing-the-connection"></a>Stänger anslutningen
När ett klient program har slut på strömning av ljud och har fått det sista slutliga resultatet bör det stänga anslutningen genom att initiera hand skakningen för WebSocket. Det finns villkor som gör att servern avbryter anslutningen. Följande koder för WebSocket-stängda kan tas emot av klienten:

* `1003 - Invalid Message Type`: Servern avslutar anslutningen eftersom den inte kan acceptera den mottagna data typen. Detta händer ofta när inkommande ljud inte börjar med en korrekt rubrik.
* `1000 - Normal closure`: Anslutningen har stängts efter att begäran har uppfyllts. Servern kommer att stänga anslutningen: när inget ljud tas emot från klienten under en längre tid. När tystnads fri sten strömmas under en längre tid. När en session når den längsta tillåtna varaktigheten (cirka 90 minuter).
* `1001 - Endpoint Unavailable`: Anger att servern kommer att bli otillgänglig. Klient programmet kan försöka återansluta med en gräns för antalet återförsök.
* `1011 - Internal Server Error`: Anslutningen stängs av servern på grund av ett fel på servern.

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:---|:---|:---|:---|:---|
|API-versionen|1.0|Den version av API: t som klienten begär. Tillåtna värden är: `1.0`.|query   |sträng|
|from|saknas   |Anger språket för det inkommande talet. Värdet är en av språk identifierarna från `speech` omfånget i svaret från språk-API: et.|query|sträng|
|till|saknas|Anger det språk som den beskrivande texten ska översättas till. Värdet är en av språk identifierarna från `text` omfånget i svaret från språk-API: et.|query|sträng|
|funktioner|saknas   |En kommaavgränsad uppsättning funktioner som valts av klienten. Tillgängliga funktioner är:<ul><li>`TextToSpeech`: anger att tjänsten måste returnera det översatta ljudet av den slutliga översatta meningen.</li><li>`Partial`: anger att tjänsten måste returnera mellanliggande igenkännings resultat medan ljudet strömmas till tjänsten.</li><li>`TimingInfo`: anger att tjänsten måste returnera tids information som är associerad med varje igenkänning.</li></ul>En klient kan till exempel ange `features=partial,texttospeech` att delar av resultat ska tas emot och text till tal, men ingen tids inställnings information. Observera att de slutliga resultaten alltid strömmas till klienten.|query|sträng|
|Röst|saknas|Identifierar vilken röst som ska användas för text till tal-åter givning av den översatta texten. Värdet är ett av röst identifierarna från TTS-omfånget i svaret från språk-API: et. Om ingen röst anges väljer systemet automatiskt en när text till tal-funktionen är aktive rad.|query|sträng|
|format|saknas|Anger formatet för text till tal-ljud strömmar som returneras av tjänsten. De tillgängliga alternativen är:<ul><li>`audio/wav`: Waveform Audio Stream. Klienten bör använda WAV-huvudet för att tolka ljud formatet korrekt. WAV-ljud för text till tal är 16 bitar, Single Channel PCM med en samplings frekvens på 24kHz eller 16kHz.</li><li>`audio/mp3`: MP3, ljud ström.</li></ul>Standardvärdet är `audio/wav`.|query|sträng|
|ProfanityAction    |saknas    |Anger hur tjänsten ska hantera svordomar som identifieras i talet. Giltiga åtgärder är:<ul><li>`NoAction`: Svordomar är kvar som de är.</li><li>`Marked`: Svordomar ersätts med en markör. Se `ProfanityMarker` parameter.</li><li>`Deleted`: Svordomar tas bort. Om ordet `"jackass"` till exempel behandlas som en svordom, kommer frasen `"He is a jackass."` att bli`"He is a .".`</li></ul>Standardvärdet är markerat.|query|sträng|
|ProfanityMarker|saknas    |Anger hur identifierade svordomar hanteras när `ProfanityAction` är inställt på. `Marked` Giltiga alternativ är:<ul><li>`Asterisk`: Svordomar ersätts med strängen `***`. Om ordet `"jackass"` till exempel behandlas som en svordom, kommer frasen `"He is a jackass."` att bli`"He is a ***.".`</li><li>`Tag`: Svordomar omges av en XML-tagg med en svordom. Om ordet `"jackass"` till exempel behandlas som en svordom, kommer frasen `"He is a jackass."` att bli `"He is a <profanity>jackass</profanity>."`.</li></ul>Standardvärdet är `Asterisk`.|query|sträng|
|Authorization|saknas  |Anger värdet för klientens Bearer-token. Använd prefixet `Bearer` följt av värdet för det `access_token` värde som returneras av tjänsten Authentication token.|sidhuvud   |sträng|
|OCP-Apim-Subscription-Key|saknas|Krävs om `Authorization` rubriken inte har angetts.|sidhuvud|sträng|
|access_token|saknas   |Alternativt sätt att skicka en giltig OAuth-åtkomsttoken. Bearer-token tillhandahålls vanligt vis med `Authorization`header. Vissa WebSocket-bibliotek tillåter inte att klient koden anger huvuden. I så fall kan klienten använda `access_token` Frågeparametern för att skicka en giltig token. När du använder en åtkomsttoken för att autentisera, `Authorization` `access_token` måste du ange om huvudet inte är angivet. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern. Klienter bör bara använda en metod för att skicka token.|query|sträng|
|prenumerations nyckel|saknas   |Alternativt sätt att skicka prenumerations nyckeln. Vissa WebSocket-bibliotek tillåter inte att klient koden anger huvuden. I så fall kan klienten använda `subscription-key` Frågeparametern för att skicka en giltig prenumerations nyckel. När du använder en prenumerations nyckel för att `Ocp-Apim-Subscription-Key` autentisera om huvudet inte har angetts måste prenumerations nyckeln anges. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern. Klienter bör bara använda en metod för att skicka `subscription key`.|query|sträng|
|X-ClientTraceId    |saknas    |Ett GUID som skapats av klienten som används för att spåra en begäran. För korrekt fel sökning av problem bör klienterna ange ett nytt värde för varje begäran och logga det.<br/>I stället för att använda ett sidhuvud kan det här värdet skickas med Frågeparametern `X-ClientTraceId`. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-CorrelationId|saknas    |En identifierare som skapats av klienten som används för att korrelera flera kanaler i en konversation. Flera tal översättnings sessioner kan skapas för att aktivera konversationer mellan användare. I detta scenario använder alla tal översättnings sessioner samma korrelations-ID för att koppla ihop kanalerna. Detta underlättar spårning och diagnostik. Identifieraren ska uppfylla följande:`^[a-zA-Z0-9-_.]{1,64}$`<br/>I stället för att använda ett sidhuvud kan det här värdet skickas med Frågeparametern `X-CorrelationId`. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-ClientVersion|saknas    |Identifierar versionen av klient programmet. Exempel: "2.1.0.123".<br/>I stället för att använda ett sidhuvud kan det här värdet skickas med Frågeparametern `X-ClientVersion`. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-OsPlatform|saknas   |Identifierar namnet och versionen för det operativ system som klient programmet körs på. Exempel: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>I stället för att använda ett sidhuvud kan det här värdet skickas med Frågeparametern `X-OsPlatform`. Om både huvud-och Frågeparametern anges, ignoreras Frågeparametern.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|Svars modell|Huvuden|
|:--|:--|:--|:--|
|101    |WebSocket-uppgradering.|Exempel värde för modell <br/> Jobbobjektet{}|X-RequestId<br/>Ett värde som identifierar begäran om fel söknings syfte.<br/>sträng|
|400    |Felaktig begäran. Kontrol lera indataparametrarna för att säkerställa att de är giltiga. Objektet Response innehåller en mer detaljerad beskrivning av felet.|||
|401    |Tillstånd. Se till att autentiseringsuppgifterna är inställda, att de är giltiga och att Azure Data Marketing-prenumerationen är i ett snyggt skick med ett tillgängligt saldo.|||
|500    |Ett fel uppstod. Om felet kvarstår kan du rapportera det med klient spårnings-ID: n (X-ClientTraceId) eller begärande-ID (X-RequestId).|||
|503    |Servern är inte tillgänglig för tillfället. Försök att utföra begäran igen. Om felet kvarstår kan du rapportera det med klient spårnings-ID: n (X-ClientTraceId) eller begärande-ID (X-RequestId).|||
