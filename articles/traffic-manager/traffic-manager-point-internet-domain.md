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
ms.openlocfilehash: 6c5c5c185063caf8ca258ad70a70903c9b583e07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80294845"
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
