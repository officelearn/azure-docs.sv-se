---
title: Translator Text API BreakSentence metod
titlesuffix: Azure Cognitive Services
description: Använd metoden Translator Text API BreakSentence.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 1202d49688bfd6aee50d1fa21c10423c071c6d92
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124991"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Anger placeringen av mening gränser i en del av texten.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna som skickades mot frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td>*Krävs Frågeparametern*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>Språk</td>
    <td>*Valfritt Frågeparametern*.<br/>Språktaggen som identifierar språket i indatatexten. Om en kod inte anges används automatisk språkidentifiering.</td>
  </tr>
  <tr>
    <td>skriptet</td>
    <td>*Valfritt Frågeparametern*.<br/>Skripttypen identifiera skript som används av indatatexten. Om ett skript inte anges, antas standardskript för språket.</td>
  </tr>
</table> 

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Rubriker</th>
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
    <td>*Valfritt*.<br/>En klientgenererade GUID för unik identifiering på begäran. Observera att du kan utelämna den här rubriken om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter som heter `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med en strängegenskap med namnet `Text`. Mening gränser beräknas för värdet för den `Text` egenskapen. En exempel-begärandetext med en del av texten ser ut som den:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Textvärdet för ett matriselement får inte överstiga 10 000 tecken inklusive blanksteg.
* Hela texten i begäran får inte överstiga 50 000 tecken inklusive blanksteg.
* Om den `language` Frågeparametern anges, kommer alla matriselement måste vara på samma språk. I annat fall tillämpas automatisk språkidentifiering-på varje matriselement oberoende av varandra.

## <a name="response-body"></a>Svarstext

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i Indatamatrisen. En resultatobjektet innehåller följande egenskaper:

  * `sentLen`: En matris med heltal som representerar längder i meningar i text-elementet. Längden på matrisen är antalet meningar och värdena är längden på varje mening. 

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket via följande egenskaper:

     * `language`: Kod av identifierat språk.

     * `score`: Ett flyttalsvärde som anger förtroende i resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett låga förtroende.
     
    Observera att den `detectedLanguage` egenskapen finns bara i resultatobjektet när automatisk identifiering av språk som efterfrågas.

Ett exempel JSON-svar är:

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
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används för felsökning.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svarsstatuskoder

Här följer möjliga HTTP-statuskoder som returnerar en begäran. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltig. Korrigera parametrarna innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna är angivna och giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran har inte behörighet. Finns information om felmeddelandet. Detta innebär ofta att alla kostnadsfria översättningar som medföljer en utvärderingsprenumeration har förbrukats.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Anroparen skickar för många förfrågningar.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Exempel

I följande exempel visas hur du skaffar mening gränser för en enskild mening. Språket i meningen identifieras automatiskt av tjänsten.

# <a name="curltabcurl"></a>[CURL](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

