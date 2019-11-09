---
title: Translator Text API BreakSentence-metod
titleSuffix: Azure Cognitive Services
description: Metoden Translator Text API BreakSentence identifierar placeringen av menings gränser i en text del.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: b4eb083b0f98112274a5d00631af8662ff5c063a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835893"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3,0: BreakSentence

Anger placeringen av menings gränser i en text del.

## <a name="request-url"></a>URL för begäran

Skicka en `POST`-begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-version</td>
    <td>*Parameter för obligatorisk fråga*.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Valfri frågeparameter*.<br/>Språk tag gen som identifierar språk för inmatad text. Om ingen kod anges används automatisk språk identifiering.</td>
  </tr>
  <tr>
    <td>-skriptet</td>
    <td>*Valfri frågeparameter*.<br/>Skript tag gen som identifierar skriptet som används av inmatad text. Om ett skript inte anges kommer standard språket för språket att antas.</td>
  </tr>
</table> 

Begärandehuvuden innehåller:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Authentication-huvud (er)</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Begär ande huvud för begäran*.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td>*Begär ande huvud för begäran*.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Observera att du kan utelämna detta sidhuvud om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med en sträng egenskap med namnet `Text`. Menings gränser beräknas för värdet för egenskapen `Text`. En exempel begär ande text med en text ruta ser ut så här:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 100 element.
* Text värden för ett mat ris element får inte överstiga 10 000 tecken inklusive blank steg.
* Hela texten som ingår i begäran får inte överstiga 50 000 tecken inklusive blank steg.
* Om parametern `language` Query anges måste alla mat ris element vara på samma språk. Annars används automatisk identifiering av språk för varje mat ris element oberoende av varandra.

## <a name="response-body"></a>Svars text

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `sentLen`: en matris med heltal som representerar längden på meningarna i text elementet. Matrisens längd är antalet meningar och värdena är längden på varje mening. 

  * `detectedLanguage`: ett objekt som beskriver det identifierade språket via följande egenskaper:

     * `language`: kod för identifierat språk.

     * `score`: ett flyt värde som indikerar förtroendet i resultatet. Poängen är mellan noll och en och en låg poäng indikerar en låg exakthet.
     
    Observera att egenskapen `detectedLanguage` endast visas i result-objektet när språk automatisk identifiering begärs.

Ett exempel på JSON-svar är:

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

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används i fel söknings syfte.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar. 

<table width="100%">
  <th width="20%">Status kod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrar saknas eller är ogiltig. Korrigera parametrarna för begäran innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrol lera att autentiseringsuppgifterna har angetts och är giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran är inte auktoriserad. Se fel meddelandet information. Detta indikerar ofta att alla kostnads fria översättningar som ingår i en utvärderings prenumeration har använts.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för felet, begär ande identifierare från svars huvud `X-RequestId`och klient-ID: n från begärans huvud `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår rapporterar du det med: datum och tid för felet, begär ande identifierare från svars huvud `X-RequestId`och klient-ID: n från begärans huvud `X-ClientTraceId`.</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran även ett JSON-felsvar. Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referens sidan för v3-Translator text API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

I följande exempel visas hur du hämtar menings gränser för en enda mening. Språket i meningen identifieras automatiskt av tjänsten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

