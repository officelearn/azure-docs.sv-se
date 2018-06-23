---
title: Microsoft Translator tal API-referens | Microsoft Docs
titleSuffix: Cognitive Services
description: I referensdokumentationen för Microsoft översättare tal-API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355641"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator-API för talöversättning

Den här tjänsten erbjuder en strömmande API för att transkribera vardagliga samtalsuttryck tal från ett språk till text för ett annat språk. API: et är integrerat översatta texten tillbaka upp med text till tal-funktioner. Microsofts översättare Speech API möjliggör scenarier som realtid översättning av konversationer som visas i Skype-konverteraren.

Med Microsofts översättare Speech API klientprogram strömma tal ljud till tjänsten och få tillbaka en dataström med textbaserade resultat, bland annat den tolkade texten i käll-språk och dess översättning på språket som mål. Textresultat produceras genom att använda automatisk Speech Recognition (ASR) tillhandahålls av djupa neurala nätverk till inkommande ljudström. Rådata ASR utdata ytterligare förbättras av en ny teknik som kallas Sanntext för att avspegla närmare användaravsikt. Till exempel Sanntext tar bort disfluencies (hmms och coughs) och återställning rätt skiljetecken och skiftläge. Möjligheten att maskera med eller undanta profanities ingår också. Redovisning och översättning motorer är särskilt utbildade för att hantera vardagliga samtalsuttryck tal. Tjänsten tal översättning använder identifiering av tystnad för att avgöra i slutet av en utterance. Efter en paus i röst aktivitet direktuppspelas tjänsten tillbaka ett slutresultat för slutförda utterance. Tjänsten kan också skicka tillbaka ofullständiga resultat som ger mellanliggande erkännanden och översättningar för en utterance pågår. Slutgiltiga resultatet möjligheten tjänsten att syntetisera tal (text till tal) från talade texten i språk som mål. Text till tal-ljud har skapats i det format som anges av klienten. WAV och MP3 format är tillgängliga.

Microsofts översättare Speech API utnyttjar WebSocket-protokollet för att tillhandahålla en full duplex kommunikationskanalen mellan klienten och servern. Ett program behöver dessa steg för att använda tjänsten:

## <a name="1-getting-started"></a>1. Komma igång
Åtkomst till Microsoft översättare Text API du behöver [registrera dig för Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Autentisering

Använd prenumeration för att autentisera. Microsoft översättare tal-API: et stöder två lägen av autentisering:

* **Med hjälp av en åtkomst-token:** i ditt program att hämta en åtkomst-token från token-tjänsten. Använda din prenumeration Microsoft översättare tal-API-nyckel för att hämta en åtkomst-token från Autentiseringstjänsten kognitiva tjänster. Åtkomst-token är giltig i 10 minuter. Hämta en ny åtkomsttoken var 10: e minut och fortsätt att använda samma åtkomst-token för upprepad begäranden inom dessa 10 minuter.

* **Använda en prenumeration nyckel direkt:** i ditt program att överföra din prenumeration nyckel som ett värde i `Ocp-Apim-Subscription-Key` huvud.

Hantera din prenumeration nyckel och åtkomst-token som hemligheter som ska vara dold från vyn.

## <a name="3-query-languages"></a>3. Frågespråk
**Fråga resursen språk för den aktuella uppsättningen av språk som stöds.** Den [språk resurs](languages-reference.md) visar antal språk och röster som är tillgängliga för taligenkänning för textöversättning av och text till tal. Varje språk eller en röst tilldelas en identifierare som Microsoft översättare tal-API som används för att identifiera samma språk eller röst.

## <a name="4-stream-audio"></a>4. Dataströmmen ljud
**Öppna en anslutning och börjar spela upp ljud till tjänsten.** Tjänst-URL: en är `wss://dev.microsofttranslator.com/speech/translate`. Parametrar och ljud format förväntades av tjänsten beskrivs nedan, i den `/speech/translate` igen. En av parametrarna som används för att skicka den åtkomst-token från steg 2 ovan.

## <a name="5-process-the-results"></a>5. Bearbeta resultaten
**Bearbeta resultaten strömmas tillbaka från tjänsten.** Formatet för ofullständiga resultat, slutresultatet och text till tal-ljud segment beskrivs i dokumentationen för den `/speech/translate` åtgärden nedan.

Kodexempel som visar användningen av Microsofts översättare Speech API är tillgängliga från den [Microsoft översättare Github plats](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementeringsanteckningar

Hämta /speech/translate Establishes en session för översättning av tal

### <a name="connecting"></a>Ansluter
Granska listan över parametrar som ges senare i det här avsnittet innan du ansluter till tjänsten. En exempelbegäran är:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Begäran anger talade engelska kommer att strömmas till tjänsten och översättas till italienska. Varje slutliga recognition resultat genererar ett text till tal-ljud svar med hondjur rösten med namnet Elsa. Observera att denna begäran omfattar autentiseringsuppgifter i den `Ocp-Apim-Subscription-Key header`. Begäran också följer bästa praxis genom att ange en globalt unik identifierare i huvudet `X-ClientTraceId`. Ett klientprogram ska logga trace-ID så att den kan användas för att felsöka problem när de inträffar.

### <a name="sending-audio"></a>Skickar ljud
När anslutningen har upprättats börjar klienten direktuppspelning av ljud till tjänsten. Klienten skickar ljud i segment. Varje segment överförs med hjälp av ett Websocket-meddelande av typen Binary.

Ljud-indata är i formatet Wave ljud (WAVE eller mer, ofta kallade WAV på grund av dess filnamnstillägg). Klientprogrammet får strömma en kanal, signerat 16-bitars PCM ljud samplas vid 16 kHz. Den första uppsättningen med byte som strömmas av klienten kommer att innehålla WAV-huvudet. En 44 byte-huvudet är för en enda kanal signerat 16 bitar PCM dataströmmen samplas vid 16 kHz:

|Offset|Värde|
|:---|:---|
|0 - 3|”RIFF”|
|4 - 7|0|
|8 - 11|”WAVE”|
|12 - 15|”fmt”|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|”data”|
|40 - 43|0|

Observera att den totala filstorleken (byte 4 – 7) och storleken på ”data” (byte 40-43) anges till noll. Det är OK för strömmande scenariot där den totala storleken inte är nödvändigtvis känd förskott.

När du har skickat rubriken WAV (RIFF), skickar klienten segment av ljuddata. Klienten direktuppspelas vanligtvis med fast storlek segment som representerar en fast varaktighet (t.ex. dataströmmen 100ms ljud samtidigt).

### <a name="final-result"></a>Slutresultatet
Ett sista speech recognition resultat ska genereras i slutet av en utterance. Resultatet skickas till klienten med hjälp av ett WebSocket-meddelande av typen Text från tjänsten. Meddelandeinnehållet är JSON-serialisering av ett objekt med följande egenskaper:

* `type`: Strängkonstant att identifiera vilken typ av resultat. Värdet är slutgiltig slutgiltiga resultatet.
* `id`: Strängen identifierare för igenkänning resultatet.
* `recognition`: Tolkade texten på språket som källa. Texten som kan vara en tom sträng när det gäller ett falskt igenkänning.
* `translation`: Text okänt översättas på språket som mål.
* `audioTimeOffset`: Tidsförskjutningen i början av recognition i Tick (1 skalstreck = 100 nanosekunder). Förskjutningen är i förhållande till början av strömning.
* `audioTimeSize`: Varaktighet i redovisning-signaler (100 nanosekunder).
* `audioStreamPosition`: Byte-förskjutning i början av redovisning. Förskjutningen är i förhållande till början av dataströmmen.
* `audioSizeBytes`: Storlek i byte för redovisning.

Observera att placering av recognition i ljudströmmen inte ingår i resultaten som standard. Den `TimingInfo` funktionen måste väljas av klienten (se `features` parametern).

Ett exempel slutresultat är följande:

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

### <a name="partial-result"></a>Delresultat
Mellanliggande eller delvis tal resultat strömmas inte till klienten som standard. Klienten kan använda Frågeparametern funktioner för att begära dem.

En partiell resultatet skickas till klienten med hjälp av ett WebSocket-meddelande av typen Text från tjänsten. Meddelandeinnehållet är JSON-serialisering av ett objekt med följande egenskaper:

* `type`: Strängkonstant att identifiera vilken typ av resultat. Värdet är delvis för ofullständiga resultat.
* `id`: Strängen identifierare för igenkänning resultatet.
* `recognition`: Tolkade texten på språket som källa.
* `translation`: Text okänt översättas på språket som mål.
* `audioTimeOffset`: Tidsförskjutningen i början av recognition i Tick (1 skalstreck = 100 nanosekunder). Förskjutningen är i förhållande till början av strömning.
* `audioTimeSize`: Varaktighet i redovisning-signaler (100 nanosekunder).
* `audioStreamPosition`: Byte-förskjutning i början av redovisning. Förskjutningen är i förhållande till början av dataströmmen.
* `audioSizeBytes`: Storlek i byte för redovisning.

Observera att placering av recognition i ljudströmmen inte ingår i resultaten som standard. Funktionen TimingInfo måste väljas av klienten (se funktioner parameter).

Ett exempel slutresultat är följande:

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
När text till tal-funktionen är aktiverad (se `features` nedan), ett slutresultat följs av ljud talade översatt text. Ljuddata chunked och skickas från tjänsten till klienten som en sekvens av Websocket-meddelanden av typen Binary. En klient kan identifiera slutet på dataströmmen genom att kontrollera Finland bit för varje meddelande. Det sista binära meddelandet har dess Finland bit inställt på en för att visa slutet på dataströmmen. Formatet på dataströmmen beror på värdet för den `format` parameter.

### <a name="closing-the-connection"></a>Stänga anslutningen
När ett klientprogram har slutförts direktuppspelning av ljud och har fått det senaste slutresultatet, bör det stänga anslutningen genom att initiera WebSocket avslutande handskakningen. Det är villkor som gör att servern att avbryta anslutningen. Följande WebSocket stängd koder kan tas emot av klienten:

* `1003 - Invalid Message Type`: Servern avslutar anslutningen eftersom den inte kan acceptera den datatyp som togs emot. Detta händer ofta när inkommande ljud inte börjar med rätt rubrik.
* `1000 - Normal closure`: Anslutningen har stängts när begäran har uppfyllts. Servern stänger anslutningen: när inget ljud tas emot från klienten för en viss tid. När tystnad strömmas för en viss tid. När en session når maximal varaktighet tillåts (cirka 90 minuter).
* `1001 - Endpoint Unavailable`: Anger att servern blir otillgänglig. Klientprogrammet försöker ansluta med en gräns för antalet försök.
* `1011 - Internal Server Error`: Anslutningen kommer att stängas av på servern på grund av ett fel på servern.

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:---|:---|:---|:---|:---|
|API-version|1.0|Versionen av API: et som begärs av klienten. Tillåtna värden är: `1.0`.|DocumentDB   |sträng|
|från|(tom)   |Anger språket för inkommande tal. Värdet är en språkidentifierare från den `speech` scope i svaret från API: et för språk.|DocumentDB|sträng|
|till|(tom)|Anger språket för att översätta transcribed texten till. Värdet är en språkidentifierare från den `text` scope i svaret från API: et för språk.|DocumentDB|sträng|
|funktioner|(tom)   |Kommaavgränsade uppsättning funktioner som markerats av klienten. Tillgängliga funktioner är:<ul><li>`TextToSpeech`: Anger att tjänsten måste returnera den slutliga översatta meningen översatta ljuduppspelningen.</li><li>`Partial`: Anger att tjänsten måste returnera mellanliggande resultat när ljuduppspelningen strömning till tjänsten.</li><li>`TimingInfo`: Anger att tjänsten måste returnera tidsinställning information som associeras med varje igenkänning.</li></ul>Exempelvis kan en klient anger `features=partial,texttospeech` att ta emot ofullständiga resultat och text till tal, men ingen tidsinställning information. Observera att slutresultatet alltid strömmas till klienten.|DocumentDB|sträng|
|röst|(tom)|Identifierar vilken röst för text till tal-återgivningen av den översatta texten. Värdet är en röst identifierare från text till tal-scope i svaret från API: et för språk. Om en röst inte har angetts systemet ska automatiskt väljer du en när text till tal-funktionen är aktiverad.|DocumentDB|sträng|
|Format|(tom)|Anger formatet för text till tal-ljudström som returnerats av tjänsten. Tillgängliga alternativ är:<ul><li>`audio/wav`: Wave ljudström. Klienten bör använda WAV-huvudet kan tolka ljudformatet. Wave-ljud för text till tal är 16-bitars, en kanal PCM med en samplingsfrekvensen för 24 eller 16kHz.</li><li>`audio/mp3`: MP3 ljudström.</li></ul>Standardvärdet är `audio/wav`.|DocumentDB|sträng|
|ProfanityAction    |(tom)    |Anger hur tjänsten ska hantera profanities som identifieras i tal. Giltiga åtgärder är:<ul><li>`NoAction`: Profanities är kvar.</li><li>`Marked`: Profanities ersätts med en markör. Se `ProfanityMarker` parameter.</li><li>`Deleted`: Profanities tas bort. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a .".`</li></ul>Standardvärdet är markerat.|DocumentDB|sträng|
|profanityMarker|(tom)    |Anger hur identifierade profanities hanteras när `ProfanityAction` är inställd på `Marked`. Giltiga alternativ är:<ul><li>`Asterisk`: Profanities ersätts med strängen `***`. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a ***.".`</li><li>`Tag`: Svordomar omges av svordomar XML-taggen. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a <profanity>jackass</profanity>."`.</li></ul>Standardvärdet är `Asterisk`.|DocumentDB|sträng|
|Auktorisering|(tom)  |Anger värdet för klientens ägartoken. Använd prefixet `Bearer` följt av värdet för den `access_token` värdet som returneras av token Autentiseringstjänsten.|sidhuvud   |sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om de `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|
|access_token|(tom)   |Alternativa sätt att skicka en giltig OAuth-åtkomsttoken. Ägartoken tillhandahålls vanligtvis med rubriken `Authorization`. Vissa websocket-bibliotek tillåter inte klientkod för att ange rubriker. I så fall klienten kan använda den `access_token` Frågeparametern om du vill skicka en giltig token. När du använder en åtkomst-token för autentisering, om `Authorization` huvud har angetts, sedan `access_token` måste anges. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern. Klienter ska bara använda en metod för att skicka token.|DocumentDB|sträng|
|prenumerationen-nyckel|(tom)   |Alternativa sätt att ange nyckel för prenumerationen. Vissa websocket-bibliotek tillåter inte klientkod för att ange rubriker. I så fall klienten kan använda den `subscription-key` Frågeparametern för att ange en giltig prenumeration nyckel. När du använder en prenumeration för att autentisera, om `Ocp-Apim-Subscription-Key` huvud har angetts och prenumeration nyckeln måste anges. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern. Klienter ska bara använda en metod för att skicka den `subscription key`.|DocumentDB|sträng|
|X-ClientTraceId    |(tom)    |En genererad klient-GUID som används för att spåra en begäran. För korrekt felsökning av problem ska klienter ange ett nytt värde för varje begäran och logga.<br/>I stället för ett sidhuvud, kan det här värdet skickas med frågeparameter `X-ClientTraceId`. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-CorrelationId|(tom)    |En klient-genererade identifierare som används för att korrelera flera kanaler i en konversation. Flera sessioner för översättning av tal kan skapas för att aktivera konversationer mellan användare. I sådana fall kan använda alla tal översättning sessioner samma Korrelations-ID för att koppla samman de kanaler. Detta underlättar spårning och diagnostik. Identifieraren bör följa: `^[a-zA-Z0-9-_.]{1,64}$`<br/>I stället för ett sidhuvud, kan det här värdet skickas med frågeparameter `X-CorrelationId`. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-ClientVersion|(tom)    |Identifierar versionen av klientprogrammet. Exempel: ”2.1.0.123”.<br/>I stället för ett sidhuvud, kan det här värdet skickas med frågeparameter `X-ClientVersion`. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|sträng|
|X-OsPlatform|(tom)   |Identifierar namnet och versionen av operativsystemet klientprogrammet körs på. Exempel: ”Android 5.0”, ”iOs 8.1.3” ”, Windows 8.1”.<br/>I stället för ett sidhuvud, kan det här värdet skickas med frågeparameter `X-OsPlatform`. Om både sidhuvud och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|Svaret modellen|Sidhuvuden|
|:--|:--|:--|:--|
|101    |WebSocket-uppgraderingen.|Modellen exempelvärde <br/> objektet {}|X-RequestId<br/>Ett värde som identifierar begäran i felsökningssyfte.<br/>sträng|
|400    |Felaktig begäran. Kontrollera indataparametrarna för att säkerställa att de är giltiga. Objektet svaret innehåller en mer detaljerad beskrivning av felet.|||
|401    |Ej auktoriserad. Kontrollera att autentiseringsuppgifterna är inställda att de är giltiga och att prenumerationen Azure Data marknaden är i god stående med en tillgänglig balans.|||
|500    |Ett fel uppstod. Om felet kvarstår rapport med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|||
|503    |Servern är inte tillgänglig för tillfället. Försök att utföra begäran. Om felet kvarstår rapport med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|||

    


    





    
    




    




    




    

            




        









