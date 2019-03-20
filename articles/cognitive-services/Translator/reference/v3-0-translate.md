---
title: API för textöversättning översätta metod
titleSuffix: Azure Cognitive Services
description: Använd metoden Translator Text API översätta.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 4f6c420ab76462818fb17308d062cc9d881af7df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091043"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: Translate

Översätter text.

## <a name="request-url"></a>URL för begäran

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna som skickades mot frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td><em>Obligatoriska parametern</em>.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>från</td>
    <td><em>Valfri parameter</em>.<br/>Anger språket i indatatexten. Hitta vilka språk är tillgängliga att översätta från genom att leta upp [språk som stöds](./v3-0-languages.md) med hjälp av den <code>translation</code> omfång. Om den <code>from</code> parametern inte anges, automatisk språkidentifiering används för att fastställa en källspråket.</td>
  </tr>
  <tr>
    <td>till</td>
    <td><em>Obligatoriska parametern</em>.<br/>Anger språket i utdata texten. Målspråket som måste vara något av de [språk som stöds](./v3-0-languages.md) ingår i den <code>translation</code> omfång. Till exempel använda <code>to=de</code> att översätta tyska.<br/>Det är möjligt att översätta på flera språk samtidigt genom att upprepa parametern i frågesträngen. Till exempel använda <code>to=de&to=it</code> att översätta tyska och italienska.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Valfri parameter</em>.<br/>Anger om texten som översätts är oformaterad text eller HTML-text. All HTML-kod måste vara en korrekt strukturerad, fullständig element. Möjliga värden är: <code>plain</code> (standard) eller <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Valfri parameter</em>.<br/>En sträng som anger kategorin (domän) för översättningen. Den här parametern används för att hämta översättningar från ett anpassat system som skapats med [anpassad Translator](../customization.md). Lägg till kategori-ID från projektet anpassade Translator för den här parametern för att använda ditt anpassade distribuerade system. Standardvärdet är: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur profanities ska hanteras på översättningar. Möjliga värden är: <code>NoAction</code> (standard), <code>Marked</code> eller <code>Deleted</code>. Information om olika sätt att behandla svordomar finns i [svordomar hantering](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur profanities bör markeras i översättningar. Möjliga värden är: <code>Asterisk</code> (standard) eller <code>Tag</code>. Information om olika sätt att behandla svordomar finns i [svordomar hantering](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill inkludera justering projektion från källtext till översatt text. Möjliga värden är: <code>true</code> eller <code>false</code> (standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill inkludera mening gränser för indatatexten och den översatta texten. Möjliga värden är: <code>true</code> eller <code>false</code> (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Valfri parameter</em>.<br/>Anger en återställningsplats språk om det inte går att identifiera språket i indatatexten. Automatisk identifiering av språk används när den <code>from</code> parametern utelämnas. Om identifieringen misslyckas den <code>suggestedFrom</code> antas språk.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet på indatatexten.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet på den översatta texten.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Valfri parameter</em>.<br/>Anger att tjänsten ska kunna återgå till ett allmänt system när en anpassad system inte finns. Möjliga värden är: <code>true</code> (standard) eller <code>false</code>.<br/><br/><code>allowFallback=false</code> Anger att översättningen bara ska använda system som har tränats för den <code>category</code> anges i begäran. Om en översättning för språk X språk Y kräver länkning via en pivot-språk E, sedan alla system i kedjan (X -> E- och E -> Y) måste vara anpassad och har samma kategori. Om inga så är fallet med viss kategori returnerar begäran 400-statuskod. <code>allowFallback=true</code> Anger att tjänsten ska kunna återgå till ett allmänt system när en anpassad system inte finns.
</td>
  </tr>
</table> 

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>_En auktorisering_<br/>_header_</td>
    <td><em>Nödvändiga begärandehuvudet</em>.<br/>Se [tillgängliga alternativ för autentisering](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Nödvändiga begärandehuvudet</em>.<br/>Anger innehållstypen för nyttolasten. Möjliga värden är: <code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Nödvändiga begärandehuvudet</em>.<br/>Längden på det begärda innehållet.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Valfritt</em>.<br/>En klientgenererade GUID för unik identifiering på begäran. Du kan utelämna den här rubriken om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter som heter <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med en strängegenskap med namnet `Text`, som representerar strängen att översätta.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Hela texten i begäran får inte överskrida 5 000 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstext

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i Indatamatrisen. En resultatobjektet innehåller följande egenskaper:

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket via följande egenskaper:

      * `language`: En sträng som representerar koden för identifierat språk.

      * `score`: Ett flyttalsvärde som anger förtroende i resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett låga förtroende.

    Den `detectedLanguage` egenskapen finns bara i resultatobjektet när automatisk identifiering av språk som efterfrågas.

  * `translations`: En matris med translation resultat. Storleken på matrisen stämmer med antalet mål språk som anges via den `to` frågeparameter. Varje element i matrisen innehåller:

    * `to`: En sträng som representerar språkkod för språket som mål.

    * `text`: En sträng som ger den översatta texten.

    * `transliteration`: Ett objekt som ger den översatta texten i skriptet som anges av den `toScript` parametern.

      * `script`: En sträng som anger att mål-skriptet.   

      * `text`: En sträng som ger den översatta texten i mål-skriptet.

    Den `transliteration` objekt ingår inte om transkriberingsspråk inte ägde rum.

    * `alignment`: Ett objekt med en enkel sträng-egenskap med namnet `proj`, vilket mappar textinmatning till översatt text. Justering informationen tillhandahålls endast när parametern begäran `includeAlignment` är `true`. Justering returneras som ett strängvärde i följande format: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Avgränsas start och slut-index, ett streck separerar språk och blanksteg mellan ord. Ett ord som kan anpassas till noll, ett eller flera ord på andra språk och justerade orden kanske inte är sammanhängande. Om det finns ingen information om justering ska textjustering elementet vara tomt. Se [få justering information](#obtain-alignment-information) för ett exempel och begränsningar.

    * `sentLen`: Ett objekt som returnerar mening gränser i inkommande och utgående texter.

      * `srcSentLen`: En heltalsmatris som representerar längden på meningarna i indatatexten. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`:  En heltalsmatris som representerar längden på meningarna i den översatta texten. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

    Mening gränser är endast ingår när parameter för förfrågan `includeSentenceLength` är `true`.

  * `sourceText`: Ett objekt med en enkel sträng-egenskap med namnet `text`, vilket ger indatatexten i standardskriptet för språket som källa. `sourceText` Egenskapen finns bara när indata uttrycks i ett skript som inte är vanligt skriptet för språket. Exempel: om indata har arabiska som skrivits i latinska alfabetet, sedan `sourceText.text` skulle samma Arabic text konverteras till Arabrepubliken skript.

Exempel på JSON-svaren finns i den [exempel](#examples) avsnittet.

## <a name="response-headers"></a>Svarshuvud

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
    <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används för felsökning.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Anger den systemtyp av som har använts för översättning för språket som efterfrågas för översättning varje ”till”. Värdet är en kommaavgränsad lista med strängar. Varje sträng Anger en typ:<br/><ul><li>Anpassad - begäran innehåller en anpassad system och minst en anpassad system används vid översättning.</li><li>Team – alla övriga förfrågningar</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svarsstatuskoder

Här följer möjliga HTTP-statuskoder som returnerar en begäran. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltig. Korrigera parametrarna innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna är angivna och giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran har inte behörighet. Finns information om felmeddelandet. Detta innebär ofta att alla kostnadsfria översättningar som medföljer en utvärderingsprenumeration har förbrukats.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Begäran kan inte fullföljas eftersom en resurs saknas. Finns information om felmeddelandet. När du använder en anpassad <code>category</code>, detta indikerar ofta att den anpassade översättningssystemet inte ännu tillgänglig för att hantera begäranden. Begäran ska göras efter en väntetid (t.ex. 1 minut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Anroparen skickar för många förfrågningar.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud <code>X-RequestId</code>, och klient-ID från begärandehuvudet <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud <code>X-RequestId</code>, och klient-ID från begärandehuvudet <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Om ett fel inträffar, returneras också en JSON-felsvar i begäran. Felkoden är en 6-siffrig nummer kombinera 3-siffriga HTTP-statuskoden följt av ett 3-siffriga tal till ytterligare kategorisera felet. Vanliga felkoder finns på den [v3 Translator Text API-referenssida](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Översätta en enda indata

Det här exemplet visar hur du översätta en mening från engelska till kinesiska (förenklad).

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Svarstexten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Den `translations` matris innehåller ett element som tillhandahåller översättningen av den enda textstycke i indata.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Översätta en enkel inmatning med automatisk identifiering av språk

Det här exemplet visar hur du översätta en mening från engelska till kinesiska (förenklad). Begäran anger inte språket. Automatisk identifiering av källspråket används i stället.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Svarstexten är:

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
Svaret liknar svaret från föregående exempel. Eftersom automatisk språkidentifiering-begärdes innehåller även information om språk som har identifierats för indatatexten i svaret. 

### <a name="translate-with-transliteration"></a>Översätt med transkriberingsspråk

Vi utökar det tidigare exemplet genom att lägga till transkriberingsspråk. Följande begäran begär en kinesiska översättning som skrivits i Latin-skript.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Svarstexten är:

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

Översättning resultatet innehåller nu en `transliteration` -egenskapen, vilket ger den översatta texten med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätt flera typer av text

Översättning av flera strängar på samma gång är bara att ange en matris med strängar i begärandetexten.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

Svarstexten är:

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

Det här exemplet visar hur du översätta samma indata för flera språk i en begäran.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Svarstexten är:

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

### <a name="handle-profanity"></a>Hantera olämpligt språk

Normalt behåller tjänsten Translator svordomar som finns i källan i översättningen. Graden av svordomar och kontext som gör ord olämpliga skiljer sig åt mellan kulturer och därmed graden av olämpligt språk på språket som mål kan framhävas eller minskas.

Du kan använda svordomar filtrering alternativet om du vill undvika svordomar i översättning, oavsett förekomsten av svordomar i källtext. Alternativet kan du välja om du vill se svordomar bort, oavsett om du vill markera profanities med lämpliga taggar (vilket ger dig möjlighet att lägga till egna efterbearbeta), eller om du vill att någon åtgärd krävs. Godkända värden för `ProfanityAction` är `Deleted`, `Marked` och `NoAction` (standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Åtgärd</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Detta är standardbeteendet. Svordomar skickas från källan till målet.<br/><br/>
    <strong>Exempel källan (japanska)</strong>: 彼はジャッカスです。<br/>
    <strong>Exempel Translation (på engelska)</strong>: Han är en jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Olämpliga ord. tas bort från utdata utan ersättning.<br/><br/>
    <strong>Exempel källan (japanska)</strong>: 彼はジャッカスです。<br/>
    <strong>Exempel Translation (på engelska)</strong>: Han är en.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Olämpliga ersättas med en markör i utdata. Markörens beror på den <code>ProfanityMarker</code> parametern.<br/><br/>
För <code>ProfanityMarker=Asterisk</code>, olämpliga ord har ersatts med <code>***</code>:<br/>
    <strong>Exempel källan (japanska)</strong>: 彼はジャッカスです。<br/>
    <strong>Exempel Translation (på engelska)</strong>: Han är en \* \* \*.<br/><br/>
För <code>ProfanityMarker=Tag</code>, olämpliga ord. omges av XML-taggar &lt;svordomar&gt; och &lt;/profanity&gt;:<br/>
    <strong>Exempel källan (japanska)</strong>: 彼はジャッカスです。<br/>
    <strong>Exempel Translation (på engelska)</strong>: Han är en &lt;svordomar&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Exempel:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätt innehåll med markup och Bestäm vad är översatt?

Det är vanligt att översätta innehåll som innehåller markup, till exempel innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera Frågeparametern `textType=html` vid översättning av innehåll med taggar. Dessutom kan är ibland det praktiskt att utesluta translation specifikt innehåll. Du kan använda attributet `class=notranslate` och ange innehåll som ska vara på dess ursprungliga språk. I följande exempel innehållet i först `div` elementet kommer inte att översättas när innehållet i andra `div` elementet kommer att översättas.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är ett exempel på begäran för att illustrera.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

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

### <a name="obtain-alignment-information"></a>Hämta information för justering

För att få information om justering, ange `includeAlignment=true` i frågesträngen.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

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

Justering av information som börjar med `0:2-0:1`, vilket innebär att de första tre tecknen i texten källa (`The`) mappas till de första två tecknen i den översatta texten (`La`).

Observera följande begränsningar:

* Justering returneras bara för en delmängd av språkpar:
  - från engelska till andra språk.
  - från ett annat språk till engelska förutom kinesiska, förenklad kinesiska, traditionell kinesiska och lettiska till engelska.
  - från japanska till koreanska eller från koreanska till japanska.
* Du får inte justering om meningen är en kapslade översättning. Exempel på en kapslade översättning är ”detta är ett test”, ”jag älskar du” och andra hög frekvens meningar.

### <a name="obtain-sentence-boundaries"></a>Hämta mening gränser

För att få information om Meningslängd i källtext och översatt text, ange `includeSentenceLength=true` i frågesträngen.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

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

### <a name="translate-with-dynamic-dictionary"></a>Omvandla dynamisk ordlista

Om du redan vet översättning som du vill använda på ett ord eller en fras kan ange du den som markup i begäran. Dynamisk ordlistan är endast säkra för sammansatt substantiv som egennamn och produktnamn.

Kod för att ange använder du följande syntax.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Anta exempelvis att engelska meningen ”word wordomatic är en post i ordlistan”. Att bevara ordet _wordomatic_ i översättningen, skickar du begäran:

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

Den här funktionen fungerar på samma sätt med `textType=text` eller med `textType=html`. Funktionen bör användas sparsamt. Det lämpliga och mycket bättre sättet att anpassa translation är med hjälp av anpassade Translator. Anpassade Translator blir fullt ut utnyttja kontext och statistiska sannolikhet. Om du har eller har råd att skapa träningsdata som visar ditt arbete eller en fras i sammanhanget, får mycket bättre resultat. [Mer information om anpassade Translator](../customization.md).
 





