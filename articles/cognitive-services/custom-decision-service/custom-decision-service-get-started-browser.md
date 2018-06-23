---
title: Anropa API från en webbläsare - kognitiva Azure-tjänster | Microsoft Docs
description: Hur du kommer igång med Azure anpassad beslut-tjänsten för att optimera en webbsida genom att göra API-anrop direkt från en webbläsare.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 10236c9d8f70d9b90a896464b4f86a847ee904c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354747"
---
# <a name="call-api-from-a-browser"></a>Anropa API från en webbläsare

Den här artikeln hjälper dig att göra anrop till API: er Azure anpassad beslut tjänsten direkt i en webbläsare.

Se till att [registrera ditt program](custom-decision-service-get-started-register.md), första.

Nu sätter vi igång. Programmet modelleras som en klient sida som länkar till flera sidor i artikeln. Främre sidan använder anpassad beslut tjänst för att ange ordning artikel sidor. Infoga följande kod i HTML-huvudet på främre sidan:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

Den `data` argumentet innehåller rangordning URL: er som ska renderas. Mer information finns i referensen [API](custom-decision-service-api-reference.md).

Anropa följande kod för att hantera användare klickar på den översta artikeln på sidan klient:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här, `data` argumentet till den `callback()` funktion. Ett exempel på implementering finns i det här [kursen](custom-decision-service-tutorial-news.md#use-the-apis).

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