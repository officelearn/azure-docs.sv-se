---
title: Microsoft Translator Text API översätta metoden | Microsoft Docs
titleSuffix: Cognitive Services
description: Använd Microsoft översättare Text API översätta-metoden.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d8d5e1e2fac747fa733f1d92c08008b7eac2a1bc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355629"
---
# <a name="text-api-30-translate"></a>Text API 3.0: översätta

Översätter text.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrar som skickas på frågesträngen är för begäran:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-version</td>
    <td>*Nödvändiga parametern*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>från</td>
    <td>*Valfri parameter*.<br/>Anger språket av indatatexten. Hitta vilka språk som är tillgängliga att översätta från genom att leta upp [språk som stöds](.\v3-0-languages.md) med hjälp av den `translation` omfång. Om den `from` parameter har angetts, automatisk identifiering av språk används för att fastställa källan språk.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Nödvändiga parametern*.<br/>Anger språket i texten för utdata. Mål-språket måste vara något av de [språk som stöds](.\v3-0-languages.md) ingår i den `translation` omfång. Till exempel använda `to=de` översätter till tyska.<br/>Det är möjligt att översätta flera språk samtidigt genom att upprepa parametern i frågesträngen. Till exempel använda `to=de&to=it` översätter till tyska och italienska.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Valfri parameter*.<br/>Anger om texten som översätts är oformaterad text eller HTML-format. All HTML-kod måste vara en giltig, fullständig element. Möjliga värden är: `plain` (standard) eller `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Valfri parameter*.<br/>En sträng som anger översättningen kategorin (domän). Den här parametern används för att hämta översättningar från en anpassad system som skapats med [anpassad konverterare](../customization.md). Standardvärdet är: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Valfri parameter*.<br/>Anger hur profanities behandlas på översättningar. Möjliga värden är: `NoAction` (standard), `Marked` eller `Deleted`. Information om olika sätt att behandla svordomar finns [svordomar hantering](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Valfri parameter*.<br/>Anger hur profanities bör markeras översättningar. Möjliga värden är: `Asterisk` (standard) eller `Tag`. Information om olika sätt att behandla svordomar finns [svordomar hantering](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Valfri parameter*.<br/>Anger om du vill inkludera justering projektion från källtext till översatt text. Möjliga värden är: `true` eller `false` (standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Valfri parameter*.<br/>Anger om du vill inkludera mening gränser för indatatexten och översatt text. Möjliga värden är: `true` eller `false` (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Valfri parameter*.<br/>Anger en grundspråk om språket för indatatexten inte kan identifieras. Automatisk identifiering av språk används när den `from` har utelämnats. Om identifiering misslyckas den `suggestedFrom` språk antas.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Valfri parameter*.<br/>Anger skriptet indatatexten.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Valfri parameter*.<br/>Anger skriptet översatt text.</td>
  </tr>
</table> 

Huvuden för begäran är:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>_En auktorisering_<br/>_Huvudet_</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Se [tillgängliga alternativ för autentisering](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Innehållstyp</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Anger innehållstypen i nyttolasten. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Längden på texten på begäran.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>En klient-genererade GUID som unik identifierare för begäran. Du kan hoppa över det här sidhuvudet om du inkluderar trace-ID i frågesträngen med hjälp av en Frågeparametern `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement i är en JSON-objekt med en string-egenskap med namnet `Text`, som motsvarar att översätta strängen.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar:

* Matrisen kan ha högst 25 element.
* Hela texten ingår i denna begäran får inte överskrida 5 000 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstexten

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i matrisen indata. En resultatobjekt innehåller följande egenskaper:

  * `detectedLanguage`: Ett objekt som beskriver identifierade språket via följande egenskaper:

      * `language`: En sträng som representerar koden för upptäckta språk.

      * `score`: Ett flyttal-värde som anger förtroende i resultatet. Poängen är mellan noll och ett och en låg poäng anger en låg förtroende.

    Den `detectedLanguage` egenskapen finns bara i resultatobjektet när automatisk identifiering av språk som begärs.

  * `translations`: En matris med översättningsresultat. Storleken på matrisen stämmer med antalet mål språk som anges via den `to` Frågeparametern. Varje element i matrisen innehåller:

    * `to`: En sträng som representerar språkkoden för språket som mål.

    * `text`: En sträng som ger den översatta texten.

    * `transliteration`: Ett objekt som ger den översatta texten i skriptet som anges av den `toScript` parameter.

      * `script`: En sträng som anger skriptet mål.   

      * `text`: En sträng som ger den översatta texten i skriptet med målet.

    Den `transliteration` objekt inkluderas inte om transliteration inte sker.

    * `alignment`: Ett objekt med en enda string-egenskap med namnet `proj`, som mappar indatatext till översatt text. Justering informationen tillhandahålls endast när begäranparametern `includeAlignment` är `true`. Justering returneras som ett strängvärde i följande format: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Avgränsas start och end index, ett streck separerar språk och blanksteg mellan ord. Ett ord som kan justera med noll, en eller flera ord på andra språk och justerade orden kan vara icke sammanhängande. När ingen justering information är tillgänglig, vara elementet justering tomt. Se [få information om justering](#obtain-alignment-information) för ett exempel och begränsningar.

    * `sentLen`: Ett objekt som returnerar meningen gränser i inkommande och utgående texterna.

      * `srcSentLen`: En heltalsmatris som representerar meningar i indatatexten längder. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`: En heltalsmatris som representerar längder i meningar i översatta texten. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

    Meningen gränser är bara ingår när begäranparametern `includeSentenceLength` är `true`.

  * `sourceText`: Ett objekt med en enda string-egenskap med namnet `text`, vilket ger indatatexten i standardskript för språket som källa. `sourceText` Egenskapen är endast när indata uttrycks i ett skript som inte är vanligt skript för språket. Om exempelvis indata har arabiska som skrivits i Latin skriptet sedan `sourceText.text` skulle samma Arabic text konverteras till Arabrepubliken skript.

Exempel på JSON-svar finns i den [exempel](#examples) avsnitt.

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga http-statuskoder som returnerar en begäran. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltigt. Korrigera parametrarna innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna är angiven och giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran är inte auktoriserad. Läs felmeddelandet information. Detta indikerar ofta att alla gratis översättningar som medföljer en utvärderingsprenumeration har använts.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Anroparen för många begäranden skickas.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår kan du rapportera det med: datum och tid för felet begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår kan du rapportera det med: datum och tid för felet begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Omvandla en enda inmatning

Det här exemplet visar hur man konverterar en mening från engelska till förenklad kinesiska.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

Den `translations` matris innehåller ett element, vilket ger översättning av enda text i indata.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Omvandla en enda indata med automatisk identifiering av språk

Det här exemplet visar hur man konverterar en mening från engelska till förenklad kinesiska. Begäran har ingen angiven inmatningsspråk. Automatisk identifiering av käll-språk används i stället.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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
Svaret liknar svaret från föregående exempel. Eftersom automatisk identifiering av språk begärdes innehåller även information om språk som identifierats för indatatexten i svaret. 

### <a name="translate-with-transliteration"></a>Översätt med transliteration

Vi utöka det tidigare exemplet genom att lägga till transliteration. Följande begäran begär en översättning för kinesiska skrivs i Latin-skriptet.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
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
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Översättning resultatet innehåller nu en `transliteration` -egenskap som ger översatt text med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätta flera delar av texten

Översättning av samtidigt flera strängar är bara att ange en matris med strängar i begärandetexten.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

### <a name="handle-profanity"></a>Hantera svordomar

Normalt behåller tjänsten översättare svordomar som finns i datakällan i översättningen. Graden av svordomar och kontext som gör ord vulgärt skiljer sig åt mellan kulturer och därför graden av svordomar på språket som mål kan framhävas eller minskas.

Du kan använda svordomar filtrering alternativet om du vill undvika svordomar i översättning, oavsett förekomst av svordomar i källtexten. Alternativet kan du välja om du vill visa svordomar bort, oavsett om du vill markera profanities med lämpliga taggar (och du får välja att lägga till egna efter bearbetning) eller om du vill att ingen åtgärd vidtogs. Accepterade värden för `ProfanityAction` är `Deleted`, `Marked` och `NoAction` (standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Åtgärd</th>
  <tr>
    <td>`NoAction`</td>
    <td>Detta är standardbeteendet. Svordomar skickar från källan till målet.<br/><br/>
    **Exempel källan (japanska)**: 彼はジャッカスです。<br/>
    **Exempel översättning (engelska)**: han är en jackass.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Vulgärt ord tas bort från utdata utan ersättning.<br/><br/>
    **Exempel källan (japanska)**: 彼はジャッカスです。<br/>
    **Exempel översättning (engelska)**: han är en.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Vulgärt ersättas med en markör i utdata. Markören är beroende av den `ProfanityMarker` parameter.<br/><br/>
För `ProfanityMarker=Asterisk`, vulgärt ersättas med `***`:<br/>
    **Exempel källan (japanska)**: 彼はジャッカスです。<br/>
    **Exempel översättning (engelska)**: han är en \* \* \*.<br/><br/>
För `ProfanityMarker=Tag`, vulgärt ord omges av XML-taggar &lt;svordomar&gt; och &lt;/profanity&gt;:<br/>
    **Exempel källan (japanska)**: 彼はジャッカスです。<br/>
    **Exempel översättning (engelska)**: han är en &lt;svordomar&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Exempel:

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Den senaste begäranden returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätt innehåll med markering och bestämma vad översätts

Det är vanligt att översätta innehåll som innehåller markeringar, till exempel innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera frågeparameter `textType=html` vid översättning av innehåll med taggar. Dessutom kan det vara bra att undanta specifika innehåll från översättning. Du kan använda attributet `class=notranslate` Ange innehåll som ska finnas kvar i det ursprungliga språket. I följande exempel innehåll i först `div` kommer inte att översätta elementet när innehållet i andra `div` element ska översättas.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är ett exempel på begäran att illustrera.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

### <a name="obtain-alignment-information"></a>Få information om justering

Om du vill ta emot information om justering ange `includeAlignment=true` för frågesträngen.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

Justering av information som börjar med `0:2-0:1`, vilket innebär att de första tre tecknen i texten källa (`The`) mappas till de första två tecknen i översatt text (`La`).

Observera följande begränsningar:

* Justering returneras endast för en delmängd av språkpar:
  - från engelska till andra språk.
  - från ett annat språk till engelska utom kinesiska, förenklad kinesiska, traditionell kinesiska och lettiska till engelska.
  - från japanska till koreanska eller från koreanska till japanska.
* Du får inte justering om meningen är en kapslade översättning. Exempel på en kapslade översättning är ”det här är ett test”, ”jag gillar du” och andra hög frekvens meningar.

### <a name="obtain-sentence-boundaries"></a>Hämta meningen gränser

Ta emot information om Meningslängd i källtext och översatt text, ange `includeSentenceLength=true` för frågesträngen.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

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

### <a name="translate-with-dynamic-dictionary"></a>Översätt med dynamiska ordlista

Om du redan vet översättning som du vill tillämpa på ett ord eller en fras kan du ange den som markeringar i begäran. Dynamisk ordlistan är endast säkra för sammansatta substantiv som namn och produktnamn.

Ange koden använder du följande syntax.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Anta till exempel att den engelska meningen ”word-wordomatic är en ordlista”. Att bevara ordet _wordomatic_ i översättning, skicka begäran:

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

Den här funktionen fungerar på samma sätt med `textType=text` eller med `textType=html`. Funktionen bör användas sparsamt. Lämpliga och mycket bättre sätt för att anpassa översättningen är genom att använda anpassade konverterare. Anpassade konverterare är full användning av kontexten och statistiska sannolikhet. Om du har eller har råd att skapa träningsdata som visar ditt arbete eller en fras i kontexten får mycket bättre resultat. [Mer information om anpassade konverterare](../customization.md).
 





