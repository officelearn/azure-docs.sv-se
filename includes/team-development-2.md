---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e3762744d2d1f021531694b2d9a3f75ca1c3ed49
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291073"
---
### <a name="run-the-service"></a>Köra tjänsten

1. Tryck på F5 (eller ange `azds up` i terminalfönstret) om du vill köra tjänsten. Tjänsten körs automatiskt i det valda utrymmet `default/scott`. 
1. Du kan bekräfta att tjänsten körs i sitt eget utrymme genom att köra `azds list-up` igen. Först och främst märker du att ett exemplar av `mywebapi` nu körs i `default/scott`-utrymmet (versionen som körs i `default` körs fortfarande, men visas inte). Om du kör `azds list-uris` märker du att åtkomstpunktens URL för `webfrontend` har ett prefix med texten ”scott.s.”. Den här webbadressen är unik för `default/scott`-utrymmet. Den särskilda webbadressen innebär att en begäran som skickas till ”scott-webbadressen” först skickas till tjänster i `default/scott`-utrymmet. Om det misslyckas återkommer de till tjänster i `default`-utrymmet.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Med den här inbyggda Azure Dev Spaces-funktionen kan du testa kod i ett delat utrymme utan att varje utvecklare måste återskapa hela tjänstestacken i deras respektive utrymme. Den här typen av routning kräver att appkoden vidarebefordrar spridningshuvuden, vilket visas i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa koden i ett utrymme
Du testar den nya versionen av `mywebapi` med `webfrontend` genom att öppna webbläsaren på den offentliga åtkomstpunktens webbadress för `webfrontend` och gå till sidan Om. Nu bör ditt nya meddelande visas.

Ta nu bort delen ”scott.s.” från webbadressen och uppdatera webbläsaren. Nu bör du se det gamla beteendet (med `mywebapi`-versionen som körs i `default`).
