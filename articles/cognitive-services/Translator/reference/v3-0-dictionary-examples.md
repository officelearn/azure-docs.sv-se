---
title: Microsoft Translator Text API ordlista exempel metoden | Microsoft Docs
description: Använd metoden Microsoft översättare Text API ordlista exempel.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9960f3be42090edaec1df935d70e4c1a0d25b691
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354978"
---
# <a name="text-api-30-dictionary-examples"></a>Text API 3.0: Exempel ordlista

Innehåller exempel som visar hur villkor i ordlistan används i kontexten. Den här åtgärden används tillsammans med [ordlista sökning](.\v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
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

Brödtexten i begäran är en JSON-matris. Varje matriselement i är en JSON-objekt med följande egenskaper:

  * `Text`: En sträng som anger termen för sökning. Detta ska värdet för en `normalizedText` från tillbaka-översättningar av ett tidigare [ordlista sökning](.\v3-0-dictionary-lookup.md) begäran. Det kan också vara värdet för den `normalizedSource` fältet.

  * `Translation`: En sträng som anger den översatta texten som tidigare returnerats av den [ordlista sökning](.\v3-0-dictionary-lookup.md) igen. Detta bör vara värdet från den `normalizedTarget` i den `translations` lista över de [ordlista sökning](.\v3-0-dictionary-lookup.md) svar. Tjänsten returnerar exempel för specifika käll-mål word-par.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 100 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstexten

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i matrisen indata. En resultatobjekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger normaliserade form källa har löpt ut. I allmänhet bör detta vara identiskt med värdet för den `Text` fält med matchande ListIndex i brödtexten i begäran.
    
  * `normalizedTarget`: En sträng som ger normaliserade form mål har löpt ut. I allmänhet bör detta vara identiskt med värdet för den `Translation` fält med matchande ListIndex i brödtexten i begäran.
  
  * `examples`: En lista över exempel på (källa sikt måltermen) par. Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: Strängen att sammanfoga _innan_ värdet för `sourceTerm` för att bilda en komplett exempel. Lägg inte till ett blanksteg, eftersom det redan finns det när det ska vara. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: En sträng som motsvarar den faktiska termen slås upp. Strängen har lagts till med `sourcePrefix` och `sourceSuffix` för att bilda det fullständiga exemplet. Värdet är avgränsade så att den kan markeras i användargränssnittet, t.ex. med fetstil den.

    * `sourceSuffix`: Strängen att sammanfoga _när_ värdet för `sourceTerm` för att bilda en komplett exempel. Lägg inte till ett blanksteg, eftersom det redan finns det när det ska vara. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: En sträng som liknar `sourcePrefix` men för målet.

    * `targetTerm`: En sträng som liknar `sourceTerm` men för målet.

    * `targetSuffix`: En sträng som liknar `sourceSuffix` men för målet.

    > [!NOTE]
    > Om det inte finns några exempel i ordlistan, svaret är 200 (OK) men `examples` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du söka efter exempel för paret består av engelska termen `fly` och dess spanska översättning `volar`.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Svarstexten (förkortat för tydlighetens skull) är:

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
