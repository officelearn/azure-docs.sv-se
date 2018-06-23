---
title: Microsoft Translator Text API språk metoden | Microsoft Docs
description: Använd metoden Microsoft översättare Text API språk.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 93c06218a560faf439f05903438d021b372ce257
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354156"
---
# <a name="text-api-30-languages"></a>Text API 3.0: språk

Hämtar antal språk som stöds för närvarande av andra åtgärder av Text-API. 

## <a name="request-url"></a>Fråge-URL

Skicka en `GET` begäran om att:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
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
    <td>omfång</td>
    <td>*Valfri parameter*.<br/>En kommaavgränsad lista över namn som definierar gruppen med språk som ska returneras. Tillåtna namnen är: `translation`, `transliteration` och `dictionary`. Om ingen omfattning ges så returneras alla grupper, vilket motsvarar att passera `scope=translation,transliteration,dictionary`. För att bestämma vilken uppsättning språk som stöds för ditt scenario, finns i beskrivningen av den [objektet response](#response-body).</td>
  </tr>
</table> 

Huvuden för begäran är:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>Acceptera språk</td>
    <td>*Valfria begärandehuvudet*.<br/>Språket som ska användas för gränssnittet användarsträngar. Vissa fält i svaret är namnen på språk eller namn på områden. Använd den här parametern om du vill definiera språket som dessa namn returneras. Språket som har angetts genom att ange en giltig BCP 47 språk tagg. Till exempel använda värdet `fr` att begära namn på franska eller använda värdet `zh-Hant` i begäran namn i traditionell kinesiska.<br/>Namn tillhandahålls på engelska när ett språk inte har angetts eller lokalisering inte är tillgänglig.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfria begärandehuvudet*.<br/>En klient-genererade GUID som unik identifierare för begäran.</td>
  </tr>
</table> 

Autentisering är inte nödvändigt att hämta språkresurser.

## <a name="response-body"></a>Svarstexten

En klient använder de `scope` Frågeparametern för att definiera vilka grupper av språk som den är intresserad av.

* `scope=translation` ger språk som stöds för att översätta text från ett språk till ett annat språk.

* `scope=transliteration` tillhandahåller funktioner för konvertering av text på ett språk från ett skript till ett annat skript;

* `scope=dictionary` innehåller språkpar som `Dictionary` operations returnera data.

En klient kan hämta flera grupper samtidigt genom att ange en kommaavgränsad lista med namn. Till exempel `scope=translation,transliteration,dictionary` returnerar språk som stöds för alla grupper.

Ett lyckat svar är en JSON-objekt med en egenskap för varje begärda grupp:

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

  Värdet för den `translation` egenskapen är en dictionary (nyckel, värde) par. Varje nyckel är en BCP 47 språk tagg. En nyckel identifierar ett språk som text kan översättas till eller översättas från. Värdet som associeras med nyckeln är en JSON-objekt med egenskaper som beskriver språket:

  * `name`: Visningsnamnet för språket på det språk som begärdes `Accept-Language` huvud.

  * `nativeName`: Visa namnet för språket på språket som är interna för det här språket.

  * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger-språk.

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

  Värdet för den `transliteration` egenskapen är en dictionary (nyckel, värde) par. Varje nyckel är en BCP 47 språk tagg. En nyckel identifierar ett språk som text kan konverteras från ett skript till ett annat skript. Värdet som associeras med nyckeln är en JSON-objekt med egenskaper som beskriver språket och dess stöds skript:

  * `name`: Visningsnamnet för språket på det språk som begärdes `Accept-Language` huvud.

  * `nativeName`: Visa namnet för språket på språket som är interna för det här språket.

  * `scripts`: Lista över skript för att konvertera från. Varje element i den `scripts` listan har egenskaper:

    * `code`: Kod som identifierar skriptet.

    * `name`: Visningsnamn av skriptet på det språk som begärdes `Accept-Language` huvud.

    * `nativeName`: Visa namnet för språket på språket som är interna för språket.

    * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger-språk.

    * `toScripts`: Lista över skript som är tillgängliga för att konvertera text till. Varje element i den `toScripts` listan har egenskaper `code`, `name`, `nativeName`, och `dir` som tidigare beskrivits.

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

  Värdet för den `dictionary` egenskapen är en dictionary (nyckel, värde) par. Varje nyckel är en BCP 47 språk tagg. Nyckeln identifierar ett språk för vilka alternativa översättningar och tillbaka översättningar som är tillgängliga. Värdet är ett JSON-objekt som beskriver språk för källa och mål-språk med tillgängliga översättningar:

  * `name`: Visningsnamn för språket som källa på det språk som begärdes `Accept-Language` huvud.

  * `nativeName`: Visa namnet för språket på språket som är interna för det här språket.

  * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger-språk.

  * `translations`: Lista med alternativt översättningar och exempel för frågan som uttrycks i käll-språk. Varje element i den `translations` listan har egenskaper:

    * `name`: Visningsnamn målspråk på det språk som begärdes `Accept-Language` huvud.

    * `nativeName`: Visningsnamn målspråk på språket som är interna för språket som mål.

    * `dir`: Riktningen, vilket är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger-språk.
    
    * `code`: Språk kod som identifierar målspråket.

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

Strukturen för objektet response ändrar inte utan en ändring i versionen av API: et. Samma version av API: et, kan lista över tillgängliga språk ändras med tiden eftersom Microsoft Translator kontinuerligt utökar en lista med språk som stöds av dess tjänster.

Lista över språk som stöds kan inte ändras ofta. Om du vill spara bandbredd och förbättra tillgänglighet, bör ett klientprogram cachelagring språkresurser och motsvarande entitetstaggen (`ETag`). Sedan klientprogrammet kan regelbundet (till exempel en gång per dygn) skicka frågor till tjänsten för att hämta den senaste uppsättningen av språk som stöds. Skicka aktuellt `ETag` värde i en `If-None-Match` huvudfältet kan tjänsten optimera svaret. Om resursen inte har ändrats, returneras tjänsten 304-statuskoden och texten för ett tomt svar.

## <a name="response-headers"></a>Svarshuvud

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>ETag</td>
    <td>Aktuella värdet för entitetstaggen för de begärda grupperna av språk som stöds. Om du vill göra efterföljande förfrågningar effektivare klienten kan skicka den `ETag` värde i en `If-None-Match` huvudfält.
    </td>
  </tr>
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
    <td>304</td>
    <td>Resursen har inte ändrats sedan den version som anges av begärandehuvudena `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltigt. Korrigera parametrarna innan du försöker igen.</td>
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

I följande exempel visas hur du hämtar språk som stöds för textöversättning.

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
