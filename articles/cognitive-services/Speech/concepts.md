---
title: Taligenkänning i Bing koncept | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Grundläggande begrepp som används i Microsoft Speech service.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965719"
---
# <a name="basic-concepts"></a>Grundläggande begrepp

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

På den här sidan beskrivs några grundläggande begrepp i Microsoft tal igenkännings tjänsten. Vi rekommenderar att du läser den här sidan innan du använder Microsoft tal igenkännings-API i programmet.

## <a name="understanding-speech-recognition"></a>Förstå tal igenkänning

Om det är första gången du skapar ett tal aktiverat program, eller om det är första gången du lägger till tal funktioner i ett befintligt program, hjälper det här avsnittet dig att komma igång. Om du redan har en viss upplevelse med tal aktiverade program kan du välja att bara Skim det här avsnittet, eller så kan du hoppa över det helt om du är en gammal hand på tal och du vill komma åt protokoll informationen.

### <a name="audio-streams"></a>Ljud strömmar

Alla grundläggande koncept för tal är *ljud strömmen*. Till skillnad från en tangenttryckning, som inträffar vid en enskild tidpunkt och innehåller en enda information, sprids en talad begäran över hundratals millisekunder och innehåller många kilobyte information. Tiden för att talas yttranden är några svårigheter för utvecklare som vill ha en smidig och elegant tal upplevelse för sitt program. Dagens datorer och algoritmer utför tal avskrifter inom cirka hälften av varaktigheten för uttryck, så en 2-sekunders uttryck kan skrivas om i ungefär 1 sekund, men alla program som upplever en 1 sekunds fördröjning vid bearbetning av användare är varken strömlinjeformade eller eleganta.

Lyckligt vis finns det olika sätt att "dölja" avskrifts tiden genom att utföra avskrifter på en del av uttryck medan användaren pratar en annan del. Genom att t. ex. dela upp en 1-sekund-uttryck till 10 segment om 100 millisekunder och genom att göra en avskriftering av varje segment i tur och upp till 450 av de totala 500 millisekunder som krävs för avskrifter kan vara "dolda", så att användaren inte är medveten om avskrifter utförs när de talar. När du funderar på det här exemplet måste du komma ihåg att tjänsten utför avskriften på föregående 100 millisekunder av ljud medan användaren pratar nästa 100, så när användaren slutar att tala, behöver tjänsten bara skriva av ungefär 100 millisekunder av ljud för att skapa ett resultat.

För att uppnå den här användar upplevelsen samlas talad ljud information in i segment och omsättas när användaren pratar. De här ljud segmenten sammantaget från *ljud strömmen*och processen för att skicka dessa ljud segment till tjänsten kallas för *ljud strömning.* Direkt uppspelning av ljud är en viktig del av alla tal aktiverade program. justering av segment storlek och optimering av strömnings implementeringen är några av de mest utnyttjade sätten att förbättra programmets användar upplevelse.

### <a name="microphones"></a>Mikrofoner

Människor behandlar talade ljud med sin öron, men inanimerad maskin vara använder mikrofoner. Om du vill aktivera tal i alla program måste du integrera med mikrofonen som tillhandahåller ljud strömmen för ditt program.

API: erna för mikrofonen måste tillåta att du startar och slutar att ta emot ljud byte från mikrofonen. Du måste bestämma vilka användar åtgärder som ska utlösa mikrofonen för att börja lyssna efter tal. Du kan välja att låta en knapp trycka utlösa starten av lyssningen, eller så kan du välja att ha ett *nyckel ord* eller ett *aktiverings ord* spotter alltid lyssnar på mikrofonen och att använda utdata från den modulen för att utlösa sändning av ljud till Microsoft Speech Telefonitjänstprovider.

### <a name="end-of-speech"></a>Slutet av tal

Att upptäcka *när* en talare har *slutat* att talan verkar vara tillräckligt lätt för människor, men är ett snarare svårt problem utanför laboratorie förhållanden. Det räcker inte att bara söka efter ren tystnad efter en uttryck, eftersom det ofta finns en mängd omgivande brus för att kunna ta sig till att komplicera saker. Microsoft Speech service är ett utmärkt jobb för att snabbt upptäcka när en användare har slutat prata, och tjänsten kan informera ditt program om detta, men den här ordningen innebär att ditt program är det sista du vet när användaren slutar att tala. Detta är inte på samma sätt som andra former av indata där ditt program är det *första* att veta när användarens indata startar *och* slutar.

### <a name="asynchronous-service-responses"></a>Svar på asynkron tjänst

Det faktum att ditt program måste informeras om när användarindata är slutförd ger inte några prestanda straff eller programmerings problem i ditt program, men det kräver att du funderar på tal förfrågningar på ett annat sätt än med begäran om indata svars mönster som du är van vid. Eftersom programmet inte vet när användaren slutar att tala, måste programmet fortsätta att strömma ljud till tjänsten samtidigt som det asynkront väntar på ett svar från tjänsten. Det här mönstret är till skillnad från andra webb protokoll för begäran/svar som HTTP. I dessa protokoll måste du slutföra en begäran innan du får svar. i Microsoft Speech service-protokollet får du svar *när du fortfarande strömma ljud för begäran*.

> [!NOTE]
> Den här funktionen stöds inte när tal-HTTP-REST API används.

### <a name="turns"></a>Aktiverar

Tal är ett informations bärare. När du pratar försöker du förmedla information som är i besittning till någon som lyssnar på den informationen. När du förmedlar information tar du vanligt vis till att tala och lyssna. Ditt tal aktiverade program interagerar med användare genom att växla och svara på olika sätt, även om programmet vanligt vis gör det mesta av lyssningen. Användarens talade indata och tjänst svaret till denna indata kallas för en *turn*. En *turn* startar när användaren pratar och slutar när programmet har slutfört hanteringen av tal tjänst svaret.

### <a name="telemetry"></a>Telemetri

Att skapa en tal aktive rad enhet eller ett program kan vara utmanande, även för erfarna utvecklare. Stream-baserade protokoll verkar ofta avskräckande vid första uppblicken och viktig information som till exempel tyst identifiering kan vara helt ny. Eftersom många meddelanden behöver skickas och tas emot för att slutföra ett enda begär ande-/svar-par, är det *mycket* viktigt att samla in fullständiga och korrekta data om dessa meddelanden. Microsoft Speech service Protocol tillhandahåller för insamling av dessa data. Du bör göra allt för att tillhandahålla nödvändiga data så korrekt som möjligt. genom att tillhandahålla fullständig och korrekt data hjälper du dig själv – om du skulle behöva hjälp från Microsoft Speech Service-teamet vid fel sökning av klient implementeringen kommer kvaliteten på de telemetridata som du har samlat in vara avgörande för problem analys.

> [!NOTE]
> Den här funktionen stöds inte när du använder REST API för tal igenkänning.

### <a name="speech-application-states"></a>Program tillstånd för tal

De steg som du vidtar för att aktivera tal inskrivning i programmet är lite annorlunda än stegen för andra former av inläsningar, till exempel mus klickningar eller finger knackning. Du måste hålla reda på när ditt program lyssnar på mikrofonen och skickar data till tal tjänsten när det väntar på svar från tjänsten och när det är i inaktivt läge. Relationen mellan dessa tillstånd visas i diagrammet nedan.

![Diagram över program status för tal](Images/speech-application-state-diagram.png)

Eftersom Microsoft Speech service deltar i några av de olika tillstånden definierar tjänst protokollet meddelanden som hjälper din programs över gång mellan tillstånd. Ditt program måste tolka och agera på dessa protokoll meddelanden för att spåra och hantera program tillstånden för tal.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Använda tjänsten för tal igenkänning från dina appar

Microsoft tal igenkännings tjänsten erbjuder två sätt för utvecklare att lägga till tal i sina appar.

- [REST-API: er](GetStarted/GetStartedREST.md): Utvecklare kan använda HTTP-anrop från sina appar till tjänsten för tal igenkänning.
- [Klient bibliotek](GetStarted/GetStartedClientLibraries.md): För avancerade funktioner kan utvecklare Hämta Microsofts tal klient bibliotek och länka till sina appar.  Klient biblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med olika språk (C#, Java, Java Script, ObjectiveC).

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klient bibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera ett kort talade ljud, till exempel kommandon (ljud längd < 15 s) utan interimistiska resultat | Ja | Ja |
| Konvertera ett långt ljud (> 15 s) | Nej | Ja |
| Strömma ljud med önskade interimistiska resultat | Nej | Ja |
| Förstå texten som konverteras från ljud med LUIS | Nej | Ja |

 Om ditt språk eller din plattform ännu inte har ett SDK kan du skapa en egen implementering baserat på [protokoll dokumentationen](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Igenkännings lägen

Det finns tre lägen för igenkänning: `interactive`, `conversation`och `dictation`. Igenkännings läget justerar tal igenkänningen baserat på hur användarna kan tala. Välj lämpligt igenkännings läge för ditt program.

> [!NOTE]
> Igenkännings lägena kan ha olika beteenden i REST-protokollet än i WebSocket-protokollet. REST API stöder till exempel inte kontinuerlig igenkänning, även i konversations-eller dikteringsläge.
> [!NOTE]
> Dessa lägen gäller när du använder REST-eller WebSocket-protokollet direkt. [Klient biblioteken](GetStarted/GetStartedClientLibraries.md) använder olika parametrar för att ange tolknings läge. Mer information finns i det klient bibliotek som du väljer.

Microsoft Speech service returnerar bara ett igenkännings fras resultat för alla igenkännings lägen. Det finns en gräns på 15 sekunder för alla enskilda uttryck.

### <a name="interactive-mode"></a>Interaktivt läge

I `interactive` läget gör en användare korta begär Anden och förväntar sig att programmet ska utföra en åtgärd som svar.

Följande egenskaper är typiska för program i interaktivt läge:

- Användare vet att de talar på en dator och inte till en annan mänsklig.
- Program användare vet i förväg vad de vill säga, baserat på vad de vill att programmet ska göra.
- Yttranden senast 2-3 sekunder.

### <a name="conversation-mode"></a>Konversations läge

I `conversation` läget är användarna engagerade i en mänsklig konversation.

Följande egenskaper är typiska för program i konversations läge:

- Användare vet att de pratar med en annan person.
- Tal igenkänning förbättrar de mänskliga konversationerna genom att låta en eller båda deltagarna se den talade texten.
- Användarna planerar inte alltid vad de vill säga.
- Användare använder ofta slang och annat informellt tal.

### <a name="dictation-mode"></a>Diktamensläge

I `dictation` läget omciterar användare längre yttranden till programmet för ytterligare bearbetning.

Följande egenskaper är typiska för program för dikterings-läge:

- Användare vet att de kommunicerar med en dator.
- Användarna visas text resultat för tal igenkänning.
- Användare planerar ofta vad de vill säga och använder mer formella språk.
- Användarna använder fullständiga meningar som de senaste 5-8 sekunderna.

> [!NOTE]
> I läget Dictation och konversationer returnerar inte Microsoft Speech service ofullständiga resultat. I stället returnerar tjänsten stabila fras resultat efter tystnads gränser i ljud strömmen. Microsoft kan förbättra tal protokollet för att förbättra användar upplevelsen i de här kontinuerliga igenkännings lägena.

## <a name="recognition-languages"></a>Igenkännings språk

*Tolknings språket* anger det språk som program användaren pratar med. Ange *tolknings språk* med frågesträngparametern för *språk* -URL i anslutningen. Värdet för *språkets* FRÅGEPARAMETER använder IETF-språktaggen [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag)och **måste** vara ett av de språk som stöds av API: t för tal igenkänning. Den fullständiga listan över språk som stöds av tal tjänsten finns på sidan [språk som stöds](API-Reference-REST/supportedlanguages.md).

Microsoft Speech service avvisar ogiltiga anslutnings begär Anden genom att `HTTP 400 Bad Request` Visa ett svar. En ogiltig begäran är en som:

- Innehåller inget värde för parameter värde för *språk* .
- Innehåller en språkfrågeparameter som är felaktigt formaterad.
- Innehåller en språkfrågeparameter som inte *är ett av* support språken.

Du kan välja att bygga ett program som stöder ett eller flera av de språk som stöds av tjänsten.

### <a name="example"></a>Exempel

I följande exempel använder ett program *samtals* tal igenkännings läge för en amerikansk engelsk högtalare.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Avskrifts svar

Avskrifts Svaren returnerar den konverterade texten från ljud till klienter. Ett avskrifts svar innehåller följande fält:

- `RecognitionStatus`anger status för igenkänningen. De möjliga värdena anges i tabellen nedan.

| State | Beskrivning |
| ------------- | ---------------- |
| Klart | Igenkänningen lyckades och visnings fältet finns |
| Omatcha | Tal påträffades i ljudströmmen, men inga ord från målspråket som kunde matchas. Mer information finns i identifierings status för [nomatchion (#nomatch-igenkänning-status).  |
| InitialSilenceTimeout | Starten av ljud strömmen innehöll bara tystnad, och tids gränsen för tjänsten nåddes i väntan på tal |
| BabbleTimeout | Starten av ljud strömmen innehöll bara brus, och tids gränsen för tjänsten nåddes i väntan på tal |
| Fel | Ett internt fel inträffade i igenkännings tjänsten och det gick inte att fortsätta |

- `DisplayText`representerar den identifierade frasen efter versaler, interpunktion och invertering av text-normalisering som har tillämpats och svordomar har maskerats med asterisker. Fältet visnings text är *bara* tillgängligt om `RecognitionStatus` fältet innehåller värdet. `Success`

- `Offset`anger förskjutningen (i 100 – nanosekunder) då frasen identifierades, i förhållande till starten av ljud strömmen.

- `Duration`anger varaktigheten (i 100 – nanosekunder) för den här tal frasen.

Ett avskrifts svar returnerar mer information om du vill. Se [utdataformat](#output-format) för att returnera mer detaljerade utdata.

Microsoft Speech service stöder ytterligare avskrifts processer som omfattar att lägga till versaler och interpunktion, maskera svordomar och normalisera text till vanliga formulär. Om en användare till exempel pratar i en fras som representeras av orden "Påminn mig att köpa sex iPhone", returnerar Microsofts tal tjänster texten "Påminn mig att köpa 6 iPhone". Processen som konverterar ordet "sex" till siffran "6" kallas *inverterad text normalisering* (*redundansväxlingar* för kort).

### <a name="nomatch-recognition-status"></a>Omatcha matchning, igenkännings status

Avskrifts svaret returneras `NoMatch` i `RecognitionStatus` när Microsoft Speech service identifierar tal i ljud strömmen men inte kan matcha det talet mot språkgrammatiken som används för begäran. Till exempel kan ett *nomatch* -villkor inträffa om en användare anger något på tyska när tolken förväntar oss på engelska som det talade språket. Våg mönstret i uttryck skulle tyda på förekomsten av mänskligt tal, men inget av orden som talas skulle matcha amerikansk engelska lexikon som används av tolken.

Ett annat *Nomatch* -villkor inträffar när igenkännings algoritmen inte kan hitta en korrekt matchning för de ljud som finns i ljud strömmen. När det här tillståndet inträffar kan Microsoft Speech service producera *tal. hypotes* meddelanden som innehåller en *hypotetisk text* men som skapar ett *tal. fras* meddelande där *RecognitionStatus* är *nomatch* . Det här tillståndet är normalt; du får inte göra några antaganden om precisionen eller åter givningen av texten i *tal. hypotes* -meddelandet. Dessutom måste du inte anta att eftersom Microsoft Speech service skapar *tal. hypotes* meddelanden som tjänsten kan skapa ett *tal. fras* meddelande med *RecognitionStatus* *lyckades*.

## <a name="output-format"></a>Utdataformat

Microsoft Speech service kan returnera olika nytto Last format i avskrifts svar. Alla nytto laster är JSON-strukturer.

Du kan kontrol lera frasernas resultat format genom att `format` ange URL-Frågeparametern. Som standard returnerar `simple` tjänsten resultat.

| Format | Beskrivning |
|-----|-----|
| `simple` | Ett förenklat fras resultat som innehåller igenkännings status och den identifierade texten i visnings formuläret. |
| `detailed` | En igenkännings status och en N-bästa lista över fras resultat där varje fras resultat innehåller alla fyra igenkännings formulär och en förtroende poäng. |

`duration` `RecognitionStatus` `Offset` [](#n-best-values)Formatet innehåller N-bästa värden, förutom, och, i svaret. `detailed`

### <a name="n-best-values"></a>N-bästa värden

Lyssnare, oavsett om det är mänsklig eller dator, kan aldrig vara säker på att de hörde *exakt* vad som talas. En lyssnare kan bara tilldela en *sannolikhet* till en viss tolkning av en uttryck.

I normala fall har människor en hög sannolikhet för att känna igen de ord som har talat om de ofta interagerar. Datorbaserade tal lyssnare strävar efter att uppnå liknande noggrannhets nivåer och, under de rätta villkoren, kan [de få paritet med människor](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmerna som används i tal igenkänning utforskar alternativa tolkningar av en uttryck som en del av normal bearbetning. De här alternativen förkastas vanligt vis som bevis för att en enda tolkning blir överbelastad. I mindre än optimala villkor slutförs tal igenkänningen med en lista över alternativa möjliga tolkningar. De översta *n* alternativen i den här listan kallas den *N-bästa listan*. Varje alternativ tilldelas en [förtroende poäng](#confidence). Förtroendet sträcker sig från 0 till 1. Poängen på 1 representerar den högsta nivån av förtroende. Poängen 0 representerar den lägsta nivån av förtroende.

> [!NOTE]
> Antalet poster i den N-bästa listan varierar mellan flera yttranden. Antalet poster kan variera mellan flera igenkänningar av *samma* uttryck. Den här variationen är ett naturligt och förväntat resultat av tal igenkännings algoritmens Probabilistic-karaktär.

Varje post som returneras i den N-bästa listan innehåller

- `Confidence`, som representerar [förtroende poängen](#confidence) för den här posten.
- `Lexical`, det vill säga den tolkade textens [lexikala form](#lexical-form) .
- `ITN`, som är [redundansväxlingar-formuläret](#itn-form) för den tolkade texten.
- `MaskedITN`, som är det [maskerade redundansväxlingar-formuläret](#masked-itn-form) för den tolkade texten.
- `Display`, som är [visnings formatet](#display-form) för den tolkade texten.

### Förtroende Poäng<a id="confidence"></a>

Förtroende poängen är integrerade för tal igenkännings system. Microsoft Speech service erhåller förtroende Poäng från en *förtroende klassificerare*. Microsoft tränar en förtroende klassificering över en uppsättning funktioner som är utformade för att maximally diskriminera mellan korrekt och felaktig igenkänning. Förtroende poängen utvärderas för enskilda ord och hela yttranden.

Om du väljer att använda förtroende poängen som returneras av tjänsten bör du vara medveten om följande beteende:

- Konfidens resultat kan bara jämföras inom samma tolknings läge och språk. Jämför inte poängen mellan olika språk eller olika tolknings lägen. Till exempel har en förtroende poäng i interaktivt igenkännings läge *ingen* korrelation till en förtroende poäng i diktamensläge.
- Förtroende poängen används bäst på en begränsad uppsättning yttranden. Det finns naturlig variation i poängen för en stor uppsättning yttranden.

Om du väljer att använda ett förtroende poäng värde som ett *tröskelvärde* som programmet agerar i använder du tal igenkänning för att fastställa tröskelvärdena.

- Kör tal igenkänning på ett representativt exempel på yttranden för ditt program.
- Samla in förtroende poängen för varje igenkänning i exempel uppsättningen.
- Basera ditt tröskelvärde på en viss percentil av förtroende för det exemplet.

Inget enskilt tröskelvärde är lämpligt för alla program. Ett acceptabelt förtroende poäng för ett program kan vara oacceptabelt för ett annat program.

### <a name="lexical-form"></a>lexikal form

Det lexikala formuläret är den tolkade texten, exakt hur det har inträffat i uttryck och utan skiljetecken eller Skift läge. Den lexikala formen för adressen "1020 Enterprise Way" skulle till exempel vara *10 20 på företags vägen*, förutsatt att det har talas på det sättet. Den lexikala formen av meningen "Påminn mig att köpa fem blyertspennas" är en *påminnelse om att köpa fem pennor*.

Lexikalisk form är lämplig för program som behöver utföra text normalisering som inte är standard. Lexikalisk form är också lämplig för program som behöver obearbetade igenkännings ord.

Svordomar maskeras aldrig i det lexikala formuläret.

### <a name="itn-form"></a>REDUNDANSVÄXLINGAR-formulär

Text normalisering är en process för att konvertera text från ett formulär till ett annat "kanoniskt" format. Telefonnumret "555-1212" kan till exempel konverteras till kanoniskt format *5 5 5 1 2 1 2*. *Unverse* text normalisering (redundansväxlingar) återför den här processen och konverterar orden "5 5 5 1 2 1 2" till den inverterade kanoniska formen *555-1212*. REDUNDANSVÄXLINGAR form av ett igenkännings resultat omfattar inte versaler eller skiljetecken.

REDUNDANSVÄXLINGAR-formuläret passar bäst för program som agerar på den tolkade texten. Ett program som gör det möjligt för en användare att tala om Sök villkor och som sedan använder dessa villkor i en webbfråga använder formuläret REDUNDANSVÄXLINGAR. Svordomar maskeras aldrig i REDUNDANSVÄXLINGAR-formuläret. Om du vill maskera svordomar använder du *formuläret maskerad redundansväxlingar*.

### <a name="masked-itn-form"></a>Maskerat REDUNDANSVÄXLINGAR-formulär

Eftersom svordomar naturligt är en del av det talade språket känner Microsoft Speech service igen sådana ord och fraser när de talas. Svordomar kanske inte är lämpliga för alla program, särskilt de program som har en begränsad, icke-vuxen användar mottagare.

Det maskerade REDUNDANSVÄXLINGAR-formuläret använder svordomar som maskning i formuläret för invertering av text. Om du vill maskera svordomar ställer du in värdet för parameter värde för svordomar till `masked`. När svordomar maskeras ersätts ord som identifieras som en del av ord listan för språkets svordomar med asterisker. Exempel: *Påminn mig att köpa 5 * * * * blyertspennas*. Det maskerade REDUNDANSVÄXLINGAR-formuläret för ett igenkännings resultat omfattar inte versaler eller skiljetecken.

> [!NOTE]
> Om värdet för parametern svordomar är inställt `raw`på, är det maskerade redundansväxlingar-formuläret detsamma som redundansväxlingar-formuläret. Svordomar maskeras *inte* .

### <a name="display-form"></a>Visa formulär

Skiljetecken och Skift läges signal där du får betoning, var du ska pausa och så vidare, vilket gör texten lättare att förstå. Visnings formuläret lägger till interpunktion och kapitalisering för att få igenkännings resultat, vilket gör det lämpligast för program som visar den talade texten.

Eftersom visnings formuläret utökar det maskerade redundansväxlingar-formuläret kan du ange värdet eller `masked` `raw`för parametern svordomar. Om värdet är inställt `raw`på, innehåller igenkännings resultatet eventuella svordomar som talas av användaren. Om värdet är inställt `masked`på, ersätts ord som identifieras som en del av ord listan för språkets svordomar med asterisker.

### <a name="sample-responses"></a>Exempel-svar

Alla nytto laster är JSON-strukturer.

Nytto Last formatet för `simple` fras resultatet:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Nytto Last formatet för `detailed` fras resultatet:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Svordomar – hantering i tal igenkänning

Microsoft Speech service känner igen alla former av mänskligt tal, inklusive ord och fraser som många personer skulle klassificera som "svordomar". Du kan styra hur tjänsten hanterar svordomar med hjälp av Frågeparametern för *svordomar* . Som standard maskas svordomar i *tal. fras* resultatet och returnerar inte tal-och *hypotes* -meddelanden som innehåller svordomar.

| *Svordomar* -värde | Beskrivning |
| - | - |
| `masked` | Maskar svordomar med asterisker. Det här är standardinställningen. |
| `removed` | Tar bort svordomar från alla resultat. |
| `raw` | Identifierar och returnerar svordomar i alla resultat. |

### <a name="profanity-value-masked"></a>Svordomar-värde`Masked`

Om du vill maskera svordomar ställer du in parametern *svordomar* på värdet *maskerad*. När Frågeparametern för *svordomar* har detta värde eller inte har angetts för en begäran, *maskerar* den svordomen. Tjänsten utför maskning genom att ersätta svordomar i igenkännings resultatet med asterisker. När du anger svordomar för svordomar returnerar tjänsten inte tal-och *hypotes* -meddelanden som innehåller svordomar.

### <a name="profanity-value-removed"></a>Svordomar-värde`Removed`

När Frågeparametern har värdet *Borttaget*, tar tjänsten *bort en svordom* från både *tal. fras* -och *tal-. hypotes* -meddelanden. Resultatet är detsamma *som om svordoms-orden inte talas*.

#### <a name="profanity-only-utterances"></a>Yttranden – endast svordomar

En användare kan *bara* tala om svordomar när ett program har konfigurerat tjänsten för att ta bort svordomar. I det här scenariot returnerar tjänsten inte ett *tal. result*om igenkännings läget är *Diktering* eller *konversation*. Om igenkännings läget är *interaktivt*, returnerar tjänsten ett *tal. resultat* med status koden *nomatch*.

### <a name="profanity-value-raw"></a>Svordomar-värde`Raw`

När Frågeparametern *har* värdet *RAW*, tar tjänsten inte bort eller maskar svordomar i antingen *tal. fras* eller *tal. hypotes* -meddelanden.
