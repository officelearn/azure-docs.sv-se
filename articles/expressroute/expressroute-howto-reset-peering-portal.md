---
title: 'Azure-ExpressRoute: återställa krets-peering med hjälp av Azure Portal'
description: Lär dig hur du inaktiverar och aktiverar peering för en Azure ExpressRoute-krets med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582988"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Återställ ExpressRoute krets-peering Använd Azure Portal

Den här artikeln beskriver hur du inaktiverar och aktiverar peering för en ExpressRoute-krets med hjälp av Azure Portal. När du inaktiverar en peering kommer BGP-sessionen för både den primära och den sekundära anslutningen av ExpressRoute-kretsen att stängas av. Du förlorar anslutningen genom denna peering till Microsoft. När du aktiverar en peering skapas BGP-sessionen på både den primära och den sekundära anslutningen av ExpressRoute-kretsen. Du får en anslutning via denna peering till Microsoft. Du kan aktivera och inaktivera Microsoft-peering och Azures privata peering på en ExpressRoute-krets oberoende av varandra. Första gången du konfigurerar peer-kopplingarna på ExpressRoute-kretsen är peer-kopplingarna aktiverade som standard.

Det finns ett par scenarier där du kan få hjälp att återställa dina ExpressRoute-peer-datorer.
* Testa din design och implementering av haveri beredskap. Du kan till exempel ha två ExpressRoute-kretsar. Du kan inaktivera peering-kopplingar för en krets och tvinga nätverks trafiken att växla över till den andra kretsen.
* Aktivera identifiering av dubbelriktad vidarebefordran (BFD) på Azures privata peering eller Microsoft-peering av din ExpressRoute-krets. BFD aktive ras som standard i Azures privata peering om din ExpressRoute-krets skapas efter 1 2018 augusti och på Microsoft-peering. Om din ExpressRoute-krets skapas efter 10 2020 januari. Om din krets skapades tidigare var BFD inte aktiverat. Du kan aktivera BFD genom att inaktivera peering och återaktivera den. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

## <a name="reset-a-peering"></a>Återställa en peer koppling

1. Välj den krets du vill göra ändringar i konfigurationen av peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute krets List":::

1. Välj den peering-konfiguration som du vill aktivera eller inaktivera.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Översikt över ExpressRoute-krets":::

1. Avmarkera **Aktivera peering** och välj **Spara** för att inaktivera peering-konfigurationen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Inaktivera privat peering":::

1. Du kan aktivera peering igen genom att kontrol lera **aktivera peer** koppling och välja **Spara**.

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp med att felsöka ett ExpressRoute-problem kan du läsa följande artiklar:
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverks prestanda](expressroute-troubleshooting-network-performance.md)
