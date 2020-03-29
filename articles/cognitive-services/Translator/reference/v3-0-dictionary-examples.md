---
title: Exempelmetod för translator text API-ordlista
titleSuffix: Azure Cognitive Services
description: Metoden Translator Text API Dictionary Examples innehåller exempel som visar hur termer i ordlistan används i kontext.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548075"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Ordlista Exempel

Innehåller exempel som visar hur termer i ordlistan används i kontext. Den här åtgärden används tillsammans med [Dictionary-sökning](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

| Frågeparameter | Beskrivning |
| --------- | ----------- |
| api-version <img width=200/> | **Obligatorisk parameter**.<br/>Version av API som begärs av klienten. Värdet måste `3.0`vara . |
| Från | **Obligatorisk parameter**.<br/>Anger språket för indatatexten. Källspråket måste vara ett av de `dictionary` språk som [stöds](./v3-0-languages.md) i omfånget. |
| till | **Obligatorisk parameter**.<br/>Anger utdatatextens språk. Målspråket måste vara ett av de `dictionary` språk som [stöds](./v3-0-languages.md) i omfånget.  | 

Begäran rubriker inkluderar:

| Rubriker  | Beskrivning |
| ------ | ----------- |
| Autentiseringshuvuden <img width=200/>  | **Obligatoriskt begäranhuvud**.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Obligatoriskt begäranhuvud**.<br/>Anger innehållstypen för nyttolasten. Möjliga värden `application/json`är: . |
| Innehållslängd   | **Obligatoriskt begäranhuvud**.<br/>Längden på begäran kroppen. |
| X-ClientTraceId   | **Valfritt**.<br/>Ett klientgenererat GUID för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av frågeparametern `ClientTraceId`. |

## <a name="request-body"></a>Begärandetext

Brödtexten för begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med följande egenskaper:

  * `Text`: En sträng som anger termen att söka efter. Detta bör vara värdet `normalizedText` för ett fält från bakåtöversättningarna för en tidigare [uppslagsbegäran för ordlista.](./v3-0-dictionary-lookup.md) Det kan också vara `normalizedSource` värdet på fältet.

  * `Translation`: En sträng som anger den översatta texten som tidigare returnerats av [uppslagningen av ordlistan.](./v3-0-dictionary-lookup.md) Detta bör vara värdet `normalizedTarget` från `translations` fältet i listan över [uppslagssvaret för ordlista.](./v3-0-dictionary-lookup.md) Tjänsten returnerar exempel för det specifika ordparet för källmål.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överstiga 100 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger den normaliserade formen av källtermen. I allmänhet bör detta vara identiskt med värdet för `Text` fältet vid det matchande listindexet i brödtexten för begäran.
    
  * `normalizedTarget`: En sträng som ger den normaliserade formen av måltermen. I allmänhet bör detta vara identiskt med värdet för `Translation` fältet vid det matchande listindexet i brödtexten för begäran.
  
  * `examples`: En lista med exempel för paret (källterm, målterm). Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: Strängen som ska _before_ sammanfogas `sourceTerm` för värdera av att bilda ett färdigt exempel. Lägg inte till ett blanksteg, eftersom det redan finns där när det ska vara. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: En sträng som motsvarar den faktiska termen tittade upp. Strängen läggs `sourcePrefix` till `sourceSuffix` med och för att skapa ett fullständigt exempel. Dess värde separeras så att det kan markeras i ett användargränssnitt, t.ex.

    * `sourceSuffix`: Strängen som ska _sammanfogas_ `sourceTerm` efter värdet av att bilda ett färdigt exempel. Lägg inte till ett blanksteg, eftersom det redan finns där när det ska vara. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: En sträng `sourcePrefix` som liknar men för målet.

    * `targetTerm`: En sträng `sourceTerm` som liknar men för målet.

    * `targetSuffix`: En sträng `sourceSuffix` som liknar men för målet.

    > [!NOTE]
    > Om det inte finns några exempel i ordlistan är svaret `examples` 200 (OK) men listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du slår upp exempel `fly` för paret `volar`som består av den engelska termen och dess spanska översättning .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Svarstexten (förkortad för tydlighetens skull) är:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
