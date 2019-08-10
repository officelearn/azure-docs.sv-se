---
title: Exempel metod för Translator Text API-ordlista
titleSuffix: Azure Cognitive Services
description: Använd exempel metoden Translator Text API Dictionary.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 419e11862b2c584686922cfc8d1db72ee4751a03
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934023"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3,0: Ordlisteexempel

Innehåller exempel som visar hur termer i ord listan används i kontexten. Den här åtgärden används i tandem med [ord](./v3-0-dictionary-lookup.md)lists ökning.

## <a name="request-url"></a>URL för begäran

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td>*Obligatorisk parameter*.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för inmatad text. Käll språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary` omfånget.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary` omfånget.</td>
  </tr>
</table>

Begärandehuvuden innehåller:

<table width="100%">
  <th width="20%">Huvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>Authentication-huvud (er)</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Innehållstyp</td>
    <td>*Begär ande huvud för begäran*.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td>*Begär ande huvud för begäran*.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp `ClientTraceId`av en frågeparameter med namnet.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med följande egenskaper:

  * `Text`: En sträng som anger den term som ska sökas. Detta bör vara värdet för ett `normalizedText` fält från back-translations för en föregående [ord söknings](./v3-0-dictionary-lookup.md) förfrågan. Det kan också vara värdet för `normalizedSource` fältet.

  * `Translation`: En sträng som anger den översatta text som tidigare returnerades av [ord listans Sök](./v3-0-dictionary-lookup.md) åtgärd. Detta ska vara värdet från `normalizedTarget` fältet `translations` i listan över Sök svaret för [ord listan](./v3-0-dictionary-lookup.md) . Tjänsten kommer att returnera exempel för det specifika käll målets Word-par.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 10 element.
* Text värden för ett mat ris element får inte överstiga 100 tecken inklusive blank steg.

## <a name="response-body"></a>Svars text

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger en normaliserad form av käll termen. I allmänhet ska detta vara identiskt med värdet för `Text` fältet i det matchande List indexet i bröd texten i begäran.
    
  * `normalizedTarget`: En sträng som ger en normaliserad form av mål termen. I allmänhet ska detta vara identiskt med värdet för `Translation` fältet i det matchande List indexet i bröd texten i begäran.
  
  * `examples`: En lista med exempel på paret (käll termen, mål termen). Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: Strängen som ska sammanfogas _innan_ värdet för `sourceTerm` ska utgöra ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: En sträng som motsvarar den faktiska term som har sökts upp. Strängen läggs till med `sourcePrefix` och `sourceSuffix` används för att skapa hela exemplet. Värdet är avgränsat så att det kan markeras i ett användar gränssnitt, t. ex. med fetstil.

    * `sourceSuffix`: Strängen som ska sammanfogas _efter_ värdet för `sourceTerm` att skapa ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: En sträng som `sourcePrefix` liknar men för målet.

    * `targetTerm`: En sträng som `sourceTerm` liknar men för målet.

    * `targetSuffix`: En sträng som `sourceSuffix` liknar men för målet.

    > [!NOTE]
    > Om det inte finns några exempel i ord listan är svaret 200 (OK), men `examples` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du söker efter exempel för paret som består av den engelska `fly` och dess Spanska Översättning `volar`.

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
