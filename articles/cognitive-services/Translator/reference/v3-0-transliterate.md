---
title: Översättare Text API Translitterera metod
titleSuffix: Azure Cognitive Services
description: Konvertera text på ett språk från ett skript till ett annat skript med metoden Translator Text API Transliterate.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837285"
---
# <a name="translator-text-api-30-transliterate"></a>Översättare Text API 3.0: Translitterera

Konverterar text på ett språk från ett skript till ett annat skript.

## <a name="request-url"></a>URL för begäran

Skicka `POST` en begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för den text som ska konverteras från ett skript till ett annat. Möjliga språk visas i `transliteration` det scope som erhålls genom att fråga tjänsten för dess [språk som stöds](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>frånSkript</td>
    <td>*Obligatorisk parameter*.<br/>Anger skriptet som används av indatatexten. Slå upp språk `transliteration` som [stöds](./v3-0-languages.md) med hjälp av scopet för att hitta indataskript som är tillgängliga för det valda språket.</td>
  </tr>
  <tr>
    <td>toScript (toScript)</td>
    <td>*Obligatorisk parameter*.<br/>Anger utdataskriptet. Slå upp språk `transliteration` som [stöds](./v3-0-languages.md) med hjälp av scopet för att hitta utdataskript som är tillgängliga för den valda kombinationen av inmatningsspråk och inmatningsskript.</td>
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

Brödtexten för begäran är en JSON-matris. Varje arrayelement är ett JSON-objekt `Text`med en strängegenskap med namnet , som representerar strängen som ska konverteras.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överstiga 1 000 tecken, inklusive blanksteg.
* Hela texten som ingår i begäran får inte överstiga 5 000 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje element i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `text`: En sträng som är resultatet av att du konverterar indatasträngen till utdataskriptet.
  
  * `script`: En sträng som anger skriptet som används i utdata.

Ett exempel på JSON-svar är:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

I följande exempel visas hur du konverterar två japanska strängar till romaniserad japanska.

JSON-nyttolasten för begäran i det här exemplet:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Om du använder cURL i ett kommandoradsfönster som inte stöder Unicode-tecken tar du följande `request.txt`JSON-nyttolast och sparar den i en fil med namnet . Var noga med att `UTF-8` spara filen med kodning.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
