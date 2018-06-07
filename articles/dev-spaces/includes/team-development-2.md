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
ms.openlocfilehash: f6245a97f5d94c90e022ac509b61da477f4d9494
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823840"
---
### <a name="run-the-service"></a>Tjänsten körs

1. Tryck på F5 (eller typ `azds up` i fönstret Terminal) att köra tjänsten. Tjänsten körs automatiskt på din valda sida `scott`. 
1. Du kan bekräfta att tjänsten körs i sin egen utrymme genom att köra `azds resource list` igen. Först märke till en instans av `mywebapi` körs nu i den `scott` utrymme (version körs i den `default` utrymme, men den visas inte). Andra åtkomstpunkt URL: en för `webfrontend` har nu prefixet `scott.s.`. Denna URL är unik för den `scott` utrymme och innebär att begäranden skickas till ”scott URL” först försöker vidarebefordra till tjänster i den `scott` utrymme, och om det finns inga specifika `scott` instansen för tjänsten begäranden återgår till att tjänster i den `default` utrymme.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Den här inbyggda funktionen i Azure Dev blanksteg kan du testa code slutpunkt till slutpunkt i en delad dev utrymme utan varje utvecklare att återskapa fullständig stack för tjänster i utrymme. Den här routning kräver Appkod för att vidarebefordra spridningen sidhuvud, enligt beskrivningen i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa kod i ett utrymme
Testa den nya versionen av `mywebapi` med `webfrontend`, öppna webbläsaren till offentlig åtkomst punkt URL för webfrontend och gå till sidan om. Du bör se nya meddelandet visas.

Nu kan ta bort den `scott.s.` en del av URL: en och uppdatera webbläsaren. Du bör se den gamla funktionen (visas av den `mywebapi` version körs i `default`)