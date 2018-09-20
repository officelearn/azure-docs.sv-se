---
title: API - referens för Custom Decision Service
titlesuffix: Azure Cognitive Services
description: En fullständig API-guide för Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: 3d9b87241946a04ae71fabde9958b24ad626c0db
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364038"
---
# <a name="api"></a>API

Azure Custom Decision Service innehåller två API: er som anropas för varje beslut: den [rangordning API](#ranking-api) att ange rangordning av åtgärder och [utmärkelse API](#reward-api) att mata ut trafik. Dessutom kan du ange en [åtgärden Ange API](#action-set-api-customer-provided) att ange vilka åtgärder som Azure Custom Decision Service. Den här artikeln beskriver dessa tre API: er. Ett typiskt scenario används nedan för att visa när Custom Decision Service optimerar rangordning av artiklar.

## <a name="ranking-api"></a>Rangordning API

Rangordning-API använder en standard [JSONP](https://en.wikipedia.org/wiki/JSONP)-style kommunikationsmönster att optimera svarstid och kringgå den [princip om samma ursprung](https://en.wikipedia.org/wiki/Same-origin_policy). Det senare förbjuder JavaScript från att hämta data från utanför sidans ursprung.

Infoga det här kodfragmentet i HTML-huvudet på klient sidan (där en lista över artiklar visas):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> Återanropsfunktionen måste definieras innan anropet till API: et rangordning.

> [!TIP]
> För att förbättra svarstiden, rangordning API: et exponeras via HTTP i stället för HTTPS, som i `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> En HTTPS-slutpunkt måste dock användas om sidan klient hanteras via HTTPS.

När parametrar inte används är HTTP-svaret från API: et rangordning en JSONP-formaterad sträng:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Webbläsaren körs sedan den här strängen som ett anrop till den `callback()` funktion.

Parametern för Återanropsfunktionen i föregående exempel har följande schema:

- `ranking` innehåller rangordning URL: er som ska visas.
- `eventId` används internt av Custom Decision Service så att den matchar den här rangordning med motsvarande klick.
- `appId` kan Återanropsfunktionen att skilja mellan flera program i Custom Decision Service som körs på samma webbsida.
- `actionSets` Visar en lista över varje åtgärd som angivits i rangordning API-anrop, tillsammans med UTC-tidsstämpel för senaste lyckade uppdatering. Custom Decision Service uppdaterar regelbundet åtgärd set-flöden. Till exempel om några av de åtgärder som inte är aktuella behöva Återanropsfunktionen återgår till att deras standard rangordning.

> [!IMPORTANT]
> De angivna åtgärden anger bearbetas, och eventuellt rensas för att skapa standard rangordning artiklar. Standard rangordning hämtar sedan ordnas om och returneras i HTTP-svaret. Standard rangordning definieras här:
>
> - Inom varje åtgärd uppsättning rensas artiklarna 15 de senaste artiklarna (om mer än 15 returneras).
> - När flera uppsättningar med åtgärden anges slås de samman i samma ordning som i API-anrop. Ursprungliga sorteringen av artiklarna bevaras i varje åtgärd-uppsättningen. Dubbletter tas bort och ersatts med de tidigare kopiorna.
> - Först `n` artiklar hålls i sammanfogade listan med artiklar, där `n=20` som standard.

### <a name="ranking-api-with-parameters"></a>Rangordning API med parametrar

Rangordning API kan dessa parametrar:

- `details=1` och `details=2` infogar ytterligare information om varje artikel som anges i `ranking`.
- `limit=<n>` Anger det maximala antalet artiklar i standard rangordning. `n` måste vara mellan `2` och `30` (eller annan trunkeras till `2` eller `30`respektive).
- `dnt=1` inaktiverar användarcookies.

Parametrar kan kombineras i standard frågesyntax över sträng, till exempel `details=2&dnt=1`.

> [!IMPORTANT]
> Standardinställningen i Europa ska vara `dnt=1` tills kunden samtycker till att cookie-banderollen. Det bör också vara standardinställningen för webbplatser som är riktade till minderåriga. Mer information finns i den [användningsvillkoren](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Den `details=1` elementet infogar varje artikel `guid`, om de har hämtats av åtgärden Ange API. HTTP-svaret:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Den `details=2` element lägger till mer information om Custom Decision Service kan extrahera från artiklarnas SEO metataggar [funktionalisering kod](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` från `<meta property="og:title" content="..." />` eller `<meta property="twitter:title" content="..." />` eller `<title>...</title>`
- `description` från `<meta property="og:description" ... />` eller `<meta property="twitter:description" content="..." />` eller `<meta property="description" content="..." />`
- `image` Från `<meta property="og:image" content="..." />`
- `ds_id` Från `<meta name=”microsoft:ds_id” content="..." />`

HTTP-svaret:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Den `<details>` element:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Utmärkelse API

Anpassade Decision Service använder klickar endast på den översta platsen. Varje gång du klickar tolkas som ersättning av 1. Bristen på ett klick tolkas som ersättning 0. Klick matchas med motsvarande rankningen med hjälp av händelse-ID som genereras av den [rangordning API](#ranking-api) anropa. Om det behövs event ID: N kan skickas via sessionscookies.

Placera den här koden på klient sidan för att hantera ett klick på den översta platsen:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här `data` argumentet till den `callback()` fungera som tidigare beskrivits. Med hjälp av `data` i klickar du på hantering av kod kräver viss precision. Ett exempel visas i den här [självstudien](custom-decision-service-tutorial-news.md#use-the-apis).

Endast för testning, utmärkelse API: et kan anropas [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Den förväntade effekten är ett HTTP-svar på 200 (OK). Du kan se trafik 1 för den här händelsen i loggen (om en Azure storage-kontonyckel angavs i portalen).

## <a name="action-set-api-customer-provided"></a>Åtgärden Ange API (kunden tillhandahåller)

Åtgärden Ange API returnerar en lista över artiklar (åtgärder) på en hög nivå. Varje artikel har angetts av dess URL (valfritt) artikelrubrik och datum för publikationen. Du kan ange flera åtgärd anger på portalen. En annan åtgärd ange API bör användas för varje åtgärd, som en distinkt URL.

Varje åtgärd ställa in API kan implementeras på två sätt: som en RSS-feed eller en Atom-feed. Antingen en bör följa standarden och returnerar en rätt XML. RSS är här ett exempel:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Varje översta `<item>` element beskriver en åtgärd:

- `<link>` är obligatoriskt och används som en åtgärd-ID.
- `<date>` ignoreras om det är mindre än eller lika med 15 objekt; Annars är obligatorisk.
  - Om det finns fler än 15 objekt, används de senaste 15 som.
  - Det måste vara i standardformat för RSS- eller Atom, respektive:
    - [RFC 822](https://tools.ietf.org/html/rfc822) för RSS: till exempel `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) för Atom: till exempel `"2016-12-19T16:39:57-08:00"`
- `<title>` är valfria och används för att skapa funktioner som beskrivs i artikeln.
- `<guid>` är valfritt och skickade genom systemet till Återanropsfunktionen (om den `?details` parameter har angetts i rangordning API-anrop).

Andra element i en `<item>` ignoreras.

Atom-flödet versionen använder samma XML-syntax och konventioner.

> [!TIP]
> Om systemet använder sin egen artikel-ID: N, de kan skickas till Återanropsfunktionen genom att använda `<guid>`.