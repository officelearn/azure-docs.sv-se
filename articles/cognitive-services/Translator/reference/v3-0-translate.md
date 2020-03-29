---
title: Översätta metod för översättartext-API
titleSuffix: Azure Cognitive Services
description: Förstå parametrar, rubriker och brödtextmeddelanden för Azure Cognitive Services Translator Text API Translate-metoden för att översätta text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 1821623fbe2a22234af649934ac06e72897a19cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052401"
---
# <a name="translator-text-api-30-translate"></a>Översättare Text API 3.0: Översätt

Översätter text.

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

### <a name="required-parameters"></a>Obligatoriska parametrar

<table width="100%">
  <th width="20%">Parameter för fråga</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Version av API som begärs av klienten. Värdet måste <code>3.0</code>vara .</td>
  </tr>
  <tr>
    <td>till</td>
    <td><em>Obligatorisk parameter</em>.<br/>Anger utdatatextens språk. Målspråket måste vara ett av de <code>translation</code> språk som <a href="./v3-0-languages.md">stöds</a> i omfånget. Använd <code>to=de</code> till exempel för att översätta till tyska.<br/>Det är möjligt att översätta till flera språk samtidigt genom att upprepa parametern i frågesträngen. Använd <code>to=de&to=it</code> till exempel för att översätta till tyska och italienska.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Valfria parametrar

<table width="100%">
  <th width="20%">Parameter för fråga</th>
  <th>Beskrivning</th>
  <tr>
    <td>Från</td>
    <td><em>Valfri parameter</em>.<br/>Anger språket för indatatexten. Hitta vilka språk som är tillgängliga att översätta <code>translation</code> från genom att söka efter språk som <a href="./v3-0-languages.md">stöds</a> med hjälp av scopet. Om <code>from</code> parametern inte har angetts används automatisk språkidentifiering för att bestämma källspråket. <br/><br/>Du måste <code>from</code> använda parametern i stället för automatisk avdektion när du använder funktionen <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">för dynamisk ordlista.</a></td>
  </tr>  
  <tr>
    <td>textTyp</td>
    <td><em>Valfri parameter</em>.<br/>Definierar om texten som översätts är oformaterad text eller HTML-text. Alla HTML måste vara ett välformat, komplett element. Möjliga värden <code>plain</code> är: (standard) eller <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Valfri parameter</em>.<br/>En sträng som anger översättningens kategori (domän). Den här parametern används för att hämta översättningar från ett anpassat system byggt med <a href="../customization.md">Custom Translator</a>. Lägg till kategori-ID:et från <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projektinformationen</a> för Custom Translator i den här parametern om du vill använda det distribuerade anpassade systemet. Standardvärdet är: <code>general</code>.</td>
  </tr>
  <tr>
    <td>svordomarAction</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska behandlas i översättningar. Möjliga värden <code>NoAction</code> är: <code>Marked</code> (standard) eller <code>Deleted</code>. För att förstå sätt att behandla svordomar, se <a href="#handle-profanity">Svordomar hantering</a>.</td>
  </tr>
  <tr>
    <td>svordomarMarker</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska markeras i översättningar. Möjliga värden <code>Asterisk</code> är: (standard) eller <code>Tag</code>. För att förstå sätt att behandla svordomar, se <a href="#handle-profanity">Svordomar hantering</a>.</td>
  </tr>
  <tr>
    <td>inkluderaJustering</td>
    <td><em>Valfri parameter</em>.<br/>Anger om justeringsprojektion ska inkluderas från källtext till översatt text. Möjliga värden <code>true</code> är: eller <code>false</code> (standard). </td>
  </tr>
  <tr>
    <td>inkluderaSentenceLength</td>
    <td><em>Valfri parameter</em>.<br/>Anger om meningsgränserna för indatatexten och den översatta texten ska inkluderas. Möjliga värden <code>true</code> är: eller <code>false</code> (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Valfri parameter</em>.<br/>Anger ett återgångsspråk om språket i indatatexten inte kan identifieras. Automatisk identifiering av språk <code>from</code> används när parametern utelämnas. Om identifieringen <code>suggestedFrom</code> misslyckas antas språket.</td>
  </tr>
  <tr>
    <td>frånSkript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet för indatatexten.</td>
  </tr>
  <tr>
    <td>toScript (toScript)</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet för den översatta texten.</td>
  </tr>
  <tr>
    <td>tillåtFallback</td>
    <td><em>Valfri parameter</em>.<br/>Anger att tjänsten tillåts att gå tillbaka till ett allmänt system när ett anpassat system inte finns. Möjliga värden <code>true</code> är: (standard) eller <code>false</code>.<br/><br/><code>allowFallback=false</code>anger att översättningen endast ska använda <code>category</code> system som utbildats för den som anges av begäran. Om en översättning för språk X till språk Y kräver kedja genom ett pivotspråk E måste alla system i kedjan (X->E och E->Y) vara anpassade och ha samma kategori. Om inget system hittas med den specifika kategorin returneras en 400-statuskod för begäran. <code>allowFallback=true</code>anger att tjänsten tillåts att gå tillbaka till ett allmänt system när ett anpassat system inte finns.
</td>
  </tr>
</table> 

Begäran rubriker inkluderar:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Autentiseringshuvuden</td>
    <td><em>Obligatoriskt begäranhuvud</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Obligatoriskt begäranhuvud</em>.<br/>Anger innehållstypen för nyttolasten.<br/> Accepterat värde <code>application/json; charset=UTF-8</code>är .</td>
  </tr>
  <tr>
    <td>Innehållslängd</td>
    <td><em>Obligatoriskt begäranhuvud</em>.<br/>Längden på begäran kroppen.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Valfritt</em>.<br/>Ett klientgenererat GUID för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av frågeparametern <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten för begäran är en JSON-matris. Varje arrayelement är ett JSON-objekt `Text`med en strängegenskap med namnet , som representerar strängen att översätta.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Hela texten som ingår i begäran får inte överstiga 5 000 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket genom följande egenskaper:

      * `language`: En sträng som representerar koden för det identifierade språket.

      * `score`: Ett flytvärde som anger förtroendet för resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett lågt självförtroende.

    Egenskapen `detectedLanguage` finns bara i resultatobjektet när automatisk identifiering av språk begärs.

  * `translations`: En rad översättningsresultat. Matrisens storlek matchar antalet målspråk som `to` anges via frågeparametern. Varje element i matrisen innehåller:

    * `to`: En sträng som representerar målspråkets språkkod.

    * `text`: En sträng som ger den översatta texten.

    * `transliteration`: Ett objekt som anger den översatta `toScript` texten i skriptet som anges av parametern.

      * `script`: En sträng som anger målskriptet.   

      * `text`: En sträng som ger den översatta texten i målskriptet.

    Objektet `transliteration` ingår inte om translittereringen inte sker.

    * `alignment`: Ett objekt med en `proj`enda strängegenskap med namnet , som mappar indatatext till översatt text. Justeringsinformationen anges endast när `includeAlignment` parametern för begäran är `true`. Justeringen returneras som ett strängvärde `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`för följande format: .  Kolon separerar start- och slutindex, strecket separerar språken och utrymmet separerar orden. Ett ord kan justeras med noll, en eller flera ord på det andra språket, och de justerade orden kan vara icke-sammanhängande. När det inte finns någon justeringsinformation är justeringselementet tomt. Se [Hämta justeringsinformation](#obtain-alignment-information) för ett exempel och begränsningar.

    * `sentLen`: Ett objekt som returnerar meningsgränser i in- och utdatatexterna.

      * `srcSentLen`: En heltalsmatris som representerar längden på meningarna i indatatexten. Matrisens längd är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`: En heltalsmatris som representerar längden på meningarna i den översatta texten. Matrisens längd är antalet meningar och värdena är längden på varje mening.

    Meningsgränser inkluderas endast när `includeSentenceLength` `true`parametern för begäran är .

  * `sourceText`: Ett objekt med en `text`enda strängegenskap med namnet , som ger indatatexten i standardskriptet för källspråket. `sourceText`egenskapen finns endast när indata uttrycks i ett skript som inte är det vanliga skriptet för språket. Om indata till exempel var arabiska skrivna i latinskt skript, skulle det `sourceText.text` vara samma arabiska text som konverterats till arabiskt skript.

Exempel på JSON-svar [examples](#examples) finns i exempelavsnittet.

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
    <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används för felsökning.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Anger den systemtyp som användes för översättning för varje "till"-språk som begärs för översättning. Värdet är en kommaavgränsad lista med strängar. Varje sträng anger en typ:<br/><ul><li>Anpassad - Begäran innehåller ett anpassat system och minst ett anpassat system användes under översättningen.</li><li>Team - Alla andra önskemål</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna saknas eller är ogiltig. Korrigera parametrar för begäran innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna är angivna och giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran är inte auktoriserad. Kontrollera informationsfelmeddelandet. Detta indikerar ofta att alla kostnadsfria översättningar som medföljer en provprenumeration har förbrukats.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Det gick inte att uppfylla begäran eftersom en resurs saknas. Kontrollera informationsfelmeddelandet. När du <code>category</code>använder en anpassad anger detta ofta att det anpassade översättningssystemet ännu inte är tillgängligt för att hantera begäranden. Begäran ska göras om efter en väntetid (t.ex. 1 minut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran eftersom klienten har överskridit begärangränser.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för <code>X-RequestId</code>felet, begärandeidentifierare från svarshuvudet och klientidentifierare från begäranden <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Försök igen. Om felet kvarstår rapporterar du det med: datum och tid för <code>X-RequestId</code>felet, begärandeidentifierare från svarshuvudet och klientidentifierare från begäranden <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran också ett JSON-felsvar. Felkoden är ett 6-siffrigt nummer som kombinerar den tresiffriga HTTP-statuskoden följt av ett tresiffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referenssidan för v3 Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Översätt en enda ingång

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svaret kroppen är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Matrisen `translations` innehåller ett element som ger översättning av det enda textstycket i indata.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Översätta en enda indata med automatisk språkidentifiering

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska. Begäran anger inte inmatningsspråket. Automatisk identifiering av källspråket används i stället.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svaret kroppen är:

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
Svaret liknar svaret från föregående exempel. Eftersom automatisk identifiering av språk begärdes innehåller svaret även information om det språk som upptäckts för indatatexten. 

### <a name="translate-with-transliteration"></a>Översätt med translitterering

Låt oss utöka föregående exempel genom att lägga till translitterering. Följande begäran ber om en kinesisk översättning skriven i latinskt skript.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svaret kroppen är:

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

Översättningsresultatet innehåller `transliteration` nu en egenskap som ger den översatta texten med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätt flera textstycken

Översätta flera strängar på en gång är helt enkelt en fråga om att ange en matris med strängar i begäran kroppen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Svaret kroppen är:

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

Det här exemplet visar hur du översätter samma indata till flera språk i en begäran.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svaret kroppen är:

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

Normalt behåller översättaretjänsten svordomar som finns i källan i översättningen. Graden av svordomar och det sammanhang som gör ord profana skiljer sig mellan kulturer, och som ett resultat graden av svordomar i målspråket kan förstärkas eller minskas.

Om du vill undvika att få svordomar i översättningen, oavsett förekomsten av svordomar i källtexten, kan du använda svordomar filtrering alternativet. Med alternativet kan du välja om du vill se svordomar borttagna, om du vill markera svordomar med lämpliga taggar (vilket ger dig möjlighet att lägga till din egen efterbearbetning), eller om du inte vill att någon åtgärd vidtas. De godkända `ProfanityAction` värdena `Marked` `NoAction` är `Deleted`och (standard).

<table width="100%">
  <th width="20%">SvordomarÅtgärder</th>
  <th>Åtgärd</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Det här är standardbeteendet. Svordomar kommer att passera från källa till mål.<br/><br/>
    <strong>Exempelkälla (japanska)</strong>: 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン<br/>
    <strong>Exempel översättning (engelska)</strong>: Han är en idiot.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Profana ord tas bort från utdata utan ersättning.<br/><br/>
    <strong>Exempelkälla (japanska)</strong>: 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン<br/>
    <strong>Exempel översättning (engelska)</strong>: Han är a.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Profana ord ersätts med en markör i utdata. Markören beror <code>ProfanityMarker</code> på parametern.<br/><br/>
För <code>ProfanityMarker=Asterisk</code>ersätts profana <code>***</code>ord med:<br/>
    <strong>Exempelkälla (japanska)</strong>: 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン<br/>
    <strong>Exempel översättning (engelska)</strong>: \* \* \*Han är en .<br/><br/>
<code>ProfanityMarker=Tag</code>För, profana ord omges &lt;av&gt; XML-taggar svordomar och &lt;/&gt;svordomar:<br/>
    <strong>Exempelkälla (japanska)</strong>: 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン<br/>
    <strong>Exempel översättning (engelska)</strong>: &lt;Han&gt;är en&lt;svordomar jackass / svordomar&gt;.
  </tr>
</table> 

Ett exempel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
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
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Den sista begäran returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätt innehåll med markering och bestäm vad som är översatt

Det är vanligt att översätta innehåll som innehåller markeringar, till exempel innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera frågeparametern `textType=html` när du översätter innehåll med taggar. Dessutom är det ibland användbart att utesluta specifikt innehåll från översättningen. Du kan använda `class=notranslate` attributet för att ange innehåll som ska finnas kvar på originalspråket. I följande exempel översätts `div` inte innehållet i det första elementet, `div` medan innehållet i det andra elementet översätts.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är ett exempel begäran att illustrera.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
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

### <a name="obtain-alignment-information"></a>Hämta justeringsinformation

Om du vill `includeAlignment=true` ta emot justeringsinformation anger du på frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
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

Justeringsinformationen börjar `0:2-0:1`med , vilket innebär att de`The`tre första tecknen i källtexten (`La`) mappas till de två första tecknen i den översatta texten ( ).

#### <a name="limitations"></a>Begränsningar
Att få anpassningsinformation är en experimentell funktion som vi har möjliggjort för prototyper forskning och erfarenheter med potentiella fraskartläggningar. Vi kan välja att sluta stödja detta i framtiden. Här är några av de anmärkningsvärda begränsningar där justeringar inte stöds:

* Justering är inte tillgänglig för text i HTML-format, dvs textType=html
* Justeringen returneras endast för en delmängd av språkparen:
  - från engelska till något annat språk;
  - från något annat språk till engelska med undantag för förenklad kinesiska, traditionell kinesiska och lettiska till engelska;
  - från japanska till koreanska eller från koreanska till japanska.
* Du får inte justering om meningen är en konserverad översättning. Exempel på en konserverad översättning är "Detta är ett test", "Jag älskar dig" och andra högfrekventa meningar.
* Justering är inte tillgänglig när du använder någon av metoderna för att förhindra översättning enligt beskrivningen [här](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Få meningsgränser

Om du vill ta emot information om meningslängd i källtexten och översatt text anger du `includeSentenceLength=true` på frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
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

### <a name="translate-with-dynamic-dictionary"></a>Översätt med dynamisk ordlista

Om du redan känner till den översättning som du vill använda på ett ord eller en fras kan du ange den som pålägg i begäran. Den dynamiska ordlistan är endast säker för sammansatta substantiv som riktiga namn och produktnamn.

Markeringen som ska levereras använder följande syntax.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Tänk till exempel på den engelska meningen "Ordet wordomatic är en ordlistepost". Om du vill bevara ordet _wordomatic_ i översättningen skickar du begäran:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Resultatet är:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Den här funktionen fungerar `textType=text` på `textType=html`samma sätt med eller med . Funktionen bör användas sparsamt. Det lämpliga och mycket bättre sättet att anpassa översättningen är att använda Custom Translator. Custom Translator utnyttjar kontext och statistiska sannolikheter fullt ut. Om du har eller har råd att skapa träningsdata som visar ditt arbete eller din fras i sitt sammanhang får du mycket bättre resultat. [Läs mer om Custom Translator](../customization.md).
