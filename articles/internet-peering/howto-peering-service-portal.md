---
title: Aktivera Peering Service på en Direct-peering med hjälp av portalen
titleSuffix: Azure
description: Aktivera Peering Service på en Direct-peering med hjälp av portalen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129881"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Aktivera Peering Service på en Direct-peering med hjälp av portalen

I den här artikeln beskrivs hur du aktiverar [peering-tjänst](overview-peering-service.md) på en direkt peering med hjälp av portalen.

Om du vill kan du slutföra den här guiden med [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) innan du börjar konfigurera.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänst på. Om du inte har en konverterar du en äldre direkt peering eller skapar en ny Direkt peering.
    * Om du vill konvertera en äldre Direct-peering följer du instruktionerna i [Konvertera en äldre Direct-peering till Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md).
    * Om du vill skapa en ny direkt peering följer du instruktionerna i [Skapa eller ändrar en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har öppnat Direkt peering i föregående steg aktiverar du den för peering-tjänst.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutningsinställningarna läser du **Ändra ett direkt peering-avsnitt** i [Skapa eller ändrar en direkt peering med hjälp av portalen](howto-direct-portal.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Vanliga frågor och svar finns i Vanliga frågor och svar om [peering-tjänsten](service-faqs.md).