---
title: Bing Speech begrepp | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Grundläggande begrepp som används i Microsoft Speech Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 08790d73dd4fd182d8129c755a291fd99b6136a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954117"
---
# <a name="basic-concepts"></a>Grundläggande begrepp

Den här sidan beskriver några grundläggande begrepp i Microsoft speech igenkänning av tjänsten. Vi rekommenderar att du kan läsa den här sidan innan du använder Microsoft API för taligenkänning i ditt program.

## <a name="understanding-speech-recognition"></a>Förstå taligenkänning

Om det här är första gången du skapar ett tal-aktiverat program, eller om det är första gången du lägger till talfunktioner för till ett befintligt program, hjälper i det här avsnittet dig att komma igång. Om du redan har viss erfarenhet med program kan du bara skumma igenom det här avsnittet eller du kan hoppa över det helt och hållet om du är en gammal hand på tal och du vill hämta rätt protokoll detaljinformation.

### <a name="audio-streams"></a>Ljudströmmar

Främsta bland de grundläggande principerna för tal är den *ljudström*. Till skillnad från en tangenttryckning som uppstår på en enda punkt i tiden och innehåller en enda typ av information, en talat begäran är utspridd på hundratals millisekunder och innehåller många kilobyte information. Varaktigheten för talat yttranden anger vissa problem för utvecklare som vill tillhandahålla en smidig och elegant tal-upplevelse för sina program. Dagens datorer och algoritmer utför du taltranskription i cirka hälften av varaktigheten för uttryck, så att ett uttryck i 2 sekunder kan vara transkriberas i ungefär 1 sekund, men alla program som inträffar i en 1 sekund fördröjning av bearbetningen användaren är varken effektiviserad eller elegant.

Som tur är kan det finns sätt att ”dölja” avskrift tid genom att utföra avskrift på en del av uttryck medan användaren pratar en annan del. Är till exempel genom att dela upp en 1 sekund uttryck i 10 segment på 100 millisekunder och genom att utföra avskrift på varje segment i sin tur över 450 totala 500 millisekunder som krävs för avskrift kan vara ”dolda” så att användaren är inte medveten om avskrift som utförs när han talar. När tänker på hur det här exemplet kan du komma ihåg att tjänsten fungerar avskrift på föregående 100 millisekunder för ljud medan användaren pratar nästa 100, så när användaren slutar sett tjänsten behöver bara transkribera ungefär 100 millisekunder för ljud för att producera ett resultat.

För att uppnå den här användarupplevelse, talat ljud information samlas in i segment och transkriberas som användaren upp. Dessa ljud segment sammantaget från den *ljudström*, och skicka dessa ljud segment till tjänsten kallas *strömning av ljud.* Direktuppspelning av ljud är en viktig del av något tal-aktiverade program. Anpassa segmentstorleken och optimera strömning implementeringen är några av mest kraftfulla metoder för att förbättra användarupplevelsen för ditt program.

### <a name="microphones"></a>Mikrofoner

Personer bearbeta talat ljud med hjälp av deras öronen, men rörelse i orörliga maskinvara använder mikrofoner. Om du vill aktivera tal i alla program, måste du integrera med mikrofonen att tillhandahålla ljudströmmen för ditt program.

API: er för din mikrofon måste kan du starta och stoppa mottagningen för ljud från mikrofonen. Du måste du bestämma vad användaråtgärder utlöser mikrofonen påbörja avlyssning för tal. Du kan välja att låta en knapptryckning utlösa början av lyssnar eller du kan välja att låta en *nyckel ord* eller *wake word* spotter lyssnar alltid mikrofonen och kan använda utdata från modulen till Utlös skicka ljud till tjänsten Microsoft tal.

### <a name="end-of-speech"></a>Slutet av tal

Identifiera *när* föredragshållare har *stoppats* talar verkar tillräckligt enkla för människor men är utanför laboratoriemiljön i stället svåra problem. Det räcker inte att helt enkelt leta efter ren tystnad efter ett uttryck eftersom det är ofta mycket omgivande ljuden att göra det svårare att saker. Microsoft Speech-tjänsten har ett tydligt sätt att snabbt identifiera när en användare har stoppats sett och tjänsten kan informera din tillämpning av detta, men den här ordningen innebär att ditt program är sist veta när användaren slutar talar. Här är på alla andra former av indata där ditt program är den *första* veta när startar användarens indata *och* slutar.

### <a name="asynchronous-service-responses"></a>Asynkrona service svar

Det faktum att programmet behöver för att få information om när användarindata är klar inte införa några prestandaförsämringar eller programming problem i programmet, men det kräver att du tycker om talade förfrågningar annorlunda än indataförfrågan / mönster för svar som du har kunskaper om. Eftersom du inte känner till ditt program när användaren slutar talar, måste ditt program fortsätta att strömma ljud till tjänsten under samtidigt och asynkront väntan på svar från tjänsten. Det här mönstret är till skillnad från andra begäran/svar-web-protokoll som HTTP. I dessa protokoll, måste du slutföra en begäran innan du tar emot svar; i Microsoft Speech Service-protokollet, du får svar *medan du fortfarande direktuppspelning av ljud för begäran*.

> [!NOTE]
> Den här funktionen stöds inte när du använder tal HTTP REST API.

### <a name="turns"></a>Aktiverar

Tal är en operatör av information. När du talar försöker du förmedla information som är i din ägo till någon som lyssnar efter den här informationen. Förmedla information också du vanligtvis stänga när talar och lyssnar. På samma sätt kan interagerar programmet talbaserade med användarna genom alternativt lyssna och svara, även om ditt program vanligtvis sker det mesta av den lyssnar. Talat användaren och tjänstsvaret på den här indata kallas en *aktivera*. En *aktivera* startar när du talar och slutar när ditt program har slutförts hantering av tjänstsvaret tal.

### <a name="telemetry"></a>Telemetri

Skapa en tal-aktiverad enhet eller ett program kan vara en utmaning, även för erfarna utvecklare. Stream-baserade protokoll verka ofta överväldigande vid en första titt och viktig information som identifiering av tystnad kan vara helt nya. Med så många meddelanden behöva har skickats och tagits emot att slutföra en enskild begäran/svar-par, är det *mycket* viktigt att samla in fullständig och korrekt information om dessa meddelanden. Microsoft Speech Service-protokollet ger för insamling av data. Du bör göra allt för att ange nödvändiga data korrekt som möjligt. genom att tillhandahålla fullständig och korrekt data, du kommer att hjälpa dig själv – om du skulle behöva hjälp från Microsoft Speech Service-teamet felsöka klientimplementeringen av, kommer kvaliteten på telemetridata som du har samlat in vara viktig för problem analys.

> [!NOTE]
> Den här funktionen stöds inte när du använder REST API för taligenkänning.

### <a name="speech-application-states"></a>Tal programtillstånd

Vilka steg du utför för att aktivera taligenkänning indata i ditt program är lite annorlunda än stegen för andra former av indata som musen trycker finger. Du måste hålla reda på när programmet lyssnar på mikrofonen och skicka data till speech-tjänsten när den väntar på svar från tjänsten och när den är inaktiv. Relationen mellan dessa tillstånd visas i diagrammet nedan.

![Tal programtillstånd Diagram](Images/speech-application-state-diagram.png)

Eftersom Microsoft Speech Service deltar i vissa av tillstånd, definierar protokollet som tjänsten meddelanden som hjälper program övergången emellan. Programmet behöver för att tolka och agera på dessa protokollmeddelanden att spåra och hantera programtillstånd tal.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Med hjälp av tjänsten för taligenkänning från dina appar

Microsoft speech igenkänning av tjänsten ger dig två sätt för utvecklare att lägga till tal i sina appar.

- [REST API: er](GetStarted/GetStartedREST.md): utvecklare kan använda HTTP-anrop från sina appar till tjänsten för taligenkänning.
- [Klientbibliotek](GetStarted/GetStartedClientLibraries.md): för avancerade funktioner, utvecklare kan hämta Microsoft Speech klientbibliotek och länka till sina appar.  Klientbiblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med olika språk (C#, Java, JavaScript, ObjectiveC).

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klientbibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera en kort sagt talat ljud till exempel kommandon (ljud längd < 15 s) utan mellanliggande resultat | Ja | Ja |
| Omvandla en lång (> 15 s) | Nej | Ja |
| Stream ljud med mellanliggande resultat som önskas | Nej | Ja |
| Förstå den text som konverterats från ljud med LUIS | Nej | Ja |

 Om ditt språk eller plattform ännu inte har en SDK, kan du skapa en egen implementering baserat på den [protokollet dokumentation](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Igenkänning av lägen

Det finns tre lägen för taligenkänning: `interactive`, `conversation`, och `dictation`. Igenkänning av läge justerar taligenkänning baserat på hur användarna är sannolikt att tala. Välj lämplig erkännande läge för ditt program.

> [!NOTE]
> Igenkänning av lägen kan ha olika beteenden i den [REST-protokoll](#rest-speech-recognition-api) än i den [WebSocket-protokoll](#webSocket-speech-recognition-api). REST API stöder till exempel inte kontinuerlig erkännande, även i konversationen eller diktering läge.
> [!NOTE]
> Dessa lägen gäller när du använder REST- eller WebSocket-protokollet direkt. Den [klientbibliotek](GetStarted/GetStartedClientLibraries.md) använda olika parametrar för att ange erkännande läge. Mer information finns i klientbiblioteket för ditt val.

Microsoft Speech Service returnerar endast en fras igenkänningsresultatet för alla erkännande lägen. Det finns en gräns på 15 sekunder för en enda uttryck.

### <a name="interactive-mode"></a>Interaktivt läge

I `interactive` läge, en användare gör kort begäranden och förväntar sig programmet att utföra en åtgärd som svar.

Följande egenskaper är typiska för interaktivt läge program:

- Användarna vet de talar till en dator och inte till en annan personal.
- Programanvändare redan i förväg vet vad de vill säga baserat på vad de vill att programmet utför.
- Vanligtvis räcker yttranden om 2-3 sekunder.

### <a name="conversation-mode"></a>Konversationen läge

I `conversation` läge, användare bedriver en mänskliga människor konversation.

Följande egenskaper är typiska för konversationen läge program:

- Användarna vet att de pratar till en annan person.
- Taligenkänning förbättrar mänskliga konversationer genom att låta en eller båda deltagarna ser talade texten.
- Användare planerar alltid inte att säga.
- Användare har ofta använder för att hitta slang och andra informell tal.

### <a name="dictation-mode"></a>Dikteringsläge

I `dictation` läge, användare kunna räkna upp längre yttranden till programmet för vidare bearbetning.

Följande egenskaper är typiska för diktering läge program:

- Användarna vet att de pratar till en dator.
- Text för tal igenkänningsresultat visas för användarna.
- Användare har ofta planerar vad de vill säga och mer formella språk.
- Användare utsträckning fullständiga meningar som senaste 5 – 8 sekunder.

> [!NOTE]
> Microsoft Speech Service returnerar inte ofullständiga resultat i lägena diktering och konversationen. I stället returnerar tjänsten stabil frasen resultaten efter tystnad gränser i ljudströmmen. Microsoft kan förbättra tal-protokollet för att förbättra användarupplevelsen i dessa lägen för kontinuerlig erkännande.

## <a name="recognition-languages"></a>Igenkänning av språk

Den *språk för* anger det språk som dina programanvändare talar. Ange den *språk för* med den *språk* URL: en frågeparameter för anslutningen. Värdet för den *språk* fråga parametern använder IETF språktaggen [BCP-47](https://en.wikipedia.org/wiki/IETF_language_tag), och **måste** vara något av de språk som stöds av API för taligenkänning. Den fullständiga listan över språk som stöds av Speech-tjänsten finns på sidan [språk som stöds av](API-Reference-REST/supportedlanguages.md).

Microsoft Speech Service avvisar ogiltig anslutningsbegäranden genom att visa en `HTTP 400 Bad Request` svar. En ogiltig begäran är en som:

- Innehåller inte en *språk* fråga om parametervärde.
- Innehåller en *språk* frågeparameter som är felaktigt formaterad.
- Innehåller en *språk* frågeparameter som inte är något av språk som stöd.

Du kan välja att skapa ett program som stöder en eller alla språk som stöds av tjänsten.

### <a name="example"></a>Exempel

I följande exempel visas ett program använder *konversationen* tal erkännande läge för föredragshållare amerikansk engelska.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Transkription svar

Svaren avskrift returnera den konvertera texten från ljud till klienter. En avskrift svaret innehåller följande fält:

- `RecognitionStatus` Anger status för erkännande. De möjliga värdena anges i tabellen nedan.

| Status | Beskrivning |
| ------------- | ---------------- |
| Lyckades | Erkännande lyckades och fältet text finns |
| NoMatch | Tal påträffades i ljudströmmen, men inga ord från målspråket som kunde matchas. Se [NoMatch erkännande Status(#nomatch-recognition-status) för mer information  |
| InitialSilenceTimeout | Början av ljudströmmen ingår endast tystnad och tjänsten tidsgränsen för tal |
| BabbleTimeout | Början av ljudströmmen ingår endast bruset och tjänsten tidsgränsen för tal |
| Fel | Igenkänning av tjänsten påträffade ett internt fel och kunde inte fortsätta |

- `DisplayText` representerar frasen när versaler, interpunktion och inverterade-text-normalisering har tillämpats och svordomar har varit maskeras med en asterisk. Visa text fältet finns *endast* om den `RecognitionStatus` fältet har värdet `Success`.

- `Offset` Anger förskjutningen (i 100 nanosekunder enheter) som frasen identifierades i förhållande till början av ljudströmmen.

- `Duration`Anger tiden (i 100 nanosekunder enheter) för tal frasen.

Svaret avskrift returnerar mer information om så önskas. Se [utdataformat](#output-format) att returnera mer detaljerad utdata.

Microsoft Speech Service stöder ytterligare avskrift processen med att lägga till versaler och skiljetecken, maskera svordomar och normaliserar text till vanliga formulär. Till exempel om en användare talar en fras som representeras av orden ”Påminn mig att köpa sex iPhone”, Microsofts Speech Services returnerar den transkriberade texten ”Påminn mig att köpa 6 iPhone”. Hur du konverterar ordet ”sex” till tal ”6” kallas *inverterade Text normalisering* (*ITN* för kort).

### <a name="nomatch-recognition-status"></a>NoMatch erkännandestatus

Transkription svaret returnerar `NoMatch` i `RecognitionStatus` när Microsoft Speech Service identifierar tal i ljudströmmen men kan inte matcha det tal till grammatik språk som används för begäran. Till exempel en *NoMatch* kan inträffa om en användare säger något på tyska när Igenkännande förväntar engelska (USA) som talat språk. Wave mönstret för uttryck skulle indikera förekomst av tal, men ingen av de ord som sägs matchar den amerikansk engelska lexikon används reguljära.

En annan *NoMatch* tillståndet inträffar när igenkänning av algoritmen kan inte hitta en exakt matchning för ljud som ingår i ljudströmmen. Om det här tillståndet inträffar kan Microsoft Speech Service kan ge *speech.hypothesis* meddelanden som innehåller *hypotetiska text* men skapas en *speech.phrase*meddelande där den *RecognitionStatus* är *NoMatch*. Det här villkoret är normalt; Du måste inte göra några antaganden om noggrannhet eller kvalitet på texten i den *speech.hypothesis* meddelande. Dessutom du måste inte förutsätts att eftersom Microsoft Speech Service ger *speech.hypothesis* meddelanden som tjänsten är producera en *speech.phrase* med  *RecognitionStatus* *lyckades*.

## <a name="output-format"></a>Utdataformat

Microsoft Speech Service kan returnera olika nyttolastformat i avskrift svar. Alla nyttolaster är JSON-strukturer.

Du kan styra resultatformatet frasen genom att ange den `format` URL: en frågeparameter. Som standard tjänsten returnerar `simple` resultat.

| Format | Beskrivning |
|-----|-----|
| `simple` | En förenklad frasen resultat som innehåller igenkänning av status och den tolkade texten i Visningsformulär. |
| `detailed` | Ett erkännandestatus och N-best resultatlista frasen där varje frasen resultatet innehåller alla fyra igenkänning av formulär och ett förtroenderesultat. |

Den `detailed` formatet innehåller [N-best värden](#n-best-values), förutom `RecognitionStatus`, `Offset`, och `duration`, i svaret.

### <a name="n-best-values"></a>N-best-värden

Lyssnare, om maskin- eller, kan aldrig vara säker på att de hört *exakt* vad var sägs. En lyssnare kan tilldela en *sannolikheten* endast för en viss tolkning av ett uttryck. 

Under normala förhållanden, när du talar till andra som de ofta interagerar, har personer en hög sannolikhet för att identifiera de ord som har talat. Datorbaserade tal lyssnare strävar efter att uppnå liknande Precision och villkor rätt [de uppnå paritet med människor](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmer som används för taligenkänning utforska alternativ tolkningar av ett uttryck som en del av den normala bearbetningen. Vanligtvis ignoreras dessa alternativ som information och ersatts med en enkel tolkning blir överväldigande. I mindre än optimala förhållanden, men taligenkänningens är klar med en lista över alternativ möjliga tolkningar. Upp *N* alternativ i den här listan kallas den *N-best lista*. Varje alternativ har tilldelats en [förtroendepoäng](#confidence). Förtroende poäng mellan 0 och 1. Ett resultat på 1 representerar den högsta nivån av förtroende. Ett resultat på 0 representerar den lägsta nivån tillförlitlighet.

> [!NOTE]
> Antalet poster i listan med N-best varierar mycket mellan flera yttranden. Antalet poster kan variera över flera igenkänningar av den *samma* uttryck. Detta är en naturlig och förväntade resultatet av avsnittet om sannolikhetsbunden natur tal igenkänning av algoritmen.

Varje post som returneras i N-best-listan innehåller

- `Confidence`, vilket motsvarar den [förtroende poäng](#confidence) för den här posten.
- `Lexical`, vilket är den [lexikal formuläret](#lexical-form) av den tolkade texten.
- `ITN`, vilket är den [ITN formuläret](#itn-form) av den tolkade texten.
- `MaskedITN`, vilket är den [maskeras ITN formuläret](#masked-itn-form) av den tolkade texten.
- `Display`, vilket är den [Visningsformulär](#display-form) av den tolkade texten.

### Förtroende-poäng <a id="confidence"></a>

Förtroende är väsentlig tal igenkänning av system. Microsoft Speech Service hämtar förtroende resultat från en *förtroende klassificerare*. Microsoft träna förtroende klassificeraren över en uppsättning funktioner som är utformade för att skilja högst rätt och fel. Förtroende poäng utvärderas för enskilda ord och hela yttranden.

Om du väljer att använda säker poängen som returnerats av tjänsten, Tänk på följande:

- Förtroende poäng kan jämföras endast inom samma erkännande läge och språk. Jämför inte resultat mellan olika språk eller annan erkännande lägen. Ett förtroenderesultat i interaktiva erkännande läge har exempelvis *inga* korrelation med ett förtroenderesultat i dikteringsläge.
- Förtroende poäng är bäst på en begränsad uppsättning yttranden. Det är naturligt en bra grad av variationer i poängen för en stor mängd yttranden.

Om du väljer att använda ett förtroende poäng värde som en *tröskelvärdet* på som ditt program fungerar, använder du taligenkänning för att upprätta tröskelvärdena.

- Köra taligenkänning på ett representativt urval av yttranden för ditt program.
- Samla in förtroende poängen för varje taligenkänning i exemplet uppsättningen.
- Basera din tröskelvärdet på vissa: e percentilen för förtroende för det exemplet.

Ingen enskild tröskelvärdet är lämplig för alla program. En godtagbar förtroendepoäng för ett program kan vara oacceptabla för ett annat program.

### <a name="lexical-form"></a>lexikal formulär

Formuläret lexikal är den tolkade texten exakt hur det skedde i uttryck och utan interpunktion och små bokstäver. Till exempel lexikal form av adress ”1020 Enterprise sätt” skulle vara *tio tjugo enterprise sätt*, förutsatt att den har talat sätt. Lexikal form av meningen ”Påminn mig att köpa 5 pennor” är *Påminn mig att köpa fem pennor*.

Formuläret lexikal passar bäst för program som behöver utföra andra portar än standardtext normalisering. Formuläret lexikal passar också för program som behöver obearbetade igenkänning av orden.

Svordomar maskeras aldrig i formuläret lexikal.

### <a name="itn-form"></a>ITN formulär

Text normalisering är att konvertera text från ett formulär till en annan ”canonical” form. Till exempel telefonnummer ”555-1212” kan konverteras till den kanoniska formen *fem fem fem en två en två*. *Inverterade* text normalisering (ITN) kastar den här processen, konvertering av orden ”fem fem fem en två en två” till den omvända kanoniska formen *555-1212*. ITN form av en igenkänningsresultatet omfattar inte skiljetecken eller versaler.

Formuläret ITN passar bäst för program som fungerar på den tolkade texten. Ett program som låter en användare att tala söktermer och använder sedan dessa villkor på en webbfråga skulle till exempel använda ITN formuläret. Svordomar maskeras aldrig i formuläret ITN. Använd för att maskera svordomar den *maskerade ITN formuläret*.

### <a name="masked-itn-form"></a>Maskerade ITN formulär

Eftersom svordomar är naturligt en del av talat språk, identifierar Microsoft Speech Service sådana ord och fraser när de läses. Svordomar kanske, men inte lämplig för alla program, särskilt program med en begränsad, icke-vuxen målgrupp.

Formuläret maskerade ITN gäller svordomar maskera för formuläret normalisering inverterade text. Att maskera svordomar, ange värdet för det svordomar parametervärdet `masked`. När svordomar maskeras ersättas orden som känns igen som en del av det språket svordomar lexikon med en asterisk. Till exempel: *Påminn mig att köpa 5 *** pennor*. Maskerade ITN form av en igenkänningsresultatet omfattar inte skiljetecken eller versaler.

> [!NOTE]
> Om frågeparametervärdet för svordomar anges till `raw`, formuläret maskerade ITN är samma som formuläret ITN. Svordomar är *inte* maskeras.

### <a name="display-form"></a>Visa formulär

Skiljetecken och skiftläge signalera var du prioritera var du vill pausa och så vidare, vilket gör det lättare att förstå texten. Visa formulär lägger till interpunktion och skiftläge resultat, vilket gör det till den lämpligaste formen för program som visar talade texten.

Eftersom Visa formulär utökar formuläret maskerade ITN, du kan ange värdet för parametern svordomar `masked` eller `raw`. Om värdet anges till `raw`, igenkänning av resultatet innehålla några svordomar talas av användaren. Om värdet anges till `masked`, känns igen som en del av det språket svordomar lexikon ersättas med en asterisk.

### <a name="sample-responses"></a>Exempel-svar

Alla nyttolaster är JSON-strukturer.

Nyttolastformatet för den `simple` fras resultat:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Nyttolastformatet för den `detailed` fras resultat:

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

## <a name="profanity-handling-in-speech-recognition"></a>Svordomar-hantering för taligenkänning

Microsoft Speech Service kan du identifiera alla former av mänskliga tal, inklusive ord och fraser som många skulle klassificera som ”svordomar”. Du kan styra hur tjänsten hanterar olämpligt språk med hjälp av den *svordomar* frågeparameter. Som standard tjänsten döljer svordomar i *speech.phrase* resulterar och returnerar inte *speech.hypothesis* meddelanden som innehåller olämpligt språk.

| *Svordomar* värde | Beskrivning |
| - | - |
| `masked` | Döljer olämpligt språk med en asterisk. Det här beteendet är standard. | 
| `removed` | Tar bort svordomar från alla resultat. |
| `raw` | Identifierar och returnerar svordomar i alla resultat. |

### <a name="profanity-value-masked"></a>Svordomar värde `Masked`

Att maskera svordomar genom att ange den *svordomar* frågeparameter till värdet *maskeras*. När den *svordomar* frågeparameter har detta värde eller inte har angetts för en begäran, tjänsten *masker* olämpligt språk. Tjänsten utför maskning genom att ersätta svordomar i igenkänning av resultaten med en asterisk. När du anger svordomar maskning hantering av tjänsten returnerar inte *speech.hypothesis* meddelanden som innehåller olämpligt språk.

### <a name="profanity-value-removed"></a>Svordomar värde `Removed`

När den *svordomar* fråga parametern har värdet *bort*, tjänsten avlägsnar svordomar från både *speech.phrase* och *speech.hypothesis* meddelanden. Resultatet är samma *som om svordomar ord inte har sägs*.

#### <a name="profanity-only-utterances"></a>Endast svordomar yttranden

En användare kan tala *endast* svordomar när ett program har konfigurerade tjänsten för att ta bort olämpligt språk. Det här scenariot om erkännande läget är *diktering* eller *konversationen*, tjänsten returnerar inte en *speech.result*. Om läget för är *interaktiva*, tjänsten returnerar en *speech.result* med statuskod *NoMatch*. 

### <a name="profanity-value-raw"></a>Svordomar värde `Raw`

När den *svordomar* fråga parametern har värdet *raw*, tjänsten inte ta bort eller maskera svordomar antingen den *speech.phrase* eller  *Speech.hypothesis* meddelanden.
