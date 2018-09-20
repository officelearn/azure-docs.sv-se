---
title: Anropa API från en webbläsare – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Hur du optimerar en webbsida genom att göra API-anrop direkt i en webbläsare till Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 554b90efe1c646396597a722a74390e9048570ea
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363190"
---
# <a name="call-api-from-a-browser"></a>Anropa API från en webbläsare

Den här artikeln får du göra anrop till API: er för Azure Custom Decision Service direkt i en webbläsare.

Se till att [registrera ditt program](custom-decision-service-get-started-register.md)men först.

Nu sätter vi igång. Programmets modelleras med en förstasida som länkar till flera artikelsidor. Sidan klient använder Custom Decision Service för att ange sorteringen av artikelsidor. Infoga följande kod i HTML-huvudet på klient sidan:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

Den `data` argumentet innehåller URL: er som ska återges rangordning. Mer information finns i referensen [API](custom-decision-service-api-reference.md).

Anropa följande kod på klient sidan för att hantera en användare klickar du på den översta artikeln:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Här kan `data` argumentet till den `callback()` funktion. Ett exempel på implementering finns i den här [självstudien](custom-decision-service-tutorial-news.md#use-the-apis).

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