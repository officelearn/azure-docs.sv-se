---
title: API - kognitiva Azure-tjänster | Microsoft Docs
description: En fullständig och användarvänliga API-guide för Azure-anpassad beslut, ett moln-baserad API för kontextuella beslutsfattande som ökar skärpan upplevelse.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354951"
---
# <a name="api"></a>API

Azure anpassad beslut tjänsten tillhandahåller två API: er som kallas för varje beslut: den [rangordning API](#ranking-api) som indata rangordning åtgärder och [ersättning API](#reward-api) till utdata i ersättning. Dessutom kan du ange en [åtgärden Ange API](#action-set-api-customer-provided) att ange vilka åtgärder som Azure anpassad beslut Service. Den här artikeln beskriver dessa tre API: er. Ett typiskt scenario används nedan för att visa när anpassad beslut Service optimerar rangordning artiklar.

## <a name="ranking-api"></a>Rangordning API

Rangordning API: N använder en standard [hanteras JSONP](https://en.wikipedia.org/wiki/JSONP)-style kommunikation mönster för att optimera svarstid och kringgå den [samma ursprung princip](https://en.wikipedia.org/wiki/Same-origin_policy). Denna förhindrar JavaScript hämtar data utanför sidans ursprung.

Infoga följande kodutdrag i HTML-huvud din framsidan (där visas en lista med artiklar):

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
> Återanropsfunktionen måste definieras innan anropet till rangordning API.

> [!TIP]
> För att förbättra svarstiden rangordning API exponeras via HTTP i stället för HTTPS, som i `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> En HTTPS-slutpunkt måste dock användas om sidan främre hanteras via HTTPS.

När parametrar inte används, är HTTP-svaret från rangordning API en hanteras JSONP-formaterad sträng:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Webbläsaren körs sedan den här strängen som ett anrop till den `callback()` funktion.

Parameter för motringningsfunktionen i föregående exempel har följande schema:

- `ranking` innehåller rangordningen för URL: er som ska visas.
- `eventId` används internt av anpassade beslut tjänsten så att den matchar den här rangordning med motsvarande klick.
- `appId` tillåter Återanropsfunktionen att skilja mellan flera program för anpassad beslut tjänst körs på samma webbsida.
- `actionSets` Listar alla åtgärder som angivits i rangordning API-anrop, tillsammans med UTC-tidsstämpel för senaste slutförda uppdateringen. Anpassade beslut Service uppdaterar regelbundet hur set-feeds åtgärd. Till exempel om några av de åtgärder som inte är aktuella kan Återanropsfunktionen behöva återställas till sina standard rangordning.

> [!IMPORTANT]
> De angivna åtgärden anger behandlas, och eventuellt rensas för att skapa standard rangordning artiklar. Standard-rangordning hämtar sedan ordnas om och returneras i HTTP-svaret. Standard-rangordning definieras här:
>
> - Inom varje åtgärd uppsättning rensas artiklarna till 15 senaste artiklar (om mer än 15 returneras).
> - När flera åtgärd anger anges kombineras de i samma ordning som i API-anrop. Den ursprungliga sorteringen av artiklarna bevaras inom varje åtgärd. Dubbletter tas bort för tidigare versioner.
> - Först `n` artiklar hålls i sammanfogade listan med artiklar, där `n=20` som standard.

### <a name="ranking-api-with-parameters"></a>Rangordning API med parametrar

Rangordning API kan dessa parametrar:

- `details=1` och `details=2` infogar ytterligare information om varje artikel som anges i `ranking`.
- `limit=<n>` Anger det maximala antalet artiklar i standard rangordning. `n` måste vara mellan `2` och `30` (eller annan trunkeras till `2` eller `30`respektive).
- `dnt=1` inaktiverar användarcookies.

Parametrar kan kombineras i standard frågesyntaxen över sträng, till exempel `details=2&dnt=1`.

> [!IMPORTANT]
> Standardinställningen för Europa ska vara `dnt=1` förrän kunden samtycker till att cookie-banderollen. Det bör också vara standardinställningen för webbplatser som är riktade till minderåriga. Mer information finns i [användningsvillkoren](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Den `details=1` element infogar varje artikel `guid`, om den hanteras av åtgärden Ange API: et. HTTP-svar:

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

Den `details=2` element lägger till mer information som anpassade beslut Service kan extraheras från artiklar SEO metataggar [featurization kod](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` från `<meta property="og:title" content="..." />` eller `<meta property="twitter:title" content="..." />` eller `<title>...</title>`
- `description` från `<meta property="og:description" ... />` eller `<meta property="twitter:description" content="..." />` eller `<meta property="description" content="..." />`
- `image` från `<meta property="og:image" content="..." />`
- `ds_id` från `<meta name=”microsoft:ds_id” content="..." />`

HTTP-svar:

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

## <a name="reward-api"></a>Ersättning API

Anpassade beslut tjänsten använder klickar bara på den översta platsen. Varje gång du klickar tolkas som ersättning för 1. Bristen på en klickning tolkas som ersättning för 0. Klick matchas med motsvarande rangordning med hjälp av händelse-ID som genereras av den [rangordning API](#ranking-api) anropa. Om det behövs, event ID: N kan skickas via sessions-cookies.

Placera den här koden på din framsidan för att hantera en klickar du på den översta platsen:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här `data` argumentet till den `callback()` fungera som beskrivits ovan. Med hjälp av `data` i klickar du på hantering av koden kräver viss precision. Ett exempel visas i den här [kursen](custom-decision-service-tutorial-news.md#use-the-apis).

Endast för testning, ersättning API kan anropas [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Den förväntade effekten är ett HTTP-svar på 200 (OK). Du kan se trafik 1 för den här händelsen i loggen (om en nyckel för Azure storage-konto har angetts i portalen).

## <a name="action-set-api-customer-provided"></a>Åtgärden Ange API (kund tillhandahålls)

Åtgärden Ange API returnerar en lista över artiklar (åtgärder) på en hög nivå. Varje artikel anges av URL: en och (valfritt) artikelrubrik och publikationsdatum. Du kan ange flera åtgärd anger på portalen. En annan åtgärd anger API bör användas för varje åtgärd, som en distinkta URL.

Varje åtgärd anger API kan implementeras på två sätt: som en RSS-feed eller som en Atom-feed. Antingen en bör följa standarden och returnera rätt XML. RSS är här ett exempel:

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

- `<link>` är obligatoriskt och används som en åtgärds-ID.
- `<date>` ignoreras om det är mindre än eller lika med 15 objekt; Annars är obligatorisk.
  - Om det finns fler än 15 objekt, används de senaste 15 som.
  - Det måste vara i standardformat för RSS- eller Atom, respektive:
    - [RFC 822](https://tools.ietf.org/html/rfc822) för RSS: till exempel `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) för Atom: till exempel `"2016-12-19T16:39:57-08:00"`
- `<title>` är valfria och används för att generera funktioner som beskrivs i artikeln.
- `<guid>` är valfritt och skickades via systemets Återanropsfunktionen (om den `?details` parameter har angetts i rangordning API-anrop).

Andra element i en `<item>` ignoreras.

Atom-feed versionen använder samma XML-syntax och konventioner.

> [!TIP]
> Om systemet använder sin egen artikel-ID: N, de kan skickas till Återanropsfunktionen genom att använda `<guid>`.