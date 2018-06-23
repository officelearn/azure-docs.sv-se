---
title: Microsoft Translator Text API Transliterate metoden | Microsoft Docs
description: Använd Microsoft översättare Text API Transliterate-metoden.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354969"
---
# <a name="text-api-30-transliterate"></a>Text API 3.0: Transliterate

Konverterar text på ett språk från ett skript till ett annat skript.

## <a name="request-url"></a>Fråge-URL

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
    <td>Språk</td>
    <td>*Nödvändiga parametern*.<br/>Anger språket i texten som ska konverteras från ett skript till en annan. Möjliga språk visas i den `transliteration` scope som erhålls genom att fråga tjänsten för dess [språk som stöds](.\v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Nödvändiga parametern*.<br/>Anger det skript som används av indatatexten. Sökning [språk som stöds](.\v3-0-languages.md) med hjälp av den `transliteration` omfång, söka efter inkommande skript som är tillgängliga för det valda språket.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Nödvändiga parametern*.<br/>Anger det utgående skriptet. Sökning [språk som stöds](.\v3-0-languages.md) med hjälp av den `transliteration` omfång, att hitta utdata-skript som är tillgängliga för den valda kombinationen av inmatningsspråk indata skriptet.</td>
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

Brödtexten i begäran är en JSON-matris. Varje matriselement i är en JSON-objekt med en string-egenskap med namnet `Text`, som motsvarar strängen som ska konverteras.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Följande begränsningar:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 1 000 tecken inklusive blanksteg.
* Hela texten ingår i denna begäran får inte överskrida 5 000 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstexten

Ett lyckat svar är en JSON-matris med ett resultat för varje element i matrisen indata. En resultatobjekt innehåller följande egenskaper:

  * `text`: En sträng som är resultatet av en konvertering av Indatasträngen till det utgående skriptet.
  
  * `script`: En sträng som anger skriptet som används i utdata.

Är ett exempel JSON-svar:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

I följande exempel visas hur du konverterar två japanska strängar till Romanized japanska.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

JSON-nyttolast för begäran i det här exemplet:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Om du använder cUrl i ett kommandoradsfönster som inte stöder Unicode-tecken, vidta följande JSON-nyttolast och spara den till en fil med namnet `request.txt`. Se till att spara filen med `UTF-8` kodning.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
