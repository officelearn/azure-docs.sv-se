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
ms.openlocfilehash: 2f2177b289ba9d13ba635bddcd61748e1e8ab0d1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062937"
---
### <a name="run-the-service"></a>Köra tjänsten

1. Tryck på F5 (eller ange `azds up` i terminalfönstret) om du vill köra tjänsten. Tjänsten körs automatiskt i det valda utrymmet `default/scott`. 
1. Du kan bekräfta att tjänsten körs i sitt eget utrymme genom att köra `azds list-up` igen. Först och främst märker du att ett exemplar av `mywebapi` nu körs i `default/scott`-utrymmet (versionen som körs i `default` körs fortfarande, men visas inte). Dessutom står texten ”scott.s.” innan åtkomstpunktens webbadress för `webfrontend`. Den här webbadressen är unik för `default/scott`-utrymmet. Den särskilda webbadressen innebär att en begäran som skickas till ”scott-webbadressen” först skickas till tjänster i `default/scott`-utrymmet. Om det misslyckas återkommer de till tjänster i `default`-utrymmet.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Med den här inbyggda Azure Dev Spaces-funktionen kan du testa kod i ett delat utrymme utan att varje utvecklare måste återskapa hela tjänstestacken i deras respektive utrymme. Den här typen av routning kräver att appkoden vidarebefordrar spridningshuvuden, vilket visas i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa koden i ett utrymme
Du testar den nya versionen av `mywebapi` med `webfrontend` genom att öppna webbläsaren på den offentliga åtkomstpunktens webbadress för `webfrontend` och gå till sidan Om. Nu bör ditt nya meddelande visas.

Ta nu bort delen ”scott.s.” från webbadressen och uppdatera webbläsaren. Nu bör du se det äldre beteendet (med `mywebapi`-versionen som körs i `default`)
