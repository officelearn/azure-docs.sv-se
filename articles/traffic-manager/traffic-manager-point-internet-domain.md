---
title: Peka företagets Internetdomän mot ett Traffic Manager-domännamn | Microsoft Docs
description: Den här artikeln beskriver hur du pekar ditt företags domännamn på ett Traffic Manager-domännamn.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 63a78332742dcefab20401575b1df0e23c06aa78
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035154"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Peka företagets Internetdomän mot en Azure Traffic Manager-domän

När du skapar en Traffic Manager-profil, tilldelar Azure automatiskt ett DNS-namn för profilen. För att använda ett namn från din DNS-zon, skapar du en CNAME DNS-post som mappar till domännamnet för din Traffic Manager-profil. Du hittar Traffic Manager-domännamnet i **Allmänt**-avsnittet på konfigurationssidan för Traffic Manager-profilen.

Om du till exempel vill peka namnet `www.contoso.com` till Traffic Manager DNS-namnet `contoso.trafficmanager.net` skapar du följande DNS-resurspost:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Alla trafikbegäranden för *www.contoso.com* kommer nu att dirigeras till *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Du kan inte peka en andranivådomän, t.ex. *contoso.com*, på Traffic Manager-domänen. DNS-protokollstandarder tillåter inte CNAME-poster för andra nivåns domännamn.

## <a name="next-steps"></a>Nästa steg

* [Routningsmetoder för Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager – Inaktivera, aktiver eller ta bort en profil](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Inaktivera eller aktivera en slutpunkt](disable-or-enable-an-endpoint.md)
