---
title: Exempel metod för Translator Text API-ordlista
titleSuffix: Azure Cognitive Services
description: Exempel metoden Translator Text API Dictionary innehåller exempel som visar hur termer i ord listan används i kontexten.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548075"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3,0: exempel på ord lista

Innehåller exempel som visar hur termer i ord listan används i kontexten. Den här åtgärden används i tandem med [ord](./v3-0-dictionary-lookup.md)lists ökning.

## <a name="request-url"></a>URL för begäran

Skicka en `POST`-begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

| Frågeparameter | Beskrivning |
| --------- | ----------- |
| api-version <img width=200/> | **Obligatorisk parameter**.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0`. |
| från | **Obligatorisk parameter**.<br/>Anger språket för inmatad text. Käll språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary`s omfånget. |
| till | **Obligatorisk parameter**.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary`s omfånget.  | 

Begärandehuvuden innehåller:

| Rubriker  | Beskrivning |
| ------ | ----------- |
| Authentication-huvud (er) <img width=200/>  | **Begär ande huvud för begäran**.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Begär ande huvud för begäran**.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json`. |
| Innehålls längd   | **Begär ande huvud för begäran**.<br/>Längden på begär ande texten. |
| X-ClientTraceId   | **Valfritt**.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId`. |

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med följande egenskaper:

  * `Text`: en sträng som anger den term som ska sökas. Detta bör vara värdet för ett `normalizedText` fält från back-translations för en tidigare [ord söknings](./v3-0-dictionary-lookup.md) förfrågan. Det kan också vara värdet för fältet `normalizedSource`.

  * `Translation`: en sträng som anger den översatta text som tidigare returnerades av [ord listans Sök](./v3-0-dictionary-lookup.md) åtgärd. Detta bör vara värdet från fältet `normalizedTarget` i listan `translations` i Sök svaret för [ord listan](./v3-0-dictionary-lookup.md) . Tjänsten kommer att returnera exempel för det specifika käll målets Word-par.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 10 element.
* Text värden för ett mat ris element får inte överstiga 100 tecken inklusive blank steg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `normalizedSource`: en sträng som ger en normaliserad form av käll termen. I allmänhet ska detta vara identiskt med värdet för fältet `Text` i det matchande List indexet i bröd texten i begäran.
    
  * `normalizedTarget`: en sträng som ger en normaliserad form av mål termen. I allmänhet ska detta vara identiskt med värdet för fältet `Translation` i det matchande List indexet i bröd texten i begäran.
  
  * `examples`: en lista över exempel för paret (käll termen, mål termen). Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: strängen som ska sammanfogas _innan_ värdet för `sourceTerm` för att bilda ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: en sträng som motsvarar den faktiska termen som har sökts upp. Strängen läggs till med `sourcePrefix` och `sourceSuffix` för att bilda det fullständiga exemplet. Värdet är avgränsat så att det kan markeras i ett användar gränssnitt, t. ex. med fetstil.

    * `sourceSuffix`: strängen som ska sammanfogas _efter_ värdet för `sourceTerm` för att skapa ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: en sträng som liknar `sourcePrefix` men för målet.

    * `targetTerm`: en sträng som liknar `sourceTerm` men för målet.

    * `targetSuffix`: en sträng som liknar `sourceSuffix` men för målet.

    > [!NOTE]
    > Om det inte finns några exempel i ord listan är svaret 200 (OK), men `examples` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du söker efter exempel på det par som består av den engelska termen `fly` och dess spanska översättnings `volar`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Svars texten (förkortat för tydlighetens skull) är:

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
