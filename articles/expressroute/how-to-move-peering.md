---
title: 'Azure-ExpressRoute: flytta en offentlig peering till Microsoft-peering'
description: Den här artikeln beskrivs stegen för att flytta din offentlig peering till Microsoft-peering i ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f9ed841d347d99a2208971d164bbd7b9044e3b09
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031592"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta en offentlig peering till Microsoft-peering

Den här artikeln hjälper dig att flytta en offentlig peering-konfigurationen till Microsofts peering utan avbrott. ExpressRoute stöder användning av Microsoft-peering med flödesfilter för Azure PaaS-tjänster, till exempel Azure Storage och Azure SQL Database. Nu behöver du endast en routningsdomän för åtkomst till Microsoft PaaS- och SaaS-tjänster. Du kan använda flödesfilter för att selektivt presentera PaaS-tjänstprefixen för Azure-regioner du vill använda.

Offentlig Azure-peering har en NAT-IP-adress kopplad till varje BGP-session. Med Microsoft-peering kan du konfigurera dina egna NAT-allokeringar, samt använda väg filter för annonser av selektivt prefix. Offentlig peering är en enkelriktad tjänst som använder den anslutning som alltid initieras från WAN till Microsoft Azure-tjänster. Microsoft Azure-tjänster kommer inte att kunna initiera anslutningar till nätverket via den här routningsdomän.

När offentlig peering är aktiverat kan ansluta du till alla Azure-tjänster. Vi kan inte du selektivt välja tjänster som vi annonsera vägar till. Även om Microsoft-peering är en dubbelriktad anslutning där anslutning kan initieras från Microsoft Azure-tjänsten tillsammans med ditt WAN. Mer information om routningsdomäner och peering finns i ExpressRoute- [kretsar och routningsdomäner](expressroute-circuit-peerings.md).

## <a name="before"></a>Innan du börjar

För att ansluta till Microsoft-peering, måste du konfigurera och hantera NAT. Anslutningsleverantören kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att få åtkomst till Azure PaaS och SaaS för Azure-tjänster på Microsoft-peering, är det viktigt att storleksanpassa NAT IP-adresspool på rätt sätt. Mer information om NAT för ExpressRoute finns i den [NAT-krav för Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). När du ansluter till Microsoft via Azure ExpressRoute (Microsoft-peering) har du flera länkar till Microsoft. En länk är din befintliga Internetanslutning och den andra är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.

![Dubbelriktad anslutning](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.

Se [asymmetrisk routning med flera nätverks Sök vägar](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) för varningar från asymmetrisk routning innan du konfigurerar Microsoft-peering.

* Om du använder offentlig peering och för närvarande har IP-nätverk regler för offentliga IP-adresser som används för att komma åt [Azure Storage](../storage/common/storage-network-security.md) eller [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), måste du kontrollera att NAT IP-adresspoolen konfigurerats med Microsoft ingår-peering i listan över offentliga IP-adresser för Azure storage-konto eller Azure SQL-konto.<br>
* För att flytta till Microsoft-peering utan avbrott, använder du stegen i den här artikeln i den ordning som de visas.

## <a name="create"></a>1. skapa Microsoft-peering

Om Microsoft-peering inte har skapats, använder du någon av följande artiklar för att skapa Microsoft-peering. Om din anslutning tjänstleverantören erbjuder Hanterade layer 3-tjänster, kan du be anslutningsleverantören att aktivera Microsoft-peering för din krets.

Om skikt 3 hanteras av dig krävs följande information innan du fortsätter:

* Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.<br>
* Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.<br>
* Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.<br>
* AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.<br>
* Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.<br>
* Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.

* **Valfritt** -kund-ASN: om du annonserar prefix som inte har registrerats för peering som ett nummer kan du ange det as-nummer som de är registrerade för.<br>
* **Valfritt** – en MD5-hash om du väljer att använda en.

Detaljerade anvisningar om hur du aktiverar Microsoft-peering finns i följande artiklar:

* [Skapa Microsoft-peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Skapa Microsoft-peering med hjälp av Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Skapa Microsoft-peering med hjälp av Azure CLI](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Verifiera att Microsoft-peering har Aktiver ATS

Kontrollera att Microsoft-peering är aktiverat och annonserade offentliga prefix har statusen konfigurerade.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurera och koppla ett flödes filter till kretsen

Som standard annonserar inte nya Microsoft-peering några prefix förrän ett flödes filter är kopplat till kretsen. När du skapar en regel för flödes filter kan du ange en lista över service grupper för Azure-regioner som du vill använda för Azure PaaS Services. Detta ger dig flexibiliteten att filtrera vägarna efter behov, som du ser på följande skärm bild:

![Sammanfoga offentlig peering](./media/how-to-move-peering/routefilter.jpg)

Konfigurera routningsfilter med hjälp av någon av följande artiklar:

* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure portal](how-to-routefilter-portal.md)<br>
* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. ta bort offentlig peering

När du har verifierat att den Microsoft-peering har konfigurerats och de prefix som du vill använda annonseras korrekt i Microsoft-peering, du kan sedan ta bort den offentliga peeringen. Ta bort offentlig peering genom att använda någon av följande artiklar:

* [Ta bort Azures offentliga peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Ta bort Azures offentliga peering med hjälp av Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Ta bort Azures offentliga peering med hjälp av CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Visa peering
  
Du kan se en lista över alla ExpressRoute-kretsar och peer-kopplingar i Azure-portalen. Mer information finns i [se Microsofts peering-information](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
