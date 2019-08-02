---
title: Anropa API från en webbläsare – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Optimera en webb sida genom att göra API-anrop direkt från en webbläsare till Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707275"
---
# <a name="call-api-from-a-browser"></a>Anropa API från en webbläsare

Den här artikeln hjälper dig att göra anrop till Azure Custom Decision Service-API: er direkt från en webbläsare.

Var noga med att [Registrera ditt program](custom-decision-service-get-started-register.md), först.

Då sätter vi igång. Ditt program är utformat med en front sida som länkar till flera artikel sidor. På den första sidan används Custom Decision Service för att ange ordningen för dess artikel sidor. Infoga följande kod i HTML-huvudet på den första sidan:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argumentet innehåller rangordningen för de URL: er som ska renderas. Mer information finns i referens- [API: et](custom-decision-service-api-reference.md).

Om du vill hantera en användare klickar du på den översta artikeln och anropar följande kod på den första sidan:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här är argumentet till `callback()`funktionen. `data` Du hittar ett implementerings exempel i den [](custom-decision-service-tutorial-news.md#use-the-apis)här självstudien.

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
