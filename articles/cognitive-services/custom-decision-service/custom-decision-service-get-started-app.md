---
title: Anropa API från en app – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: 'Hur du anropar Custom Decision Service-API: er från en smartphone-app.'
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60511554"
---
# <a name="call-api-from-an-app"></a>Anropa API från en app

Göra anrop till API: er för Azure Custom Decision Service från en smartphone-app. Den här artikeln förklarar hur du kommer igång med några grundläggande alternativ.

Se till att [registrera ditt program](custom-decision-service-get-started-register.md)men först.

Det finns två API-anrop som du gör från din smartphone-app i Custom Decision Service: ett anrop till API: et rangordning att hämta en rankningslista över ditt innehåll och ett anrop till API: et för trafik att rapportera ersättning. Här följer exempel-anrop i [cURL](https://en.wikipedia.org/wiki/CURL).

Mer information finns i referensen [API](custom-decision-service-api-reference.md).

Börja med anrop till API: et rangordning. Skapa filen `<request.json>`, som har åtgärden in ID. Detta ID är namnet på motsvarande RSS eller Atom-flöde som du angav på portalen:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Många åtgärd uppsättningar kan anges på följande sätt:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Det här JSON-fil som sedan skickas som en del av begäran rangordning:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Här kan `<appId>` är namnet på ditt program registreras på portalen. Du bör få en ordnad uppsättning innehållsposter som du kan rendera i ditt program. Det ser ut som ett exempel på RETUR:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Den första delen av avkastningen har en lista över beställda åtgärder som anges av deras åtgärden ID: N. Åtgärds-ID är en URL för en artikel. Övergripande begäran har också ett unikt `<eventId>`, skapade av systemet.

Senare, kan du ange om du såg en klickar du på det första innehållsobjektet från den här händelsen, vilket är `<actionId3>`. Du kan rapportera ersättning för detta `<eventId>` till Custom Decision Service via API: et för trafik med en annan begäran som:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Slutligen måste du ange åtgärden Ange API som returnerar listan med artiklar (åtgärder) för att anses vara av Custom Decision Service. Implementera detta API som en RSS-feed som visas här:

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

Här kan varje översta `<item>` element beskriver en artikel. Den `<link>` är obligatoriskt och används som en åtgärds-ID av Custom Decision Service. Ange `<date>` (i ett standardformat för RSS) om du har fler än 15 artiklar. 15 de senaste artiklarna används. Den `<title>` är valfri och används för att skapa textrelaterade funktioner för artikeln.

## <a name="next-steps"></a>Nästa steg

* Gå igenom en [självstudien](custom-decision-service-tutorial-news.md) för en mer ingående exempel.
* Läser referensen [API](custom-decision-service-api-reference.md) mer information om de angivna funktionerna.
