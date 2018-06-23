---
title: Anropa API från en app - kognitiva Azure-tjänster | Microsoft Docs
description: 'Hur du kommer igång med Azure anpassad beslut tjänsten om du anropar API: er från en smartphone-app.'
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355692"
---
# <a name="call-api-from-an-app"></a>Anropa API från en app

Gör anrop till API: er Azure anpassad beslut tjänsten från en smartphone-app. Den här artikeln förklarar hur du kommer igång med några grundläggande alternativ.

Se till att [registrera ditt program](custom-decision-service-get-started-register.md), första.

Det finns två API-anrop som du gör från din smartphone-app i anpassade beslut Service: ett anrop till API: et rangordning att hämta en rankningslista över ditt innehåll och ett anrop till API: et för ersättning att rapportera ersättning. Här följer exempel anrop i [cURL](https://en.wikipedia.org/wiki/CURL).

Mer information finns i referensen [API](custom-decision-service-api-reference.md).

Börja med anropet till rangordning-API. Skapa filen `<request.json>`, som har åtgärden Ange ID. Detta ID är namnet på motsvarande RSS eller Atom-feed du angav på portalen:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Många åtgärd anger kan anges på följande sätt:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Den här JSON-fil som sedan skickas som en del av begäran rangordning:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Här är `<appId>` är namnet på programmet som är registrerad på portalen. Du bör få en ordnad uppsättning innehållsobjekt som du kan göra i ditt program. Det ser ut som ett exempel på RETUR:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Den första delen av avkastning har en lista över ordnade åtgärder som anges av åtgärden ID: N. Åtgärds-ID är en URL för en artikel. Övergripande begäran har också ett unikt `<eventId>`, skapade av systemet.

Senare kan du ange om du observerats klickar på det första innehållsobjektet från den här händelsen, som är `<actionId3>`. Du kan rapportera ersättning på den här `<eventId>` till anpassad beslut-tjänsten via API trafik med en annan begäran som:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Slutligen kan behöver du ange åtgärden Ange API som returnerar en lista över artiklar (åtgärder) anses av anpassade beslut-tjänsten. Implementera detta API som en RSS-feed som visas här:

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

Här kan varje översta `<item>` element beskriver en artikel. Den `<link>` är obligatoriskt och används som en åtgärds-ID av anpassade beslut tjänsten. Ange `<date>` (i ett standardformat för RSS) om du har mer än 15 artiklar. De senaste 15 artiklarna används. Den `<title>` är valfria och används för att skapa text-relaterade funktioner för artikeln.

## <a name="next-steps"></a>Nästa steg

* Gå igenom en [kursen](custom-decision-service-tutorial-news.md) för en mer ingående exempel.
* Kontakta referensen [API](custom-decision-service-api-reference.md) lära dig mer om de angivna funktionerna.