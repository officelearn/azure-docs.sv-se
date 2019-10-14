---
title: Translator Text API Översätt metod
titleSuffix: Azure Cognitive Services
description: Använd metoden Translator Text API Översätt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: bc03e10e40e90845c8e1a3dd064c4f50fafeac00
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299821"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3,0: Översätt

Översätter text.

## <a name="request-url"></a>URL för begäran

Skicka en `POST`-begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Den version av API: t som klienten begär. Värdet måste vara <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>som</td>
    <td><em>Valfri parameter</em>.<br/>Anger språket för inmatad text. Hitta vilka språk som kan omvandlas från genom att leta upp <a href="./v3-0-languages.md">språk som stöds</a> med hjälp av <code>translation</code>-omfånget. Om parametern <code>from</code> inte anges används automatisk språk identifiering för att fastställa käll språket. <br/><br/>Du måste använda parametern <code>from</code> i stället för automatisk identifiering när du använder funktionen för <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dynamiskt ord listor</a> .</td>
  </tr>
  <tr>
    <td>till</td>
    <td><em>Obligatorisk parameter</em>.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de <a href="./v3-0-languages.md">språk som stöds</a> som ingår i <code>translation</code>-omfånget. Använd till exempel <code>to=de</code> för att översätta till tyska.<br/>Det går att översätta till flera språk samtidigt genom att upprepa parametern i frågesträngen. Använd exempelvis <code>to=de&to=it</code> för att översätta till tyska och italienska.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Valfri parameter</em>.<br/>Definierar om texten som ska översättas är oformaterad text eller HTML-text. Alla HTML måste vara ett välformulerat, fullständigt element. Möjliga värden är: <code>plain</code> (standard) eller <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Valfri parameter</em>.<br/>En sträng som anger en kategori (domän) för översättningen. Den här parametern används för att hämta översättningar från ett anpassat system som skapats med <a href="../customization.md">anpassad översättare</a>. Lägg till kategori-ID från din anpassad översättning <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projekt information</a> till den här parametern för att använda det distribuerade anpassade systemet. Standardvärdet är: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska behandlas i översättningar. Möjliga värden är: <code>NoAction</code> (standard), <code>Marked</code> eller <code>Deleted</code>. Information om hur du hanterar svordomar finns i <a href="#handle-profanity">svordoms hantering</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska markeras i översättningar. Möjliga värden är: <code>Asterisk</code> (standard) eller <code>Tag</code>. Information om hur du hanterar svordomar finns i <a href="#handle-profanity">svordoms hantering</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill ta med justerings projektion från käll texten till översatt text. Möjliga värden är: <code>true</code> eller <code>false</code> (standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill ta med menings gränser för inmatad text och översatt text. Möjliga värden är: <code>true</code> eller <code>false</code> (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Valfri parameter</em>.<br/>Anger ett återställnings språk om språket i inmatad text inte kan identifieras. Automatisk identifiering av språk används när parametern <code>from</code> utelämnas. Om identifieringen Miss lyckas, kommer <code>suggestedFrom</code>-språket att antas.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger den inmatade textens skript.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skript för den översatta texten.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Valfri parameter</em>.<br/>Anger att tjänsten kan återgå till ett allmänt system när det inte finns något anpassat system. Möjliga värden är: <code>true</code> (standard) eller <code>false</code>.<br/><br/><code>allowFallback=false</code> anger att översättningen bara ska använda system som är utbildade för den <code>category</code> som anges av begäran. Om en översättning för språk X till språk Y kräver länkning genom ett Pivot-språk E, måste alla system i kedjan (X-> E och E-> Y) vara anpassade och ha samma kategori. Om det inte finns något system med en viss kategori returnerar begäran en 400-status kod. <code>allowFallback=true</code> anger att tjänsten kan återgå till ett allmänt system när det inte finns något anpassat system.
</td>
  </tr>
</table> 

Begärandehuvuden innehåller:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Authentication-huvud (er)</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: <code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Valfritt</em>.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med en sträng egenskap med namnet `Text`, som representerar den sträng som ska översättas.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 100 element.
* Hela texten som ingår i begäran får inte överstiga 5 000 tecken inklusive blank steg.

## <a name="response-body"></a>Svars text

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `detectedLanguage`: ett objekt som beskriver det identifierade språket via följande egenskaper:

      * `language`: en sträng som representerar koden för det identifierade språket.

      * `score`: ett flyt värde som anger förtroendet i resultatet. Poängen är mellan noll och en och en låg poäng indikerar en låg exakthet.

    Egenskapen `detectedLanguage` finns bara i result-objektet när språk automatisk identifiering begärs.

  * `translations`: en matris med översättnings resultat. Storleken på matrisen matchar antalet mål språk som anges i parametern `to`. Varje element i matrisen innehåller:

    * `to`: en sträng som representerar språk koden för mål språket.

    * `text`: en sträng som ger den översatta texten.

    * `transliteration`: ett objekt som ger den översatta texten i skriptet som anges av parametern `toScript`.

      * `script`: en sträng som anger mål skriptet.   

      * `text`: en sträng som ger den översatta texten i mål skriptet.

    @No__t-0-objektet tas inte med om transkriberingsspråk inte sker.

    * `alignment`: ett objekt med en enskild sträng egenskap med namnet `proj`, som mappar inmatad text till översatt text. Justerings informationen anges bara när parametern för begäran `includeAlignment` är `true`. Justeringen returneras som ett sträng värde av följande format: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Kolonet separerar start-och slut index, strecket separerar språken och blank steg separerar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket och de justerade orden kan vara icke-sammanhängande. Om ingen justerings information är tillgänglig är justerings elementet tomt. Se [Hämta justerings information](#obtain-alignment-information) för ett exempel och begränsningar.

    * `sentLen`: ett objekt som returnerar menings gränser i indata-och utmatnings texter.

      * `srcSentLen`: en heltals mat ris som representerar längden på meningarna i indata. Matrisens längd är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`: en heltals mat ris som representerar längden på meningarna i den översatta texten. Matrisens längd är antalet meningar och värdena är längden på varje mening.

    Menings gränser inkluderas endast när parametern request `includeSentenceLength` är `true`.

  * `sourceText`: ett objekt med en enskild sträng egenskap med namnet `text`, vilket ger inmatad text i standard skriptet för käll språket. Egenskapen `sourceText` finns bara när indatamängden uttrycks i ett skript som inte är det vanliga skriptet för språket. Om indatatypen till exempel är Arabiskt skriven i latinskt skript, kommer `sourceText.text` att vara samma arabiska text som konverteras till enskriftspråk.

Exempel på JSON-svar finns i avsnittet [exempel](#examples) .

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
    <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används i fel söknings syfte.</td>
  </tr>
  <tr>
    <td>X-MT-system</td>
    <td>Anger den system typ som användes för översättning för varje to-language som begärdes för översättning. Värdet är en kommaavgränsad lista med strängar. Varje sträng anger en typ:<br/><ul><li>Anpassad-begäran innehåller ett anpassat system och minst ett anpassat system användes under översättningen.</li><li>Team – alla andra begär Anden</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar. 

<table width="100%">
  <th width="20%">Status kod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrar saknas eller är ogiltig. Korrigera parametrarna för begäran innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrol lera att autentiseringsuppgifterna har angetts och är giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran är inte auktoriserad. Se fel meddelandet information. Detta indikerar ofta att alla kostnads fria översättningar som ingår i en utvärderings prenumeration har använts.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Begäran kunde inte uppfyllas eftersom en resurs saknas. Se fel meddelandet information. När du använder en anpassad <code>category</code>, indikerar detta ofta att det anpassade översättnings systemet ännu inte är tillgängligt för att betjäna begär Anden. Begäran bör göras om efter en vänte tid (t. ex. 1 minut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet <code>X-RequestId</code> och klient-ID: n från begär ande huvudet <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet <code>X-RequestId</code> och klient-ID: n från begär ande huvudet <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran även ett JSON-felsvar. Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referens sidan för v3-Translator text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Översätt en enstaka inmatare

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Matrisen `translations` innehåller ett-element som innehåller översättningen av den enda texten i indatatypen.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Översätt en enstaka inmatning med språk automatisk identifiering

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska. Det angivna språket anges inte i begäran. Automatisk identifiering av käll språket används i stället.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Svaret liknar svaret från föregående exempel. Eftersom automatisk automatisk identifiering av språk begärdes, innehåller svaret även information om språket som har identifierats för indata. 

### <a name="translate-with-transliteration"></a>Översätt med transkriberingsspråk

Nu ska vi utöka det tidigare exemplet genom att lägga till transkriberingsspråk. Följande begäran ber om en kinesisk översättning som skrivits i det latinska skriptet.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Översättnings resultatet innehåller nu en `transliteration`-egenskap som ger den översatta texten med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätt flera delar av text

Att översätta flera strängar samtidigt är bara en fråga om att ange en sträng mat ris i begär ande texten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Översätt till flera språk

Det här exemplet visar hur du översätter samma inaktuella ingångar till flera språk i en begäran.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Hantera svordomar

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Antalet svordomar och den kontext som gör ord svordomar skiljer sig åt mellan olika kulturer, och därför kan det leda till att svordomarna på mål språket förstärks eller minskas.

Om du inte vill få några svordomar i översättningen, oavsett om det finns svordomar i käll texten, kan du använda filtrerings alternativet för svordomar. Med alternativet kan du välja om du vill se hur svordomar tas bort, om du vill markera svordomar med lämpliga taggar (vilket ger dig möjlighet att lägga till din egen efter bearbetning) eller om du inte vill ha någon åtgärd. De godkända värdena för `ProfanityAction` är `Deleted`, `Marked` och `NoAction` (standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Åtgärd</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Detta är standard beteendet. Svordomar skickas från källa till mål.<br/><br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: he är en Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Svordomar ord tas bort från utdata utan ersättning.<br/><br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: han är en.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Svordomar ord ersätts med en markör i utdata. Markören är beroende av parametern <code>ProfanityMarker</code>.<br/><br/>
För <code>ProfanityMarker=Asterisk</code>, kommer svordoms-ord att ersättas med <code>***</code>:<br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: he är en \* @ no__t-2 @ no__t-3.<br/><br/>
För <code>ProfanityMarker=Tag</code>, kommer svordoms-ord omges av XML-taggar &lt;profanity @ no__t-2 och &lt;/svordoms @ no__t-4:<br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: he är en &lt;profanity @ no__t-2jackass @ no__t-3/svordoms @ no__t-4.
  </tr>
</table> 

Exempel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```
Detta returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Jämför med:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

Den senaste begäran returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätt innehåll med markering och Bestäm vad som översätts

Det är vanligt att översätta innehåll som innehåller markeringar som innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera Frågeparametern `textType=html` vid översättning av innehåll med taggar. Dessutom är det ibland användbart att undanta ett särskilt innehåll från översättningen. Du kan använda attributet `class=notranslate` för att ange innehåll som ska finnas kvar på det ursprungliga språket. I följande exempel kommer innehållet i det första `div`-elementet inte att översättas, medan innehållet i det andra `div`-elementet kommer att översättas.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är en exempel förfrågan som illustreras.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Hämta information om anpassning

Om du vill ta emot justerings information anger `includeAlignment=true` i frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Justerings informationen börjar med `0:2-0:1`, vilket innebär att de första tre tecknen i käll texten (`The`) mappar till de första två tecknen i den översatta texten (`La`).

#### <a name="limitations"></a>Begränsningar
Observera följande begränsningar:

* Justering är inte tillgängligt för text i HTML-format, dvs. textType = HTML
* Justering returneras bara för en delmängd av språk paren:
  - från engelska till ett annat språk.
  - från andra språk till engelska utom förenklad kinesiska, traditionell kinesiska och lettiska till engelska,
  - från japanska till koreanska eller från koreanska till japanska.
* Du får ingen justering om meningen är en konserverad översättning. Exempel på en konserverad översättning är "det här är ett test", "Jag älskar dig" och andra meningar med hög frekvens.

### <a name="obtain-sentence-boundaries"></a>Hämta menings gränser

Om du vill få information om menings längden i käll texten och översatt text anger du `includeSentenceLength=true` i frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Översätt med dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är bara säker för sammansatta substantiv som rätt namn och produkt namn.

Den markering som ska tillhandahållas använder följande syntax.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Anta till exempel den engelska meningen "ordet WordOMatic är en ord lista." Om du vill behålla ordet _WordOMatic_ i översättningen skickar du begäran:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Resultatet är:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Den här funktionen fungerar på samma sätt med `textType=text` eller med `textType=html`. Funktionen bör användas sparsamt. Ett lämpligt och mycket bättre sätt att anpassa översättning är genom att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan råd om att skapa tränings data som visar ditt arbete eller en fras i ett sammanhang får du bättre resultat. [Läs mer om anpassad översättare](../customization.md).
