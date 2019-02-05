---
title: Translator Text API språk metod
titlesuffix: Azure Cognitive Services
description: Använd metoden Translator Text API språk.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 1f7590b07ccb0af867680ce86bd1c3b042f8f7dd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694677"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: Languages

Hämtar antal språk som stöds för närvarande av andra åtgärder för Translator Text API. 

## <a name="request-url"></a>URL för begäran

Skicka en `GET` begäran om att:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna som skickades mot frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td>*Obligatoriska parametern*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>omfång</td>
    <td>*Valfri parameter*.<br/>En kommaavgränsad lista med namn som definierar gruppen med språk som ska returneras. Tillåtna namn är: `translation`, `transliteration` och `dictionary`. Om ingen omfattning ges så returneras alla grupper, vilket motsvarar skicka `scope=translation,transliteration,dictionary`. För att bestämma vilken uppsättning språk som stöds är lämpliga för ditt scenario, finns i beskrivningen av den [svarsobjekt](#response-body).</td>
  </tr>
</table> 

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Begärandehuvud i valfritt*.<br/>Språket som ska användas för användargränssnittssträngar. Vissa av fälten i svaret är namnen på språk eller namnen på regioner. Använd den här parametern för att ange språket där dessa namn returneras. Språket har angetts genom att tillhandahålla en korrekt strukturerad BCP-47 som språktagg. Till exempel använda värdet `fr` att begära namnen på franska eller använda värdet `zh-Hant` i begäran namn i traditionell kinesiska.<br/>Namn tillhandahålls på engelska när ett målspråk inte har angetts eller när lokalisering inte är tillgänglig.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Begärandehuvud i valfritt*.<br/>En klientgenererade GUID för unik identifiering på begäran.</td>
  </tr>
</table> 

Autentisering är inte nödvändigt att hämta språkresurser.

## <a name="response-body"></a>Svarstext

En klient använder de `scope` frågeparameter för att definiera vilka grupper av språk som den är intresserad av.

* `scope=translation` tillhandahåller språk som stöds för att översätta text från ett språk till ett annat språk.

* `scope=transliteration` tillhandahåller funktioner för att konvertera text på ett språk från ett skript till ett annat skript;

* `scope=dictionary` innehåller språkpar som `Dictionary` åtgärder att returnera data.

En klient kan hämta flera grupper samtidigt genom att ange en kommaavgränsad lista med namn. Till exempel `scope=translation,transliteration,dictionary` returnerar språk som stöds för alla grupper.

Ett lyckat svar är ett JSON-objekt med en egenskap för varje begärda grupp:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Värdet för varje egenskap är som följer.

* `translation` Egenskapen

  Värdet för den `translation` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP-47 som språktagg. En nyckel identifierar ett språk som text kan översättas till eller översättas från. Värdet kopplat till nyckeln är en JSON-objekt med egenskaper som beskriver språket:

  * `name`: Visningsnamnet för språket på det språk som begärs `Accept-Language` rubrik.

  * `nativeName`: Visningsnamnet för språket på språket som är interna för det här språket.

  * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger språk.

  Ett exempel är:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` Egenskapen

  Värdet för den `transliteration` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP-47 som språktagg. En nyckel identifierar ett språk som text kan konverteras från ett skript till ett annat skript. Värdet kopplat till nyckeln är en JSON-objekt med egenskaper som beskriver språket och dess stöds skript:

  * `name`: Visningsnamnet för språket på det språk som begärs `Accept-Language` rubrik.

  * `nativeName`: Visningsnamnet för språket på språket som är interna för det här språket.

  * `scripts`: Lista över skript för att konvertera från. Varje element i den `scripts` listan har egenskaper:

    * `code`: Kod som identifierar skriptet.

    * `name`: Visningsnamnet för skriptet på det språk som begärs `Accept-Language` rubrik.

    * `nativeName`: Visningsnamn för språket som visas på språket som är interna för språket.

    * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger språk.

    * `toScripts`: Lista över skript som är tillgängliga för att omvandla text till. Varje element i den `toScripts` listan har egenskaper `code`, `name`, `nativeName`, och `dir` enligt beskrivningen ovan.

  Ett exempel är:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` Egenskapen

  Värdet för den `dictionary` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP-47 som språktagg. Nyckeln identifierar ett språk som alternativa översättningar och tillbaka översättningar som är tillgängliga. Värdet är ett JSON-objekt som beskriver en källspråket och mål-språk med tillgängliga översättningar:

  * `name`: Visningsnamnet för källspråk på det språk som begärs `Accept-Language` rubrik.

  * `nativeName`: Visningsnamnet för språket på språket som är interna för det här språket.

  * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger språk.

  * `translations`: Lista med alternativt översättningar och exempel för frågan som uttrycks i källspråket. Varje element i den `translations` listan har egenskaper:

    * `name`: Visningsnamnet för målspråk på det språk som begärs `Accept-Language` rubrik.

    * `nativeName`: Visningsnamn för målspråket på språket som är interna för mål-språk.

    * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger språk.
    
    * `code`: Språkkoden som identifierar målspråket.

  Ett exempel är:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Strukturen för objektet response ändras inte utan en ändring i API-versionen. För samma version av API: et, kan en lista med tillgängliga språk ändras över tid eftersom Microsoft Translator kontinuerligt utökar listan över språk som stöds av dess tjänster.

Listan över språk som stöds kommer inte ändras ofta. Spara nätverksbandbredd och förbättra svarstiden genom ett klientprogram bör cachelagring språkresurser och motsvarande enhetstagg (`ETag`). Sedan klientprogrammet kan med jämna mellanrum (till exempel en gång per dygn) skicka frågor till tjänsten för att hämta den senaste uppsättningen språk som stöds. Skicka aktuellt `ETag` värde i en `If-None-Match` huvudfältet gör att tjänsten för att optimera svaret. Om resursen inte har ändrats, returnerar tjänsten statuskod 304 och en tom svarstext.

## <a name="response-headers"></a>Svarshuvud

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>ETag</td>
    <td>Aktuella värdet för entitetstagg för de begärda grupperna med språk som stöds. Om du vill göra efterföljande förfrågningar effektivare klienten kan skicka den `ETag` värde i en `If-None-Match` huvudfältet.
    </td>
  </tr>
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
    <td>304</td>
    <td>Resursen har inte ändrats sedan den versionen som anges av begärandehuvuden `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltig. Korrigera parametrarna innan du försöker igen.</td>
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

Om ett fel inträffar, returneras också en JSON-felsvar i begäran. Felkoden är en 6-siffrig nummer kombinera 3-siffriga HTTP-statuskoden följt av ett 3-siffriga tal till ytterligare kategorisera felet. Vanliga felkoder finns på den [v3 Translator Text API-referenssida](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

I följande exempel visas hur du hämtar språk som stöds för textöversättning.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
