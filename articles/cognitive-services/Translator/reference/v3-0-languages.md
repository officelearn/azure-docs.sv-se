---
title: Metod för api-språk för översättare
titleSuffix: Azure Cognitive Services
description: Metoden Språk hämtar den uppsättning språk som för närvarande stöds av andra åtgärder i Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835836"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: Språk

Hämtar den uppsättning språk som för närvarande stöds av andra åtgärder i Translator Text API. 

## <a name="request-url"></a>URL för begäran

Skicka `GET` en begäran till:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrar för begäran som skickas på frågesträngen är:

<table width="100%">
  <th width="20%">Parameter för fråga</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Version av API som begärs av klienten. Värdet måste `3.0`vara .</td>
  </tr>
  <tr>
    <td>omfång</td>
    <td>*Valfri parameter*.<br/>En kommaavgränsad lista med namn som definierar den grupp av språk som ska returneras. Tillåtna gruppnamn `translation` `transliteration` är: och `dictionary`. Om inget scope anges returneras alla grupper, vilket `scope=translation,transliteration,dictionary`motsvarar att passera . Information om vilken uppsättning språk som stöds är lämpliga för ditt scenario finns i beskrivningen av [svarsobjektet](#response-body).</td>
  </tr>
</table> 

Begäran rubriker är:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Valfritt förfrådehuvud*.<br/>Språket som ska användas för användargränssnittssträngar. Några av fälten i svaret är namn på språk eller namn på regioner. Använd den här parametern för att definiera det språk som dessa namn returneras på. Språket anges genom att tillhandahålla en välformad BCP 47 språktagg. Använd till exempel `fr` värdet för att begära namn `zh-Hant` på franska eller använd värdet för att begära namn i traditionell kinesiska.<br/>Namn anges på engelska när ett målspråk inte anges eller när lokalisering inte är tillgänglig.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt förfrådehuvud*.<br/>Ett klientgenererat GUID för att unikt identifiera begäran.</td>
  </tr>
</table> 

Autentisering krävs inte för att hämta språkresurser.

## <a name="response-body"></a>Själva svaret

En klient `scope` använder frågeparametern för att definiera vilka språkgrupper den är intresserad av.

* `scope=translation`tillhandahåller språk som stöds för att översätta text från ett språk till ett annat språk.

* `scope=transliteration`innehåller funktioner för att konvertera text på ett språk från ett skript till ett annat skript.

* `scope=dictionary`tillhandahåller språkpar för `Dictionary` vilka åtgärder returnerar data.

En klient kan hämta flera grupper samtidigt genom att ange en kommaavgränsad lista med namn. Skulle till `scope=translation,transliteration,dictionary` exempel returnera språk som stöds för alla grupper.

Ett lyckat svar är ett JSON-objekt med en egenskap för varje begärd grupp:

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

Värdet för varje egenskap är följande.

* `translation`Egenskapen

  Värdet för `translation` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP 47 språktagg. En nyckel identifierar ett språk som text kan översättas till eller översättas från. Värdet som är associerat med nyckeln är ett JSON-objekt med egenskaper som beskriver språket:

  * `name`: Visa namnet på språket i `Accept-Language` språket som begärs via sidhuvudet.

  * `nativeName`: Visa språkets namn på språken som är infödda för det här språket.

  * `dir`: Riktning, som `rtl` är för språk från `ltr` höger till vänster eller för språk från vänster till höger.

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

* `transliteration`Egenskapen

  Värdet för `transliteration` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP 47 språktagg. En nyckel identifierar ett språk för vilket text kan konverteras från ett skript till ett annat skript. Värdet som associeras med nyckeln är ett JSON-objekt med egenskaper som beskriver språket och dess skript som stöds:

  * `name`: Visa namnet på språket i `Accept-Language` språket som begärs via sidhuvudet.

  * `nativeName`: Visa språkets namn på språken som är infödda för det här språket.

  * `scripts`: Lista över skript att konvertera från. Varje element `scripts` i listan har egenskaper:

    * `code`: Kod som identifierar skriptet.

    * `name`: Visa namnet på skriptet i `Accept-Language` språken som begärs via sidhuvudet.

    * `nativeName`: Visa språkets namn på språkets språk.

    * `dir`: Riktning, som `rtl` är för språk från `ltr` höger till vänster eller för språk från vänster till höger.

    * `toScripts`: Lista över skript som är tillgängliga att konvertera text till. Varje element `toScripts` i listan `code` `name`har `nativeName`egenskaper `dir` , och som beskrivits tidigare.

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

* `dictionary`Egenskapen

  Värdet för `dictionary` egenskapen är en ordlista med (nyckel, värde) par. Varje nyckel är en BCP 47 språktagg. Nyckeln identifierar ett språk för vilket alternativa översättningar och bakåtöversättningar finns tillgängliga. Värdet är ett JSON-objekt som beskriver källspråket och målspråken med tillgängliga översättningar:

  * `name`: Visa namnet på källspråket i `Accept-Language` språkinställningarna som begärs via sidhuvudet.

  * `nativeName`: Visa språkets namn på språken som är infödda för det här språket.

  * `dir`: Riktning, som `rtl` är för språk från `ltr` höger till vänster eller för språk från vänster till höger.

  * `translations`: Lista över språk med alterativa översättningar och exempel för frågan uttryckt i källspråket. Varje element `translations` i listan har egenskaper:

    * `name`: Visa namnet på målspråket i `Accept-Language` det språk som begärs via sidhuvudet.

    * `nativeName`: Visa namnet på målspråket i språkinfödingen för målspråket.

    * `dir`: Riktning, som `rtl` är för språk från `ltr` höger till vänster eller för språk från vänster till höger.
    
    * `code`: Språkkod som identifierar målspråket.

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

Svarsobjektets struktur ändras inte utan en ändring i api:et. För samma version av API:et kan listan över tillgängliga språk ändras med tiden eftersom Microsoft Translator kontinuerligt utökar listan över språk som stöds av dess tjänster.

Listan över språk som stöds ändras inte ofta. För att spara nätverksbandbredd och förbättra svarstiden bör ett klientprogram överväga`ETag`att cachelagra språkresurser och motsvarande entitetstagg ( ). Sedan kan klientprogrammet regelbundet (till exempel en gång var 24:e timme) fråga tjänsten för att hämta den senaste uppsättningen språk som stöds. Om du `ETag` skickar `If-None-Match` det aktuella värdet i ett huvudfält kan tjänsten optimera svaret. Om resursen inte har ändrats returnerar tjänsten statuskod 304 och en tom svarstext.

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Etag</td>
    <td>Aktuellt värde för entitetstaggen för de begärda grupperna med språk som stöds. Om du vill göra efterföljande begäranden mer effektiva kan klienten skicka `ETag` värdet i ett `If-None-Match` huvudfält.
    </td>
  </tr>
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
    <td>304</td>
    <td>Resursen har inte ändrats sedan den version `If-None-Match`som anges av begäranden .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna saknas eller är ogiltig. Korrigera parametrar för begäran innan du försöker igen.</td>
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
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
