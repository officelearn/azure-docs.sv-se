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
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936405"
---
### <a name="run-the-service"></a>Tjänsten körs

1. Tryck på F5 (eller typ `azds up` i fönstret Terminal) att köra tjänsten. Tjänsten körs automatiskt på din valda sida `default/scott`. 
1. Du kan bekräfta att tjänsten körs i sin egen utrymme genom att köra `azds list` igen. Först märke till en instans av `mywebapi` körs nu i den `default/scott` utrymme (version körs i den `default` är fortfarande körs men det inte visas). För det andra åtkomstpunkt URL: en för `webfrontend` inleds med texten ”scott.s”. Den här URL: en är unika för den `default/scott` utrymme. URL: en särskild innebär att begäranden skickas till ”scott URL” kommer att försöka första väg till tjänster i den `default/scott` utrymme, men detta misslyckas om de kommer att gå tjänster i den `default` utrymme.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Den här inbyggda funktionen Azure Dev utrymmen kan du testa kod i en delad utrymme utan varje utvecklare att återskapa fullständig stack för tjänster i utrymme. Den här routning kräver Appkod för att vidarebefordra spridningen sidhuvud, enligt beskrivningen i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa kod i ett utrymme
Testa den nya versionen av `mywebapi` med `webfrontend`, öppna webbläsaren till offentlig åtkomst punkt URL för `webfrontend` och gå till sidan om. Du bör se nya meddelandet visas.

Nu kan ta bort ”scott.s”. en del av URL: en och uppdatera webbläsaren. Du bör se den gamla funktionen (med den `mywebapi` version körs i `default`)
