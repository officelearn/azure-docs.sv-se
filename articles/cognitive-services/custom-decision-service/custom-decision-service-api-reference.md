---
title: API-referens – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: En fullständig API-guide för Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 569a1c83562a995f15e12013c864ef4c0447d963
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161694"
---
# <a name="api"></a>API

Azure Custom Decision Service tillhandahåller två API: er som anropas för varje beslut: [rangordnings-API: et](#ranking-api) för att mata in åtgärdernas rangordning och [belönings-API: et](#reward-api) för att ge belöningen. Dessutom anger du ett API för [Åtgärds uppsättning](#action-set-api-customer-provided) för att ange åtgärder till Azure Custom Decision service. Den här artikeln beskriver dessa tre API: er. Ett typiskt scenario används nedan för att visa när Custom Decision Service optimerar rankningen av artiklar.

## <a name="ranking-api"></a>Rangordnings-API

Ranknings-API: et använder ett standardiserat [JSONP](https://en.wikipedia.org/wiki/JSONP)för att optimera svars tid och kringgå [principen för samma ursprung](https://en.wikipedia.org/wiki/Same-origin_policy). Den senare förhindrar att Java Script hämtar data från utanför sidans ursprung.

Infoga det här kodfragmentet i HTML-huvudet på den första sidan (där en anpassad lista med artiklar visas):

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
> Motringningsfunktionen måste definieras innan anropet till ranknings-API: et.

> [!TIP]
> För att förbättra svars tiden exponeras ranknings-API: t via HTTP i stället för HTTPS, som i `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> En HTTPS-slutpunkt måste dock användas om den första sidan betjänas via HTTPS.

När parametrar inte används, är HTTP-svaret från rangordnings-API: et en JSONP-formaterad sträng:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Webbläsaren kör sedan den här strängen som ett anrop till `callback()`-funktionen.

Parametern till callback-funktionen i föregående exempel har följande schema:

- `ranking` tillhandahåller rangordningen för URL: er som ska visas.
- `eventId` används internt av Custom Decision Service för att matcha den här rangordningen med motsvarande musklick.
- `appId` tillåter motringningsfunktionen att skilja mellan flera program Custom Decision Service som körs på samma webb sida.
- `actionSets` listar varje åtgärds uppsättning som används i ranknings-API-anropet, tillsammans med UTC-tidsstämpeln för den senaste lyckade uppdateringen. Custom Decision Service uppdaterar regelbundet åtgärds uppsättningens feeds. Om vissa av åtgärds uppsättningarna t. ex. inte är aktuella kan callback-funktionen behöva återgå till sin standard rangordning.

> [!IMPORTANT]
> De angivna åtgärds uppsättningarna bearbetas och kan eventuellt rensas för att utgöra standard rangordningen för artiklar. Standard rangordningen får sedan en omordning och returneras i HTTP-svaret. Standard rangordningen definieras här:
>
> - I varje åtgärds uppsättning rensas artiklarna till de 15 senaste artiklarna (om fler än 15 returneras).
> - När flera åtgärds uppsättningar anges slås de samman i samma ordning som i API-anropet. Den ursprungliga ordningen på artiklarna bevaras i varje åtgärds uppsättning. Dubbletter tas bort till förmån för de tidigare kopiorna.
> - De första `n` artiklarna sparas i den sammanslagna artikel listan, där `n=20` som standard.

### <a name="ranking-api-with-parameters"></a>Rangordnings-API med parametrar

Rangordnings-API: et tillåter följande parametrar:

- `details=1` och `details=2` infogar ytterligare information om varje artikel som anges i `ranking`.
- `limit=<n>` anger maximalt antal artiklar i standard rangordningen. `n` måste vara mellan `2` och `30` (eller annars trunkeras de till `2` respektive `30`).
- `dnt=1` inaktiverar användar-cookies.

Parametrar kan kombineras med standard syntax, frågesträng, till exempel `details=2&dnt=1`.

> [!IMPORTANT]
> Standardinställningen i Europa bör vara `dnt=1` tills kunden accepterar cookie-banderollen. Det bör också vara standardinställningen för webbplatser som är riktade mot minderåriga. Mer information finns i [användnings villkoren](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

`details=1`-elementet infogar varje artikels `guid`, om den hanteras av åtgärds uppsättnings-API: et. HTTP-svaret:

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

`details=2`-elementet lägger till mer information som Custom Decision Service kan extrahera från artiklar "SEO-metataggar [funktionalisering Code](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` från `<meta property="og:title" content="..." />` eller `<meta property="twitter:title" content="..." />` eller `<title>...</title>`
- `description` från `<meta property="og:description" ... />` eller `<meta property="twitter:description" content="..." />` eller `<meta property="description" content="..." />`
- `image` från `<meta property="og:image" content="..." />`
- `ds_id` från `<meta name="microsoft:ds_id" content="..." />`

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

`<details>`-elementet:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Belönings-API

Custom Decision Service använder bara klickningar på den övre platsen. Varje klickning tolkas som en belöning på 1. Bristen på ett klick tolkas som en belöning på 0. Klickningarna matchas med motsvarande rangordning med hjälp av händelse-ID: n, som genereras av API-anropet [rankning](#ranking-api) . Vid behov kan händelse-ID: n skickas via sessions-cookies.

Om du vill hantera ett klick på den övre platsen, så Lägg till den här koden på den första sidan:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här `data` är argumentet till `callback()`-funktionen, enligt beskrivningen ovan. Att använda `data` i för att klicka på hanterings koden kräver lite skötsel. Ett exempel visas i den här [självstudien](custom-decision-service-tutorial-news.md#use-the-apis).

För testning kan du ställa in belönings-API: t via [svängen](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Den förväntade resultatet är ett HTTP-svar på 200 (OK). Du kan se belöningen 1 för den här händelsen i loggen (om en nyckel för Azure Storage-konto har angetts i portalen).

## <a name="action-set-api-customer-provided"></a>API för åtgärds uppsättning (kund tillhandahålls)

På en hög nivå returnerar åtgärds uppsättnings-API en lista över artiklar (åtgärder). Varje artikel anges med URL: en och (valfritt) artikel rubriken och publicerings datumet. Du kan ange flera åtgärds uppsättningar på portalen. En annan åtgärds uppsättnings-API ska användas för varje åtgärds uppsättning som en distinkt URL.

Varje åtgärds uppsättnings-API kan implementeras på två sätt: som en RSS-feed eller som en Atom-feed. Antingen bör den följa standarden och returnera en korrekt XML. Här är ett exempel på RSS:

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

Varje `<item>` element på översta nivån beskriver en åtgärd:

- `<link>` är obligatoriskt och används som åtgärds-ID.
- `<date>` ignoreras om det är mindre än eller lika med 15 objekt. annars är det obligatoriskt.
  - Om det finns fler än 15 objekt används de 15 senaste.
  - Det måste vara i standardformat för RSS eller Atom:
    - [RFC 822](https://tools.ietf.org/html/rfc822) för RSS: till exempel `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) för atom: till exempel `"2016-12-19T16:39:57-08:00"`
- `<title>` är valfritt och används för att generera funktioner som beskriver artikeln.
- `<guid>` är valfritt och skickas genom systemet till callback-funktionen (om `?details`-parametern anges i API-anropet rankning).

Andra element i en `<item>` ignoreras.

I Atom-matnings versionen används samma XML-syntax och konventioner.

> [!TIP]
> Om systemet använder sina egna artikel-ID: n, kan de skickas till callback-funktionen med hjälp av `<guid>`.
