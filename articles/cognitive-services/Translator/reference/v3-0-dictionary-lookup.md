---
title: Sökmetod för Microsoft Translator Text API-ordlista | Microsoft Docs
description: Använda Microsoft Translator Text API ordlista sökmetod.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 5a186f60dc099b095c00056d965aa92618c2c708
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868093"
---
# <a name="text-api-30-dictionary-lookup"></a>API 3.0 för textöversättning: Ordlista sökning

Tillhandahåller alternativa översättningar för ett ord och ett litet antal idiomatiskt fraser. Varje översättning har en del av tal- och en lista över tillbaka översättningar. Tillbaka-översättningar att en användare kan förstå översättningen i kontexten. Den [ordlista exempel](.\v3-0-dictionary-examples.md) åtgärden tillåter ytterligare detaljnivån finns i exemplet används varje par för översättning.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna som skickades mot frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td>*Obligatoriska parametern*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>från</td>
    <td>*Obligatoriska parametern*.<br/>Anger språket i indatatexten. Källspråk måste vara något av de [språk som stöds](.\v3-0-languages.md) ingår i den `dictionary` omfång.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Obligatoriska parametern*.<br/>Anger språket i utdata texten. Målspråket som måste vara något av de [språk som stöds](.\v3-0-languages.md) ingår i den `dictionary` omfång.</td>
  </tr>
</table>

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>_En auktorisering_<br/>_Rubrik_</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Se [tillgängliga alternativ för autentisering](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Innehållstyp</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Anger innehållstypen för nyttolasten. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Längden på det begärda innehållet.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>En klientgenererade GUID för unik identifiering på begäran. Du kan utelämna den här rubriken om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter som heter `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med en strängegenskap med namnet `Text`, som representerar perioden för sökning.

```json
[
    {"Text":"fly"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 100 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstext

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i Indatamatrisen. En resultatobjektet innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger formuläret normaliserade källa har löpt ut. Till exempel om begäran är ”JOHN”, att formuläret normaliserade ”john”. Innehållet i det här fältet blir indata till [lookup exempel](.\v3-0-dictionary-examples.md).
    
  * `displaySource`: En sträng som ger källa termen i form av en bäst passar för visning av slutanvändaren. Till exempel, om indata är ”JOHN”, visa formulär visas vanliga stavningen av namnet: ”John”. 

  * `translations`: En lista över översättningar för käll-period. Varje element i listan är ett objekt med följande egenskaper:

    * `normalizedTarget`: En sträng som ger normaliserade form av den här termen på språket som mål. Det här värdet ska användas som indata till [lookup exempel](.\v3-0-dictionary-examples.md).

    * `displayTarget`: En sträng som ger termen i målspråket och i ett formulär med bäst passar för visning av slutanvändaren. I allmänhet bör detta endast skiljer sig från den `normalizedTarget` när det gäller versaler. Till exempel ett egennamn som har ”Juan” `normalizedTarget = "juan"` och `displayTarget = "Juan"`.

    * `posTag`: En sträng som kopplar den här termen har en tagg för av ordklasser.

        | Taggnamn | Beskrivning  |
        |----------|--------------|
        | JUSTERBART      | Adjektiv   |
        | ADV      | Adverb      |
        | CONJ     | Konjunktioner |
        | DET      | Determiners  |
        | MODAL    | Verb        |
        | SUBSTANTIV     | Substantiv        |
        | FÖRBERED     | Prepositioner |
        | PRON     | Pronomen     |
        | VERB     | Verb        |
        | ANDRA    | Annat        |

        Dessa taggar har bestäms av en del av tal-märkning engelska sida och sedan vidta vanligaste taggen för varje källa/mål-par som en implementering anteckning. Så om personer översätta ofta ett spanskt ord till en annan del av tal-tagg på engelska, kan taggar få som är fel (med avseende på spanskt ord).

    * `confidence`: Ett värde mellan 0,0 och 1,0 som representerar ”förtroende” (eller kanske mer korrekt, ”sannolikhet i träningsdata”) av den translation-par. Summan av förtroende poäng för en datakälla word kan eller inte kan summeras till 1.0. 

    * `prefixWord`: En sträng som ger ordet ska visas som ett prefix på översättningen. Detta är för närvarande gendered determiner av substantiv på språk som har gendered determiners. Prefixet för spanska ordet ”mosca” är till exempel ”la”, eftersom ”mosca” är en feminint substantiv på spanska. Detta är bara beroende på översättningen och inte på källan. Om det finns inget prefix, blir det en tom sträng.
    
    * `backTranslations`: En lista över ”bakåt översättningar” för mål. Till exempel datakällan ord som mål kan innebära. Listan kommer att innehålla ordet källa som begärdes (t.ex. Om källan word som söktes är ”är det Formlig” så är det säkert att ”är det Formlig” ska ingå i den `backTranslations` listan). Men det är inte säkert att finnas i den första positionen och kan ofta inte. Varje element i den `backTranslations` listan är ett objekt som beskrivs i följande egenskaper:

        * `normalizedText`: En sträng som ger normaliserade form av källkod termen som är en tillbaka-översättning av målet. Det här värdet ska användas som indata till [lookup exempel](.\v3-0-dictionary-examples.md).        

        * `displayText`: En sträng som ger källa termen som är en tillbaka-översättning av målet i ett formulär som bäst passar för visning av slutanvändaren.

        * `numExamples`: Ett heltal som representerar antalet exempel som är tillgängliga för den här translation-par. Verkliga exempel måste hämtas med ett separat anrop till [lookup exempel](.\v3-0-dictionary-examples.md). Numret är främst avsedd för att underlätta visas i en UX. Ett användargränssnitt kan till exempel lägga till en hyperlänk till tillbaka-översättning om antalet av exemplen är större än noll och visa tillbaka-översättningen som oformaterad text om det finns inga exempel. Observera att det faktiska antalet exempel som returneras av ett anrop till [lookup exempel](.\v3-0-dictionary-examples.md) kan vara mindre än `numExamples`eftersom ytterligare filtrering kan tillämpas direkt att ta bort ”dåliga” exempel.
        
        * `frequencyCount`: Ett heltal som representerar frekvensen för den här translation par i data. Det huvudsakliga syftet med det här fältet är att tillhandahålla ett sätt att sortera tillbaka översättningar så att de vanligaste villkoren är första ett användargränssnitt.

    > [!NOTE]
    > Om termen som tittat upp inte finns i ordlistan, svaret är 200 (OK) men `translations` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du leta upp alternativa översättningar på spanska engelska har löpt ut `fly` .

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Svarstexten (förkortat för tydlighetens skull) är:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Det här exemplet visar vad som händer när termen som du söker efter inte finns för giltiga ordlista paret.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Eftersom termen inte hittas i ordlistan svarstexten innehåller en tom `translations` lista.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
