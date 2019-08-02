---
title: Anropa API från en app-Custom Decision Service
titlesuffix: Azure Cognitive Services
description: 'Så här anropar du Custom Decision Service-API: er från en smartphone-app.'
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707259"
---
# <a name="call-api-from-an-app"></a>Anropa API från en app

Gör anrop till Azure Custom Decision Service-API: er från en smartphone-app. Den här artikeln förklarar hur du kommer igång med några grundläggande alternativ.

Var noga med att [Registrera ditt program](custom-decision-service-get-started-register.md), först.

Det finns två API-anrop som du gör från din smartphone-app för att Custom Decision Service: ett anrop till rangordnings-API: et för att få en rangordnad lista över ditt innehåll och ett anrop till belönings-API: et för att rapportera en belöning Här är exempel anropen i [sväng](https://en.wikipedia.org/wiki/CURL).

Mer information finns i referens- [API: et](custom-decision-service-api-reference.md).

Börja med anropet till rangordnings-API: et. Skapa filen `<request.json>`som har åtgärds uppsättnings-ID. Detta ID är namnet på motsvarande RSS-eller Atom-feed som du angav i portalen:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Många åtgärds uppsättningar kan anges på följande sätt:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Den här JSON-filen skickas sedan som en del av rangordnings förfrågan:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

`<appId>` Här är namnet på ditt program registrerat i portalen. Du bör få en ordnad uppsättning innehålls objekt, som du kan återge i ditt program. En exempel retur ser ut så här:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Den första delen av returen har en lista över beställda åtgärder som anges av deras åtgärds-ID. För en artikel är åtgärds-ID: t en URL. Den övergripande begäran har också en unik `<eventId>`som skapats av systemet.

Senare kan du ange om du har observerat ett klick på det första innehålls objektet från den här händelsen, `<actionId3>`vilket är. Du kan sedan rapportera en belöning om detta `<eventId>` till Custom Decision service via belönings-API: et, med en annan begäran, till exempel:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Slutligen måste du ange API för åtgärds uppsättningen, som returnerar listan med artiklar (åtgärder) som ska beaktas av Custom Decision Service. Implementera denna API som en RSS-feed, som du ser här:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Här beskrivs varje element på översta `<item>` nivån som innehåller en artikel. `<link>` Är obligatoriskt och används som åtgärds-ID med Custom Decision service. Ange `<date>` (i ett standard-RSS-format) om du har fler än 15 artiklar. De 15 senaste artiklarna används. `<title>` Är valfritt och används för att skapa text-relaterade funktioner för artikeln.

## <a name="next-steps"></a>Nästa steg

* Arbeta i en [självstudie](custom-decision-service-tutorial-news.md) för ett mer djupgående exempel.
* Mer information om de funktioner som tillhandahålls finns i referens- [API: et](custom-decision-service-api-reference.md) .
