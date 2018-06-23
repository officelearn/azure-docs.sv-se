---
title: Begrepp | Microsoft Docs
description: Grundläggande begrepp som används i Microsoft tal Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: bc23f4fb7dfc045a0f8cc87155c31875c4de8450
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352308"
---
# <a name="basic-concepts"></a>Grundläggande begrepp

Den här sidan beskrivs några grundläggande begrepp i Microsoft speech recognition service. Vi rekommenderar att du kan läsa den här sidan innan du använder Microsofts taligenkänning API i ditt program.

## <a name="understanding-speech-recognition"></a>Förstå taligenkänning

Om det här är första gången du skapar ett tal-aktiverat program, eller om det är första gången du lägger till talfunktioner för till ett befintligt program, hjälper i det här avsnittet dig att komma igång. Om du redan har viss erfarenhet av tal-aktiverade program kan du välja att bara skumma igenom det här avsnittet eller kan du hoppa över det helt om du är en gammal hand vid tal och du vill ha rätt att protokollet information.

### <a name="audio-streams"></a>Ljudströmmar

Den viktigaste bland de grundläggande principerna för taligenkänning är den *ljudström*. Till skillnad från en tangenttryckning som inträffar på ett ställe i tid och innehåller en enda typ av information, en talade begäran är utspridd på hundratals millisekunder och innehåller många kilobyte information. Varaktighet för talade utterances presenterar vissa svårigheter för utvecklare som vill tillhandahålla en effektiv och elegant tal för sina program. Dagens datorer och algoritmer utför du tal skrivfel i cirka hälften av varaktigheten för utterance, så kan uppges enligt en 2-sekund utterance ungefär 1 sekund, men alla program som påträffar en 1 sekund försening i behandling av användaren är varken effektiviserad eller elegant.

Lyckligtvis finns olika sätt att ”dölja” skrivfel tid genom att utföra skrivfel på en del av utterance när användaren är tala en annan del. Är till exempel genom att dela en 1 sekund utterance till 10 mängder 100 millisekunder och genom att utföra skrivfel på varje segment i sin tur över 450 totala 500 millisekunder som krävs för skrivfel kan vara ”dolda” så att användaren är inte medveten om skrivfel som utförs när han eller hon tal. När tänka på att det här exemplet komma ihåg att tjänsten fungerar skrivfel på föregående 100 millisekunder av ljud när användaren är tala nästa 100, så när användaren slutar sett tjänsten behöver bara transkribera ungefär 100 millisekunder av ljud för att producera ett resultat.

För att uppnå det här användarupplevelse talade ljud information som samlas in i segment och som användaren upp följande sätt. Dessa ljud segment gemensamt från den *ljudström*, och skicka dessa ljud segment till tjänsten kallas *direktuppspelning av ljud.* Direktuppspelning av ljud är en viktig del av något tal-aktiverade program. Justera segmentstorleken och optimera strömmande implementering är några av de mest effektfulla sätt för att förbättra användarupplevelsen för ditt program.

### <a name="microphones"></a>Mikrofoner

Personer bearbeta tal med hjälp av sina öronen, men rörelse i orörliga maskinvara använder mikrofoner. Om du vill aktivera tal på alla program, måste du integrera med mikrofon tillhandahåller ljudströmmen för ditt program.

API: er för din mikrofon måste kan du starta och stoppa ljud byte från mikrofonen. Du måste bestämma vad användaråtgärder utlöser mikrofon påbörja avlyssning för tal. Du kan välja att låta en knapptryckning utlösa början av lyssnar eller du har en *nyckelord* eller *wake word* spotter lyssnar alltid mikrofonens och använda utdata från modulen till Utlös skicka ljud till tjänsten Microsoft tal.

### <a name="end-of-speech"></a>Slutet av tal

Identifiering av *när* föredragshållare har *stoppats* tala verkar enkelt för människor men är ganska svårt problem utanför laboratoriemiljön. Det räcker inte att helt enkelt leta efter ren tystnad efter en utterance eftersom det är ofta mycket bakgrundsljud att göra det svårare att saker. Tjänsten Microsoft tal matchar en utmärkt jobbet snabbt identifiera när en användare har slutat sett och tjänsten kan informera programmet om detta, men i den här ordningen innebär att programmet är sist du behöver veta när användaren stoppa tal. Detta är inte alls som andra former av indata där programmet är den *första* att veta när användaren input startar *och* slutar.

### <a name="asynchronous-service-responses"></a>Asynkrona service svar

Det faktum att programmet behöver bli meddelad om när användarindata är klar inte införa några prestandaförsämringar eller programming problem på ditt program, men den kräver att du tycker om tal begäranden annorlunda än den inkommande begäranden / svaret mönster som du är bekant. Eftersom programmet inte vet när användaren slutar tala, måste ditt program fortsätta att strömma ljud till tjänsten under samtidigt och asynkront väntan på svar från tjänsten. Det här mönstret skiljer sig från andra frågor och svar webbprotokoll som HTTP. Dessa protokoll, måste du utföra en begäran innan du tar emot något svar; i Microsoft tal Service-protokollet, du får svar *medan du fortfarande direktuppspelning av ljud för begäran*.

> [!NOTE]
> Den här funktionen stöds inte när du använder tal http-REST API.

### <a name="turns"></a>Aktiverar

Taligenkänning är operatör av information. När du talar försöker du att förmedla information som finns i din ägo till någon som lyssnar efter informationen. När förmedla information turas du vanligtvis om talar och lyssnar. På samma sätt samverkar ditt tal-aktiverade program med användare genom att alternativt lyssna och svara, även om ditt program vanligtvis har de flesta av de lyssnar. Talade användaren och tjänsten svaret på den här indata kallas en *aktivera*. En *aktivera* startar när användaren talar och slutar när programmet har slutförts hantering av tal service svaret.

### <a name="telemetry"></a>Telemetri

Skapa en tal-aktiverad enhet eller ett program kan vara en utmaning, även för erfarna utvecklare. Stream-baserade protokoll verka ofta överväldigande vid en första titt och viktig information som identifiering av tystnad kan vara helt nya. Med så många meddelanden som ska vara har skickats och tagits emot att slutföra en enskild begäran och svar-par, är det *mycket* viktigt att samla in fullständig och korrekt information om dessa meddelanden. Microsoft tal Service-protokollet ger för insamling av data. Du bör göra allt för att ange nödvändiga data så noggrant som möjligt. genom att tillhandahålla fullständig och korrekt data du kommer att hjälpa dig själv--om du skulle behöva hjälp från Microsoft tal Service-teamet felsöka klientimplementeringen av, kvaliteten på telemetridata som du har samlat in kommer att vara viktiga för problem analys.

> [!NOTE]
> Den här funktionen stöds inte när du använder taligenkänning REST API.

### <a name="speech-application-states"></a>Tal programtillstånd

Stegen som du utför för att aktivera talindata i ditt program är lite annorlunda än stegen för andra former av indata som musklickningar eller fingeravtrycksläsare trycker. Du måste hålla reda på när ditt program lyssnar på mikrofonen och skicka data till tal-tjänsten när den väntar på svar från tjänsten och när den är i viloläge. Relationen mellan dessa tillstånd visas i diagrammet nedan.

![Tal programtillstånd Diagram](Images/speech-application-state-diagram.png)

Eftersom tjänsten Microsoft tal deltar i vissa av tillstånd, definierar service-protokollet meddelanden att programmet övergången emellan. Programmet måste tolka och agera på dessa protokollmeddelanden att spåra och hantera programtillstånd tal.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Med hjälp av tjänsten speech recognition från dina appar

Microsoft speech recognition service ger dig två sätt för utvecklare att lägga till tal i sina appar.

- [REST API: erna](GetStarted/GetStartedREST.md): utvecklare kan använda HTTP-anrop från sina appar till tjänsten för taligenkänning.
- [Klientbibliotek](GetStarted/GetStartedClientLibraries.md): för avancerade funktioner utvecklare kan hämta Microsoft Speech klientbibliotek och länka till sina appar.  Klientbiblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med hjälp av olika språk (C#, Java, JavaScript, ObjectiveC).

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klientbibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera en kort tal, till exempel kommandon (ljud längd < 15 s) utan mellanliggande resultat | Ja | Ja |
| Konvertera en lång ljud (> 15 s) | Nej | Ja |
| Dataströmmen ljud med mellanliggande resultat som önskas | Nej | Ja |
| Förstå texten konverteras från ljud med THOMAS | Nej | Ja |

 Om din plattform eller språk ännu inte har en SDK, kan du skapa egna implementering baserat på de [protokollet dokumentationen](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Recognition lägen

Det finns tre lägen för igenkänning: `interactive`, `conversation`, och `dictation`. Läge för igenkänning justerar taligenkänning baserat på hur användarna sannolikt att tala. Välj lämplig recognition läge för ditt program.

> [!NOTE]
> Recognition lägen kan ha olika beteenden i den [REST-protokollet](#rest-speech-recognition-api) än i den [WebSocket-protokollet](#webSocket-speech-recognition-api). REST API stöder till exempel inte kontinuerlig recognition även i konversationen eller dictation läge.
> [!NOTE]
> Dessa lägen kan användas när du använder direkt REST- eller WebSocket-protokollet. Den [klientbibliotek](GetStarted/GetStartedClientLibraries.md) använda olika parametrar för att ange recognition läge. Mer information finns i klientbiblioteket önskat.

Tjänsten Microsoft tal returnerar endast ett recognition frasen resultat för alla recognition lägen. Det finns en gräns på 15 sekunder för en enskild utterance.

### <a name="interactive-mode"></a>Interaktivt läge

I `interactive` läge, en användare gör kort förfrågningar och förväntar sig programmet för att utföra en åtgärd i svaret.

Följande egenskaper är vanliga i interaktivt läge program:

- Användarna vet de talar till en dator och inte till en annan personal.
- Programanvändare vet i förväg vad de vill säga baserat på vad de vill att programmet ska göra.
- Utterances senaste vanligtvis om 2-3 sekunder.

### <a name="conversation-mode"></a>Konversationen läge

I `conversation` läge, användare bedriver en mänskliga till mänskliga konversation.

Följande egenskaper är typiska för konversationen läge program:

- Användarna vet att de pratar till en annan person.
- Taligenkänning förbättrar mänsklig konversationer genom att låta en eller båda deltagarna talade texten.
- Användare planerar alltid inte att säga.
- Användare använder ofta slang och andra informell tal.

### <a name="dictation-mode"></a>Dikterings

I `dictation` läge kan användarna uppge längre utterances till programmet för vidare bearbetning.

Följande egenskaper är typiska för dictation läge program:

- Användarna vet att de pratar till en dator.
- Text för tal igenkänningsresultat visas för användarna.
- Användare har ofta planerar att säga och mer formella språk.
- Användare utsträckning fullständig meningar som sista 5 – 8 sekunder.

> [!NOTE]
> Tjänsten Microsoft tal returnerar inte ofullständiga resultat i lägena dictation och konversation. I stället returnerar tjänsten stabil frasen resultat efter tystnad gränser i ljudströmmen. Microsoft kan förbättra tal-protokollet för att förbättra användarupplevelsen i dessa kontinuerlig recognition lägen.

## <a name="recognition-languages"></a>Språk

Den *språk* anger språket som talar om dina programanvändare. Ange den *språk* med den *språk* Frågeparametern för URL: en för anslutningen. Värdet för den *språk* frågan parametern använder taggen IETF språk [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), och **måste** vara något av de språk som stöds av taligenkänning API. En fullständig lista över språk som stöds av tjänsten tal kan hittas på sidan [språk som stöds av](API-Reference-REST/supportedlanguages.md).

Tjänsten Microsoft tal avvisar ogiltig anslutningsförfrågningar genom att visa en `HTTP 400 Bad Request` svar. En ogiltig begäran är en som:

- Innehåller inte en *språk* fråga parametervärdet.
- Innehåller en *språk* Frågeparametern som är felaktigt formaterad.
- Innehåller en *språk* Frågeparametern som inte är något av språk som stöd.

Du kan välja att skapa ett program som har stöd för en eller flera av de språk som stöds av tjänsten.

### <a name="example"></a>Exempel

I följande exempel visas ett program som använder *konversation* speech recognition läge för föredragshållare amerikansk engelska.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Skrivfel svar

Skrivfel svar tillbaka den konvertera texten från ljud till klienter. Ett skrivfel svar innehåller följande fält:

- `RecognitionStatus` Anger status för redovisning. Möjliga värden anges i tabellen nedan.

| Status | Beskrivning |
| ------------- | ---------------- |
| Lyckades | Redovisning har upprättats och visningstext fältet finns |
| NoMatch | Tal påträffades i ljudströmmen, men inga ord från målspråket matchade. Se [NoMatch Recognition Status(#nomatch-recognition-status) mer information  |
| InitialSilenceTimeout | Början av ljudströmmen finns endast tystnad och tidsgränsen för tal-tjänsten |
| BabbleTimeout | Början av ljudströmmen finns endast brus och tidsgränsen för tal-tjänsten |
| Fel | Recognition tjänsten påträffade ett internt fel och kan inte fortsätta |

- `DisplayText` representerar frasen efter versaler, skiljetecken och inversen-text-normalisering har använts och svordomar har tagits maskeras med asterisker. Visningstext fältet finns *endast* om den `RecognitionStatus` fältet har värdet `Success`.

- `Offset` Anger förskjutningen (i 100 nanosekunder enheter) som frasen identifierades i förhållande till början av ljudströmmen.

- `Duration`Anger tiden (i 100 nanosekunder enheter) för frasen tal.

Ett skrivfel svar returnerar mer information om så önskas. Se [utdataformat](#output-format) att returnera mer detaljerade utdata.

Microsoft tal tjänsten stöder ytterligare skrivfel processen med att lägga till versaler och skiljetecken, maskering svordomar och Normalisera text till vanliga formulär. Till exempel om en användare talar en fras som representeras av orden ”Påminn mig att köpa sex iPhone”, Microsoft tal Services returneras transcribed texten ”Påminn mig att köpa 6 iPhone”. Processen som konverterar ordet ”sex” med värdet ”6” kallas *inverterade Text normalisering* (*ITN* för kort).

### <a name="nomatch-recognition-status"></a>NoMatch recognition status

Skrivfel svaret returnerar `NoMatch` i `RecognitionStatus` när tjänsten Microsoft tal identifierar tal i ljudströmmen men gick inte att matcha det tal med grammatiken språk som används för begäran. Till exempel en *NoMatch* kan inträffa om en användare säger någonting i tyska när tolken förväntar amerikansk engelska som språk för talade. Wave-mönstret för utterance skulle indikera förekomst av tal, men ingen av de ord talas matchar amerikansk engelska lexikonet som används av tolken.

En annan *NoMatch* tillståndet inträffar när recognition algoritmen är gick inte att hitta en exakt matchning för ljud som ingår i ljudströmmen. När det här tillståndet inträffar tjänsten Microsoft tal kan ge *speech.hypothesis* meddelanden som innehåller *hypotetiska text* men genererar en *speech.phrase*meddelande där den *RecognitionStatus* är *NoMatch*. Det här villkoret är normalt; Du måste inte göra några antaganden om noggrannhet eller återgivning av texten i den *speech.hypothesis* meddelande. Dessutom kan du måste inte anta att eftersom tjänsten Microsoft tal genererar *speech.hypothesis* meddelanden som tjänsten är kan skapa en *speech.phrase* message med  *RecognitionStatus* *lyckade*.

## <a name="output-format"></a>Utdataformat

Microsoft tal Service kan returnera olika nyttolast format i skrivfel svar. Alla nyttolaster är JSON-strukturer.

Du kan styra resultatformatet fras genom att ange den `format` Frågeparametern för URL: en. Som standard returnerar tjänsten `simple` resultat.

| Format | Beskrivning |
|-----|-----|
| `simple` | En förenklad frasen resultat som innehåller recognition status och den tolkade texten i formuläret visas. |
| `detailed` | En recognition status och N bäst resultatlista frasen där varje fras resultat innehåller alla fyra recognition formulär och en förtroende poäng. |

Den `detailed` format innehåller [N bäst värden](#n-best-values), förutom `RecognitionStatus`, `Offset`, och `duration`, i svaret.

### <a name="n-best-values"></a>N bäst värden

Lyssnare om människor eller datorer, kan aldrig vara säker på att de hört *exakt* vad var talas. En lyssnare kan tilldela en *sannolikhet* endast för en viss tolkning av en utterance. 

Under normala förhållanden, när du talar till andra som de ofta samverka, har personer en hög sannolikhet för igenkänning ord som har talas. Datorbaserat tal lyssnare strävar efter att uppnå liknande noggrannhet och villkor rätt [de uppnå paritet med människor](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmer som används i Taligenkänning utforska alternativa tolkningar av en utterance som en del av normala bearbetningen. Vanligtvis ignoreras dessa alternativ som bevis för en enkel tolkning blir överväldigande. Mindre än optimala förhållanden dock slutförs taligenkänningen med en lista över alternativa möjliga tolkningar. Upp *N* alternativ i den här listan kallas den *N bäst listan*. Varje alternativ har tilldelats en [förtroende poäng](#confidence). Förtroende poäng intervallet från 0 till 1. Ett resultat på 1 representerar den högsta nivån av förtroende. Ett resultat på 0 representerar den lägsta nivån av förtroende.

> [!NOTE]
> Antalet poster i listan N bäst varierar mycket mellan flera utterances. Antalet poster kan variera över flera erkännanden av den *samma* utterance. Den här variationen är en fysisk och förväntade resultatet av probabilistic uppbyggnad speech recognition algoritmen.

Varje post som returneras i listan N bäst innehåller

- `Confidence`, vilket motsvarar den [förtroende poäng](#confidence) för den här posten.
- `Lexical`, vilket är den [lexikala formuläret](#lexical-form) tolkade text.
- `ITN`, vilket är den [ITN formuläret](#itn-form) tolkade text.
- `MaskedITN`, vilket är den [maskeras ITN formuläret](#masked-itn-form) tolkade text.
- `Display`, vilket är den [Visa formulär](#display-form) tolkade text.

### Förtroende-resultat <a id="confidence"></a>

Förtroende poängresultat är väsentlig speech recognition system. Hämtar Microsoft tal tjänsten förtroende resultat från en *förtroende, klassificerare*. Microsoft tränar förtroende klassificeraren över en mängd funktioner som är utformade för att högst skilja mellan korrekta och felaktiga igenkänning. Förtroende poäng utvärderas för enstaka ord och hela utterances.

Om du väljer att använda förtroende resultat som returnerats av tjänsten vara medveten om följande:

- Förtroende resultat kan jämföras endast inom samma recognition läge och språk. Jämför inte resultat mellan olika språk eller annan recognition lägen. Till exempel en förtroende poäng i interaktiva recognition läge har *inga* korrelation till ett förtroende poäng i läget dictation.
- Förtroende resultat är bäst för en begränsad uppsättning utterances. Det finns en bra grad av variationer i poängen för en stor mängd utterances naturligt.

Om du väljer att använda ett förtroende score-värde som en *tröskelvärdet* på som ditt program fungerar, använder du taligenkänning för att upprätta tröskelvärdena.

- Köra taligenkänning på ett representativt urval av utterances för ditt program.
- Samla in förtroende poängen för varje i exempel-uppsättningen.
- Basera din tröskelvärdet på vissa percentil förtroende provet.

Ingen enskild tröskelvärdet är lämplig för alla program. En acceptabel förtroende poäng för ett program kanske är acceptabel för ett annat program.

### <a name="lexical-form"></a>Lexikaliskt formulär

Formuläret lexikala är den tolkade texten exakt hur den uppstod i utterance utan skiljetecken och små bokstäver. Till exempel lexikala form av adress ”1020 Enterprise sätt” skulle vara *tio 20 enterprise sätt*, förutsatt att den har talas sätt. Formuläret lexikala meningen ”Påminn mig att köpa 5 blyertspennor” är *Påminn mig att köpa fem blyertspennor*.

Formuläret lexikala passar bäst för program som behöver utföra standardmässiga text normalisering. Formuläret lexikala passar också för program som behöver obearbetat recognition ord.

Svordomar maskeras aldrig i formuläret lexikala.

### <a name="itn-form"></a>ITN formulär

Text normalisering är processen konvertera text från en form till en annan ”kanoniska” form. Till exempel telefonnummer ”555-1212” kan konverteras till den kanoniska formen *fem fem fem en två en två*. *Omvänd* text normalisering (ITN) ångrar processen konvertera orden ”fem fem fem en två en två” till den omvända kanoniska formen *555-1212*. ITN form av en recognition resultatet innehåller inte versaler eller skiljetecken.

Formuläret ITN passar bäst för program som fungerar på den tolkade texten. Ett program som gör att en användare ska kunna kommunicera söktermer och använder sedan dessa villkor i web query skulle till exempel använda ITN formuläret. Svordomar maskeras aldrig i formuläret ITN. Maskera svordomar genom att använda den *maskerade ITN formuläret*.

### <a name="masked-itn-form"></a>Maskerade ITN formulär

Eftersom svordomar naturligt är en del av talas identifierar tjänsten Microsoft tal sådana ord och fraser när de läses. Svordomar kanske, men inte lämplig för alla program, särskilt de programmen med en begränsad, icke-vuxen målgrupp.

Formuläret maskerade ITN gäller svordomar maskering till formuläret inverterade text normalisering. Att maskera svordomar, ange värdet för svordomar parametervärde för `masked`. När svordomar maskeras ersätts ord som identifieras som en del av det språket svordomar lexikonet med asterisker. Till exempel: *Påminn mig att köpa 5 *** blyertspennor*. Maskerade ITN form av en recognition resultatet innehåller inte versaler eller skiljetecken.

> [!NOTE]
> Om svordomar frågeparametervärdet anges till `raw`, maskerade ITN formuläret är samma som ITN formuläret. Svordomar är *inte* maskeras.

### <a name="display-form"></a>Visa formulär

Skiljetecken och skiftläge indikation på att prioritera om att pausa och så vidare, vilket gör det lättare att förstå text. Visa formuläret lägger till skiljetecken och skiftläge resultat, vilket gör det lämpligaste formuläret för program som visar talade texten.

Eftersom formuläret visas utökar maskerade ITN formuläret, kan du ställa parametervärdet svordomar `masked` eller `raw`. Om värdet anges till `raw`, igenkänning resultaten omfatta alla svordomar talas av användaren. Om värdet anges till `masked`, känns igen som en del av det språket svordomar lexikonet ersättas med asterisker.

### <a name="sample-responses"></a>Exempel svar

Alla nyttolaster är JSON-strukturer.

Nyttolastformatet av den `simple` fras resultat:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Nyttolastformatet av den `detailed` fras resultat:

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

## <a name="profanity-handling-in-speech-recognition"></a>Hantering av svordomar taligenkänning

Tjänsten Microsoft tal identifierar alla former av tal, inklusive ord och fraser som många skulle klassificera som ”svordomar”. Du kan styra hur tjänsten hanterar svordomar med hjälp av den *svordomar* Frågeparametern. Som standard tjänsten döljer svordomar i *speech.phrase* resultat och returnerar inte *speech.hypothesis* meddelanden som innehåller svordomar.

| *Svordomar* värde | Beskrivning |
| - | - |
| `masked` | Döljer svordomar med asterisker. Det här beteendet är standard. | 
| `removed` | Tar bort svordomar från alla resultat. |
| `raw` | Identifierar och returnerar svordomar i alla resultat. |

### <a name="profanity-value-masked"></a>Svordomar värde `Masked`

Att maskera svordomar genom att ange den *svordomar* Frågeparametern till värdet *maskeras*. När den *svordomar* frågeparameter har detta värde eller inte har angetts för en begäran, tjänsten *masker* svordomar. Tjänsten utför maskering genom att ersätta svordomar i resultat med asterisker. När du anger svordomar maskering hantering tjänsten inte returnerar *speech.hypothesis* meddelanden som innehåller svordomar.

### <a name="profanity-value-removed"></a>Svordomar värde `Removed`

När den *svordomar* frågan parametern har värdet *bort*, tjänsten avlägsnar svordomar från både *speech.phrase* och *speech.hypothesis* meddelanden. Resultatet är samma *som om de svordomar ord inte har talas*.

#### <a name="profanity-only-utterances"></a>Endast svordomar utterances

En användare kan tala *endast* svordomar när ett program har konfigurerat tjänsten för att ta bort svordomar. I det här scenariot recognition läge *dictation* eller *konversation*, tjänsten returnerar inte en *speech.result*. Läge för igenkänning *interaktiva*, tjänsten returnerar en *speech.result* med statuskod *NoMatch*. 

### <a name="profanity-value-raw"></a>Svordomar värde `Raw`

När den *svordomar* frågan parametern har värdet *raw*, tjänsten inte ta bort eller maskera svordomar i antingen den *speech.phrase* eller  *Speech.hypothesis* meddelanden.
