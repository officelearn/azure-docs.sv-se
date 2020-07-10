---
title: Peka en Internet domän till Traffic Manager-Azure Traffic Manager
description: Den här artikeln beskriver hur du pekar ditt företags domännamn på ett Traffic Manager-domännamn.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: 4b0f79ba072c813a7639e87025c19a34e65df023
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183468"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Peka företagets Internetdomän mot en Azure Traffic Manager-domän

När du skapar en Traffic Manager-profil, tilldelar Azure automatiskt ett DNS-namn för profilen. För att använda ett namn från din DNS-zon, skapar du en CNAME DNS-post som mappar till domännamnet för din Traffic Manager-profil. Du hittar Traffic Manager-domännamnet i **Allmänt**-avsnittet på konfigurationssidan för Traffic Manager-profilen.

Om du till exempel vill peka namnet `www.contoso.com` till Traffic Manager DNS-namnet `contoso.trafficmanager.net` skapar du följande DNS-resurspost:

`www.contoso.com IN CNAME contoso.trafficmanager.net`

Alla trafik begär anden till *www- \. contoso.com* får dirigeras till *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Du kan inte peka en andranivådomän, t.ex. *contoso.com*, på Traffic Manager-domänen. DNS-protokollstandarder tillåter inte CNAME-poster för andra nivåns domännamn.

## <a name="next-steps"></a>Nästa steg

* [Traffic Manager-dirigeringsmetoder](traffic-manager-routing-methods.md)
* [Traffic Manager-inaktivera, aktivera eller ta bort en profil](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Inaktivera eller aktivera en slutpunkt](disable-or-enable-an-endpoint.md)
