---
title: Metod för upplösning av translatortext-API
titleSuffix: Azure Cognitive Services
description: Metoden Translator Text API BreakSentence identifierar placeringen av meningsgränser i en textartikel.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548126"
---
# <a name="translator-text-api-30-breaksentence"></a>Översättare Text API 3.0: BreakSentence

Identifierar placeringen av meningsgränser i en textartikel.

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

| Frågeparameter | Beskrivning |
| -------| ----------- |
| api-version <img width=200/>   | **Obligatorisk frågeparameter**.<br/>Version av API som begärs av klienten. Värdet måste `3.0`vara . |
| language | **Valfri frågeparameter**.<br/>Språktagg som identifierar språket i indatatexten. Om ingen kod har angetts tillämpas automatisk språkidentifiering. |
| -skriptet    | **Valfri frågeparameter**.<br/>Skripttaggen som identifierar skriptet som används av indatatexten. Om inget skript anges antas språkets standardskript.  | 

Begäran rubriker inkluderar:

| Rubriker | Beskrivning |
| ------- | ----------- |
| Autentiseringshuvuden <img width=200/>  | **Obligatoriskt begäranhuvud**.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Obligatoriskt begäranhuvud**.<br/>Anger innehållstypen för nyttolasten. Möjliga värden `application/json`är: . |
| Innehållslängd    | **Obligatoriskt begäranhuvud**.<br/>Längden på begäran kroppen.  | 
| X-ClientTraceId   | **Valfritt**.<br/>Ett klientgenererat GUID för att unikt identifiera begäran. Observera att du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av frågeparametern `ClientTraceId`.  | 

## <a name="request-body"></a>Begärandetext

Brödtexten för begäran är en JSON-matris. Varje matriselement är ett JSON-objekt `Text`med en strängegenskap med namnet . Meningsgränser beräknas för egenskapens `Text` värde. En exempelbegärandet brödtext med en textsbit ser ut så där:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Textvärdet för ett matriselement får inte överstiga 10 000 tecken, inklusive blanksteg.
* Hela texten som ingår i begäran får inte överstiga 50 000 tecken, inklusive blanksteg.
* Om `language` frågeparametern har angetts måste alla matriselement vara på samma språk. Annars används automatisk identifiering av språk på varje matriselement oberoende av varandra.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `sentLen`: En matris med heltal som representerar längden på meningarna i textelementet. Matrisens längd är antalet meningar och värdena är längden på varje mening. 

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket genom följande egenskaper:

     * `language`: Koden för det upptäckta språket.

     * `score`: Ett flytvärde som anger förtroendet för resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett lågt självförtroende.
     
    Observera att `detectedLanguage` egenskapen bara finns i resultatobjektet när automatiskt språkidentifiering begärs.

Ett exempel på JSON-svar är:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
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

Följande exempel visar hur du får meningsgränser för en enda mening. Språket i meningen identifieras automatiskt av tjänsten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

