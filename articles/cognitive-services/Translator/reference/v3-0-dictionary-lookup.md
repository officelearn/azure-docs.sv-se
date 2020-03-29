---
title: Uppslagsmetod för translator text API-ordlista
titleSuffix: Azure Cognitive Services
description: Metoden Dictionary Lookup innehåller alternativa översättningar för ett ord och ett litet antal idiomatiska fraser.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548959"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Kontrolltext-API 3.0: Uppslag på ordlistan

Ger alternativa översättningar för ett ord och ett litet antal idiomatiska fraser. Varje översättning har en del av tal och en lista över back-översättningar. Bakåtöversättningarna gör det möjligt för en användare att förstå översättningen i sitt sammanhang. Med åtgärden [Ordlisteexempel](./v3-0-dictionary-examples.md) kan du öka detaljnivån ytterligare för att se exempel på användning av varje översättningspar.

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

| Frågeparameter  | Beskrivning |
| ------ | ----------- |
| api-version <img width=200/>   | **Obligatorisk parameter**.<br/>Version av API som begärs av klienten. Värdet måste vara`3.0` |
| Från | **Obligatorisk parameter**.<br/>Anger språket för indatatexten. Källspråket måste vara ett av de `dictionary` språk som [stöds](./v3-0-languages.md) i omfånget. |
| till   | **Obligatorisk parameter**.<br/>Anger utdatatextens språk. Målspråket måste vara ett av de `dictionary` språk som [stöds](v3-0-languages.md) i omfånget. |


Begäran rubriker inkluderar:

| Rubriker  | Beskrivning |
| ------ | ----------- |
| Autentiseringshuvuden <img width=200/>  | **Obligatoriskt begäranhuvud**.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Obligatoriskt begäranhuvud**.<br/>Anger innehållstypen för nyttolasten. Möjliga värden `application/json`är: . |
| Innehållslängd   | **Obligatoriskt begäranhuvud**.<br/>Längden på begäran kroppen. |
| X-ClientTraceId   | **Valfritt**.<br/>Ett klientgenererat GUID för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av frågeparametern `ClientTraceId`. |

## <a name="request-body"></a>Begärandetext

Brödtexten för begäran är en JSON-matris. Varje matriselement är ett JSON-objekt `Text`med en strängegenskap med namnet , som representerar termen att söka.

```json
[
    {"Text":"fly"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överstiga 100 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger den normaliserade formen av källtermen. Om begäran till exempel är "JOHN" blir den normaliserade formen "john". Innehållet i det här fältet blir indata till [uppslagsexempel](./v3-0-dictionary-examples.md).
    
  * `displaySource`: En sträng som ger källtermen i ett formulär som passar bäst för slutanvändarens visning. Om indata till exempel är "JOHN" återspeglar visningsformuläret den vanliga stavningen av namnet: "John". 

  * `translations`: En lista över översättningar för källtermen. Varje element i listan är ett objekt med följande egenskaper:

    * `normalizedTarget`: En sträng som ger den normaliserade formen av denna term i målspråket. Det här värdet ska användas som indata för att [söka efter exempel](./v3-0-dictionary-examples.md).

    * `displayTarget`: En sträng som ger termen i målspråket och i en form som passar bäst för slutanvändarens visning. I allmänhet kommer detta `normalizedTarget` bara att skilja sig från när det gäller kapitalisering. Till exempel kommer ett riktigt substantiv som "Juan" har `normalizedTarget = "juan"` och `displayTarget = "Juan"`.

    * `posTag`: En sträng som associerar den här termen med en del av taltaggen.

        | Taggnamn | Beskrivning  |
        |----------|--------------|
        | Adj      | Adjektiv   |
        | Adv      | Adverb      |
        | CONJ (PÅ ANDRA)     | Konjunktioner |
        | DET (på andra)      | Beslutsfattare  |
        | Modala    | Verb        |
        | Substantiv     | Substantiv        |
        | Prep     | Prepositioner |
        | Pron     | Pronomen     |
        | Verb     | Verb        |
        | Andra    | Annat        |

        Som en implementeringsnota bestämdes dessa taggar av en del av taltaggningen på den engelska sidan och tog sedan den vanligaste taggen för varje käll-/målpar. Så om människor ofta översätta ett spanskt ord till en annan del av tal tagg på engelska, taggar kan hamna fel (med avseende på det spanska ordet).

    * `confidence`: Ett värde mellan 0,0 och 1,0 som representerar "konfidens" (eller kanske mer exakt, "sannolikhet i träningsdata") för översättningsparet. Summan av konfidenspoäng för ett källord kan eller inte kan summeras till 1,0. 

    * `prefixWord`: En sträng som ger ordet att visa som ett prefix för översättningen. För närvarande är detta den könsrelaterade determiner av substantiv, på språk som har könsrelaterade beslutsfattare. Till exempel är prefixet för det spanska ordet "mosca" "la", eftersom "mosca" är ett feminint substantiv på spanska. Detta är bara beroende av översättningen, och inte på källan. Om det inte finns något prefix är det den tomma strängen.
    
    * `backTranslations`: En lista över "tillbaka översättningar" av målet. Till exempel källord som målet kan översätta till. Listan är garanterad att innehålla källordet som begärdes (t.ex. om källordet som tittades upp är "fluga", då är det garanterat att "flyga" kommer att finnas i `backTranslations` listan). Det är dock inte garanterat att vara i den första positionen, och ofta inte kommer att vara. Varje element `backTranslations` i listan är ett objekt som beskrivs av följande egenskaper:

        * `normalizedText`: En sträng som ger den normaliserade formen av källtermen som är en bakåtöversättning av målet. Det här värdet ska användas som indata för att [söka efter exempel](./v3-0-dictionary-examples.md).        

        * `displayText`: En sträng som ger källtermen som är en bakåtöversättning av målet i en form som är bäst lämpad för slutanvändarens visning.

        * `numExamples`: Ett heltal som representerar antalet exempel som är tillgängliga för det här översättningsparet. Faktiska exempel måste hämtas med ett separat anrop till [uppslagsexempel](./v3-0-dictionary-examples.md). Numret är oftast avsett att underlätta visning i en UX. Ett användargränssnitt kan till exempel lägga till en hyperlänk till bakåtöversättningen om antalet exempel är större än noll och visar bakåtöversättningen som oformaterad text om det inte finns några exempel. Observera att det faktiska antalet exempel som returneras av ett `numExamples`anrop till [uppslagsexempel](./v3-0-dictionary-examples.md) kan vara mindre än , eftersom ytterligare filtrering kan användas i farten för att ta bort "dåliga" exempel.
        
        * `frequencyCount`: Ett heltal som representerar frekvensen för detta översättningspar i data. Huvudsyftet med detta område är att tillhandahålla ett användargränssnitt med ett sätt att sortera bakåtöversättningar så att de vanligaste termerna är först.

    > [!NOTE]
    > Om termen som visas inte finns i ordlistan är svaret 200 `translations` (OK) men listan är en tom lista.

## <a name="examples"></a>Exempel

I det här exemplet visas hur du slår `fly` upp alternativa översättningar på spanska av den engelska termen .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Svarstexten (förkortad för tydlighetens skull) är:

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

Det här exemplet visar vad som händer när termen som visas inte finns för det giltiga ordlisteparet.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Eftersom termen inte finns i ordlistan innehåller svarstexten en tom `translations` lista.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
