---
title: Identifieringsmetod för översättaretext-API
titleSuffix: Azure Cognitive Services
description: Identifiera språket för en text med azure cognitive services translator text API Detect-metoden.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837302"
---
# <a name="translator-text-api-30-detect"></a>Översättare Text API 3.0: Identifiera

Identifierar språket i en textartikel.

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

<table width="100%">
  <th width="20%">Parameter för fråga</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td>*Obligatorisk parameter*.<br/>Version av API som begärs av klienten. Värdet måste `3.0`vara .</td>
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
    <td>*Obligatoriskt begäranhuvud*.<br/>Anger innehållstypen för nyttolasten. Möjliga värden `application/json`är: .</td>
  </tr>
  <tr>
    <td>Innehållslängd</td>
    <td>*Obligatoriskt begäranhuvud*.<br/>Längden på begäran kroppen.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>Ett klientgenererat GUID för att unikt identifiera begäran. Observera att du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av frågeparametern `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten för begäran är en JSON-matris. Varje matriselement är ett JSON-objekt `Text`med en strängegenskap med namnet . Språkidentifiering tillämpas på `Text` egenskapens värde. En exempelbegäran ser ut så där:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Textvärdet för ett matriselement får inte överstiga 10 000 tecken, inklusive blanksteg.
* Hela texten som ingår i begäran får inte överstiga 50 000 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `language`: Koden för det upptäckta språket.

  * `score`: Ett flytvärde som anger förtroendet för resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett lågt självförtroende.

  * `isTranslationSupported`: Ett booleskt värde som är sant om det identifierade språket är ett av de språk som stöds för textöversättning.

  * `isTransliterationSupported`: Ett booleskt värde som är sant om det upptäckta språket är ett av de språk som stöds för translitterering.
  
  * `alternatives`: En rad andra möjliga språk. Varje element i matrisen är ett annat objekt `language` `score`med `isTranslationSupported` `isTransliterationSupported`samma egenskaper som anges ovan: , och .

Ett exempel på JSON-svar är:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används för felsökning.</td>
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
    <td>429</td>
    <td>Servern avvisade begäran eftersom klienten har överskridit begärangränser.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för `X-RequestId`felet, begärandeidentifierare från svarshuvudet och klientidentifierare från begäranden `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Försök igen. Om felet kvarstår rapporterar du det med: datum och tid för `X-RequestId`felet, begärandeidentifierare från svarshuvudet och klientidentifierare från begäranden `X-ClientTraceId`.</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran också ett JSON-felsvar. Felkoden är ett 6-siffrigt nummer som kombinerar den tresiffriga HTTP-statuskoden följt av ett tresiffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referenssidan för v3 Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

I följande exempel visas hur du hämtar språk som stöds för textöversättning.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
