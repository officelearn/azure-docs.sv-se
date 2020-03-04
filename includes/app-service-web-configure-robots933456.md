---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255803"
---
## <a name="robots933456-in-logs"></a>robots933456 i loggar

Följande meddelande kan visas i behållar loggarna:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Du kan ignorera det här meddelandet på ett säkert sätt. `/robots933456.txt` är en URL-sökväg till dummy som App Service använder för att kontrol lera om behållaren kan betjäna begär Anden. Ett 404-svar anger helt enkelt att sökvägen inte finns, men det låter App Service veta att behållaren är felfri och redo att svara på begär Anden.

