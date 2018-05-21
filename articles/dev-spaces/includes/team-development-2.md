---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
### <a name="run-the-service"></a>Tjänsten körs

1. Tryck på F5 (eller typ `azds up` i fönstret Terminal) att köra tjänsten. Tjänsten körs automatiskt på din valda sida `scott`. 
1. Du kan bekräfta att tjänsten körs i sin egen utrymme genom att köra `azds resource list` igen. Först märke till en instans av `mywebapi` körs nu i den `scott` utrymme (version körs i den `default` är fortfarande körs men det inte visas). För det andra åtkomstpunkt URL: en för `webfrontend` är föregås av text *scott.s.*. Denna URL är unik för den `scott` utrymme och innebär att begäranden skickas till ”scott URL” ska försöka första väg till tjänster i den `scott` utrymme och kommer att gå tjänster i den `default` utrymme.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Den här inbyggda funktionen i Azure Dev blanksteg kan du testa code slutpunkt till slutpunkt i en miljö med delad utan varje utvecklare att återskapa fullständig stack för tjänster i utrymme. Den här routning kräver Appkod för att vidarebefordra spridningen sidhuvud, enligt beskrivningen i föregående steg i den här guiden.

## <a name="test-code-in-a-space"></a>Testa kod i ett utrymme
Testa den nya versionen av `mywebapi` tillsammans med `webfrontend`, öppna webbläsaren till offentlig åtkomst punkt URL för webfrontend och gå till sidan om. Du bör se nya meddelandet visas.

Nu kan ta bort ”scott.s”. en del av URL: en och uppdatera webbläsaren. Du bör se den gamla funktionen (visas av den `mywebapi` version körs i `default`)