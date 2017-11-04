---
title: "IP-begränsningar i Azure App Service | Microsoft Docs"
description: "Hur du använder IP-begränsningarna med Azure App Service"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 5fbd308e9f037038ad867f3d242da6573bc67081
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure Apptjänst statiska IP-begränsningar #

IP-begränsningar kan du definiera en lista över IP-adresser som har tillgång till din app. Listan över tillåtna kan inkludera enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask.

När en begäran om att appen har genererats från en klient, utvärderas den IP-adressen mot listan över tillåtna. Om ip-adressen inte finns med i listan över appen svarar med en [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) statuskod.

IP-begränsningarna har definierats i web.config som din app använder vid körning. Under vissa omständigheter kan vissa modulen köras innan IP-begränsningar logiken i HTTP-pipeline. När detta inträffar, misslyckas denna begäran med ett annat HTTP-felkod.

IP-begränsningar utvärderas på samma App plan tjänstinstanser tilldelats din app.

Om du vill lägga till en IP-begränsningsregel i appen, använder du menyn för att öppna **nätverk**>**IP-begränsningar** och klicka på **konfigurera IP-adressbegränsningar**

! [IP-adressbegränsningar] (media/app-service-ip-restrictions/ip-restrictions.png)

Här kan granska du listan över IP-begränsning definierade regler för din app.

![lista över IP-adressbegränsningar](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Du kan klicka på **[+] Lägg till** att lägga till en ny regel för IP-begränsning.

![lägga till IP-begränsningar](media/app-service-ip-restrictions/add-ip-restrictions.png)
