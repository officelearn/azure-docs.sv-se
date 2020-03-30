---
title: 'Azure ExpressRoute: Flytta en offentlig peering till Microsoft-peering'
description: I den här artikeln visas stegen för att flytta din offentliga peering till Microsoft-peering på ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436845"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta offentlig peering till Microsoft-peering

Den här artikeln hjälper dig att flytta en offentlig peering-konfiguration till Microsoft-peering utan driftstopp. ExpressRoute stöder användning av Microsoft-peering med flödesfilter för Azure PaaS-tjänster, till exempel Azure Storage och Azure SQL Database. Nu behöver du endast en routningsdomän för åtkomst till Microsoft PaaS- och SaaS-tjänster. Du kan använda flödesfilter för att selektivt presentera PaaS-tjänstprefixen för Azure-regioner du vill använda.

Offentlig Azure-peering har 1 NAT IP-adress som är associerad med varje BGP-session. Med Microsoft-peering kan du konfigurera dina egna NAT-allokeringar samt använda flödesfilter för selektiva prefixannonser. Offentlig peering är en enkelriktad tjänst som använder som anslutning alltid initieras från WAN till Microsoft Azure-tjänster. Microsoft Azure-tjänster kan inte initiera anslutningar till nätverket via den här routningsdomänen.

När offentlig peering är aktiverad kan du ansluta till alla Azure-tjänster. Vi tillåter dig inte att selektivt välja tjänster som vi annonserar vägar till. Medan Microsoft-peering är en dubbelriktad anslutning där anslutningen kan initieras från Microsoft Azure-tjänsten tillsammans med ditt WAN. Mer information om routning av domäner och peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Om du vill ansluta till Microsoft-peering måste du konfigurera och hantera NAT. Anslutningsleverantören kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att komma åt Azure PaaS- och Azure SaaS-tjänsterna på Microsoft-peering är det viktigt att du lägger upp NAT IP-poolen korrekt. Mer information om NAT för ExpressRoute finns i [NAT-kraven för Microsoft peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). När du ansluter till Microsoft via Azure ExpressRoute (Microsoft peering) har du flera länkar till Microsoft. En länk är din befintliga Internetanslutning och den andra är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.

![Dubbelriktad anslutning](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.

Se [asymmetrisk routning med flera nätverkssökvägar](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) för varningar om asymmetrisk routning innan du konfigurerar Microsoft-peering.

* Om du använder offentlig peering och för närvarande har IP-nätverksregler för offentliga IP-adresser som används för att komma åt [Azure Storage](../storage/common/storage-network-security.md) eller [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)måste du se till att NAT IP-poolen som konfigurerats med Microsoft-peering ingår i listan över offentliga IP-adresser för Azure-lagringskontot eller Azure SQL-kontot.<br>
* Om du vill flytta till Microsoft-peering utan driftstopp använder du stegen i den här artikeln i den ordning de presenteras.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Skapa Microsoft-peering

Om Microsoft-peering inte har skapats använder du någon av följande artiklar för att skapa Microsoft-peering. Om din anslutningsleverantör erbjuder hanterade layer 3-tjänster kan du be anslutningsleverantören att aktivera Microsoft-peering för din krets.

Om lagret 3 hanteras av dig krävs följande information innan du fortsätter:

* Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.<br>
* Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.<br>
* Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.<br>
* AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.<br>
* Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.<br>
* Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.

* **Valfritt** - Kundens ASN: Om du annonserar prefix som inte är registrerade till peering AS-numret kan du ange det AS-nummer som de är registrerade till.<br>
* **Valfritt** - En MD5-hash om du väljer att använda en.

Detaljerade instruktioner för att aktivera Microsoft-peering finns i följande artiklar:

* [Skapa Microsoft-peering med Azure-portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Skapa Microsoft-peering med Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Skapa Microsoft-peering med Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Validera Microsoft-peering är aktiverat

Kontrollera att Microsoft-peering är aktiverat och att de annonserade offentliga prefixen är i konfigurerat tillstånd.

* [Azure-portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Konfigurera och koppla ett flödesfilter till kretsen

Som standard annonserar ny Microsoft-peering inga prefix förrän ett flödesfilter är kopplat till kretsen. När du skapar en vägfilterregel kan du ange en lista över tjänstgrupper för Azure-regioner som du vill använda för Azure PaaS-tjänster. Detta ger dig flexibiliteten att filtrera rutterna enligt dina krav, som visas i följande skärmdump:

![Koppla offentlig peering](./media/how-to-move-peering/routefilter.jpg)

Konfigurera flödesfilter med någon av följande artiklar:

* [Konfigurera flödesfilter för Microsoft-peering med Azure-portal](how-to-routefilter-portal.md)<br>
* [Konfigurera flödesfilter för Microsoft-peering med Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurera flödesfilter för Microsoft-peering med Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Ta bort den offentliga peering

När du har kontrollerat att Microsoft-peering är konfigurerat och de prefix du vill använda annonseras korrekt på Microsoft-peering, kan du sedan ta bort den offentliga peering. Om du vill ta bort den offentliga peeringen använder du någon av följande artiklar:

* [Ta bort offentlig Azure-peering med Azure PowerShell](about-public-peering.md#powershell)
* [Ta bort offentlig Azure-peering med CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Visa peerings
  
Du kan se en lista över alla ExpressRoute-kretsar och peerings i Azure-portalen. Mer information finns i [Visa information om Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
