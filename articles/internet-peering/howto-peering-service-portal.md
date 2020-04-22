---
title: Aktivera Azure Peering Service på en Direct-peering med hjälp av Azure-portalen
titleSuffix: Azure
description: Aktivera Azure Peering Service på en Direct-peering med hjälp av Azure-portalen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687063"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Aktivera Azure Peering Service på en Direct-peering med hjälp av Azure-portalen

I den här artikeln beskrivs hur du aktiverar Azure [Peering Service](overview-peering-service.md) på en direct-peering med hjälp av Azure-portalen.

Om du vill kan du slutföra den här guiden med [powershell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) innan du börjar konfigurera.
* Välj en direkt peering i din prenumeration som du vill aktivera Peering Service för. Om du inte har någon konverterar du en äldre direkt peering eller skapar en ny Direkt peering:
    * Om du vill konvertera en äldre Direkt peering följer du instruktionerna i [Konvertera en äldre Direkt peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md).
    * Om du vill skapa en ny direkt peering följer du instruktionerna i [Skapa eller ändrar en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har öppnat en direkt peering i föregående steg aktiverar du den för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du vill ändra anslutningsinställningarna läser du avsnittet Ändra en direkt peering i [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Vanliga frågor och svar finns i vanliga frågor och [svar om peering-tjänsten](service-faqs.md).