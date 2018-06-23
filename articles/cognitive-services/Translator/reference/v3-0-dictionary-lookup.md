---
title: Microsoft Translator Text API ordlista sökmetod | Microsoft Docs
description: Använd Microsoft översättare Text API ordlista sökmetod.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 31435fcfca61517bfc72d534e911a1dcadbee52b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354975"
---
# <a name="text-api-30-dictionary-lookup"></a>Text API 3.0: Ordlista sökning

Innehåller alternativa översättningar för en word och ett litet antal idiomatiska fraser. Varje översättning har en del-av-tal och en lista över tillbaka översättningar. Föregående-översättningar aktivera en användare att förstå översättning i kontexten. Den [ordlista exempel](.\v3-0-dictionary-examples.md) åtgärden tillåter ytterligare detaljnivån ned till finns i exemplet används varje översättning par.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>*Nödvändiga parametern*.<br/>Anger språket av indatatexten. Käll-språket måste vara något av de [språk som stöds](.\v3-0-languages.md) ingår i den `dictionary` omfång.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Nödvändiga parametern*.<br/>Anger språket i texten för utdata. Mål-språket måste vara något av de [språk som stöds](.\v3-0-languages.md) ingår i den `dictionary` omfång.</td>
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

Brödtexten i begäran är en JSON-matris. Varje matriselement i är en JSON-objekt med en string-egenskap med namnet `Text`, som representerar termen för sökning.

```json
[
    {"Text":"fly"}
]
```

Följande begränsningar:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 100 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstexten

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i matrisen indata. En resultatobjekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger normaliserade form källa har löpt ut. Till exempel om förfrågan ”JOHN” blir normaliserade form ”john”. Innehållet i det här fältet blir indata till [sökning exempel](.\v3-0-dictionary-examples.md).
    
  * `displaySource`: En sträng som ger bäst källa termen i ett formulär lämpligt för visning av slutanvändaren. Till exempel om indata är ”JOHN”, visas formuläret motsvarar vanliga stavningen av namnet: ”John”. 

  * `translations`: En lista över översättningar för källterm. Varje element i listan är ett objekt med följande egenskaper:

    * `normalizedTarget`: En sträng som ger normaliserade form av den här termen på språket som mål. Det här värdet ska användas som indata till [sökning exempel](.\v3-0-dictionary-examples.md).

    * `displayTarget`: En sträng som ger termen på mål-språk och i ett formulär bäst passar för visning av slutanvändaren. I allmänhet detta endast skiljer sig från den `normalizedTarget` vad gäller skiftläge. Till exempel ett giltigt substantiv som har ”Juan” `normalizedTarget = "juan"` och `displayTarget = "Juan"`.

    * `posTag`: En sträng som kopplar termen till en del av tal-tagg.

        | Taggnamn | Beskrivning  |
        |----------|--------------|
        | JUSTERBART      | Adjektiv   |
        | AV      | Adverb      |
        | CONJ     | Konjunktioner |
        | DET      | Determiners  |
        | SPÄRRAD    | Verb        |
        | SUBSTANTIV     | Substantiv        |
        | FÖRBERED     | Prepositioner |
        | PRON     | Pronomen     |
        | VERB     | Verb        |
        | ANDRA    | Annat        |

        Som en implementering anteckning bestämdes dessa taggar som en del av Läs upp taggning engelska sida och sedan tar den mest frekventa taggen för varje par källan/målet. Så om personer översätta ofta ett spanska ord till en annan del av tal-tagg på engelska, kan taggar sluta att fel (med avseende på spanska ordet).

    * `confidence`: Ett värde mellan 0,0 och 1,0 som representerar ”förtroende” (eller kanske mer exakt, ”sannolikhet i utbildning data”) för översättning paret. Summan av förtroende poängen för en datakälla word kan eller inte kan sum 1.0. 

    * `prefixWord`: En sträng som ger den ordet ska visas som ett prefix översättningens. Detta är för närvarande gendered determiner av substantiv i språk som har gendered determiners. Prefixet för spanska ordet ”mosca” är till exempel ”la”, eftersom ”mosca” är ett feminint substantiv på spanska. Detta är endast beroende på översättningen och inte på källan. Om det finns inget prefix, ska det vara en tom sträng.
    
    * `backTranslations`: En lista över ”bakåt översättningar” för målet. Till exempel källan ord som mål kan översätta till. Listan garanterat innehåller ordet källa som begärdes (t.ex. Om källan word som returnerades är ”föra” och sedan garanteras att ”föra” ska ingå i den `backTranslations` listan). Men det är inte säkert att vara den första positionen och ofta inte. Varje element i den `backTranslations` listan är ett objekt som beskrivs i följande egenskaper:

        * `normalizedText`: En sträng som ger normaliserade form löper källa som är en tillbaka översättning av målet. Det här värdet ska användas som indata till [sökning exempel](.\v3-0-dictionary-examples.md).        

        * `displayText`: En sträng som ger källterm som är en tillbaka översättning av målet i ett formulär som bäst passar för visning av slutanvändaren.

        * `numExamples`: Ett heltal som representerar antalet exempel som är tillgängliga för den här översättning par. Faktiska exempel måste hämtas med ett separat anrop till [sökning exempel](.\v3-0-dictionary-examples.md). Numret är främst avsett att underlätta visas i en UX. Ett användargränssnitt kan till exempel lägga till en hyperlänk i tillbaka-översättning om antalet exempel som är större än noll och visa tillbaka-översättning som oformaterad text om det inte finns några exempel. Observera att det faktiska antalet exempel som returneras av ett anrop till [sökning exempel](.\v3-0-dictionary-examples.md) kan vara mindre än `numExamples`eftersom ytterligare filtrering kan tillämpas direkt för att ta bort ”felaktig” exempel.
        
        * `frequencyCount`: Ett heltal som representerar frekvensen för detta översättning-par i data. Det huvudsakliga syftet med det här fältet är att tillhandahålla ett sätt att sortera tillbaka översättningar så att de vanligaste villkoren är första ett användargränssnitt.

    > [!NOTE]
    > Om villkoret som ska slås upp inte finns i ordlistan, svaret är 200 (OK) men `translations` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du söka efter alternativa översättningar spanska engelska har löpt ut `fly` .

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

Det här exemplet visar vad som händer när termen som du söker efter inte finns för giltig uppslagslista-par.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Eftersom termen inte hittades i ordlistan, svarstexten innehåller en tom `translations` lista.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
