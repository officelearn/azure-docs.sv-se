---
title: Om att uppgradera krets bandbredd | Azure-ExpressRoute
description: I den här artikeln lär du dig metod tips för att uppgradera ExpressRoute-kretsens bandbredd
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: 9f44cbd9b735839640702970a65de239b36b3f30
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209809"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Om att uppgradera ExpressRoute-kretsens bandbredd

ExpressRoute möjliggör dedikerad och privat anslutning till Microsofts globala nätverk. Anslutningen under lättas av en ExpressRoute-partners nätverk eller en direkt anslutning till MSEE: N-enheterna (Microsoft Enterprise Edge). När den fysiska anslutningen har kon figurer ATS och testats kan du aktivera Layer-2-och Layer-3-anslutning genom att skapa en ExpressRoute-krets och konfigurera peering.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Uppgradera krets bandbredd

För att kunna uppgradera krets bandbredden måste ExpressRoute Direct-eller ExpressRoute-partner ha [tillräckligt med tillgänglig bandbredd](#considerations) för att uppgraderingen ska lyckas.

Om kapaciteten är tillgänglig kan du uppgradera kretsen med följande metoder:

* [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Överväganden för kapaciteter

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Otillräcklig bandbredd för ExpressRoute-partner

Om ExpressRoute-partnern inte har tillräckligt med kapacitet måste du skapa en ny krets som kon figurer ATS för den önskade bandbredden. För att upprätthålla anslutningen ska du inte ta bort den gamla kretsen förrän den nyskapade kretsen har etablerats, peering har kon figurer ATS och (för privat peering) anslutnings objekt till ExpressRoute virtuella nätverksgateway har etablerats.

Om din ExpressRoute-partner inte har tillräckligt med tillgänglig kapacitet måste du begära ytterligare kapacitet på den önskade peering-platsen. När den nya kapaciteten har allokerats kan du använda stegen i artikeln i avsnittet [bandbredd för uppgraderings krets](#upgrade) för att skapa en ny krets, konfigurera anslutning och ta bort den gamla kretsen:


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Otillräcklig ExpressRoute Direct-bandbredd

Om ExpressRoute Direct inte har tillräckligt med kapacitet kan du antingen ta bort kretsar som är kopplade till den ExpressRoute Direct-resurs som inte längre behövs eller skapa en ny ExpressRoute Direct-resurs. Vägledning för hur du hanterar ExpressRoute Direct-resursen finns i [så här konfigurerar du ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa och ändra en krets](expressroute-howto-circuit-portal-resource-manager.md)
* [Skapa och ändra peering-konfiguration](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
