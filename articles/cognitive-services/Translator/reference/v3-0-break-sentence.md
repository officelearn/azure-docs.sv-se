---
title: Microsoft Translator Text API BreakSentence metoden | Microsoft Docs
description: Använd Microsoft översättare Text API BreakSentence-metoden.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 8ce6644d21b397ea0e7f2e71e3c3a5a96638eec5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354981"
---
# <a name="text-api-30-breaksentence"></a>Text API 3.0: BreakSentence

Anger placeringen av meningen gränser i en del av texten.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrar som skickas på frågesträngen är för begäran:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-version</td>
    <td>*Krävs frågeparameter*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>Språk</td>
    <td>*Valfria frågeparameter*.<br/>Språk-tagg som identifierar språket för indatatexten. Om en kod anges tillämpas automatisk identifiering av språk.</td>
  </tr>
  <tr>
    <td>skriptet</td>
    <td>*Valfria frågeparameter*.<br/>Skripttypen identifierar skriptet som används av indatatexten. Om ett skript inte anges, antas standardskript för språket.</td>
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
    <td>*Valfritt*.<br/>En klient-genererade GUID som unik identifierare för begäran. Observera att du kan hoppa över detta huvud om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement i är en JSON-objekt med en string-egenskap med namnet `Text`. Meningen gränser beräknas för värdet för den `Text` egenskapen. En exempel brödtext i begäran med ett objekts ser ut precis som:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Följande begränsningar:

* Matrisen kan ha högst 100 element.
* Textvärdet för ett matriselement får inte överstiga 10 000 tecken inklusive blanksteg.
* Hela texten ingår i denna begäran får inte överskrida 50 000 tecken inklusive blanksteg.
* Om den `language` frågeparameter anges, kommer alla matriselement måste vara på samma språk. Annars tillämpas språk automatisk identifiering på varje matriselement i oberoende av varandra.

## <a name="response-body"></a>Svarstexten

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i matrisen indata. En resultatobjekt innehåller följande egenskaper:

  * `sentLen`: En matris med heltal som representerar längder i meningar i text-elementet. Längden på matrisen är antalet meningar och värdena är längden på varje mening. 

  * `detectedLanguage`: Ett objekt som beskriver identifierade språket via följande egenskaper:

     * `language`: Koden för det identifierade språket.

     * `score`: Ett flyttal-värde som anger förtroende i resultatet. Poängen är mellan noll och ett och en låg poäng anger en låg förtroende.
     
    Observera att den `detectedLanguage` egenskapen finns bara i resultatobjektet när automatisk identifiering av språk som begärs.

Är ett exempel JSON-svar:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Svarshuvud

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som skapas av tjänsten för att identifiera förfrågan. Den används för felsökning.</td>
  </tr>
</table> 

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

I följande exempel visas hur du skaffar meningen gränser för en mening. Språket meningen identifieras automatiskt av tjänsten.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

