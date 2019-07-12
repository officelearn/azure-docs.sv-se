---
title: Flytta en offentlig peering till Microsoft-peering - Azure ExpressRoute | Microsoft Docs
description: Den här artikeln beskrivs stegen för att flytta din offentlig peering till Microsoft-peering i ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592151"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta en offentlig peering till Microsoft-peering

Den här artikeln hjälper dig att flytta en offentlig peering-konfigurationen till Microsofts peering utan avbrott. ExpressRoute stöder användning av Microsoft-peering med flödesfilter för Azure PaaS-tjänster, till exempel Azure Storage och Azure SQL Database. Nu behöver du endast en routningsdomän för åtkomst till Microsoft PaaS- och SaaS-tjänster. Du kan använda flödesfilter för att selektivt presentera PaaS-tjänstprefixen för Azure-regioner du vill använda.

Azures offentliga peering har 1 NAT IP-adress som är kopplade till varje BGP-sessionen. Microsoft-peering kan du konfigurera din egen NAT-allokeringar, samt använda flödesfilter för selektiv prefix annonser. Offentlig Peering är en enkelriktad tjänst med vilken anslutning initieras alltid från ditt WAN-nätverk till Microsoft Azure-tjänster. Microsoft Azure-tjänster kommer inte att kunna initiera anslutningar till nätverket via den här routningsdomän.

När offentlig peering är aktiverat kan ansluta du till alla Azure-tjänster. Vi kan inte du selektivt välja tjänster som vi annonsera vägar till. Microsoft-peering är en dubbelriktad anslutning där anslutningen kan initieras från Microsoft Azure-tjänst tillsammans med ditt WAN. Mer information om routning domäner och peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).

## <a name="before"></a>Innan du börjar

För att ansluta till Microsoft-peering, måste du konfigurera och hantera NAT. Anslutningsleverantören kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att få åtkomst till Azure PaaS och SaaS för Azure-tjänster på Microsoft-peering, är det viktigt att storleksanpassa NAT IP-adresspool på rätt sätt. Mer information om NAT för ExpressRoute finns i den [NAT-krav för Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). När du ansluter till Microsoft via Azure ExpressRoute(Microsoft peering), har du flera länkar till Microsoft. En länk är din befintliga Internetanslutning och den andra är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.

![Dubbelriktad anslutning](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.

Referera till [asymmetrisk routning med flera nätverksvägar](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) för varningar med asymmetriska routning innan du konfigurerar Microsoft-peering.

* Om du använder offentlig peering och för närvarande har IP-nätverk regler för offentliga IP-adresser som används för att komma åt [Azure Storage](../storage/common/storage-network-security.md) eller [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), måste du kontrollera att NAT IP-adresspoolen konfigurerats med Microsoft ingår-peering i listan över offentliga IP-adresser för Azure storage-konto eller Azure SQL-konto.<br>
* För att flytta till Microsoft-peering utan avbrott, använder du stegen i den här artikeln i den ordning som de visas.

## <a name="create"></a>1. Skapa Microsoft-peering

Om Microsoft-peering inte har skapats, använder du någon av följande artiklar för att skapa Microsoft-peering. Om din anslutning tjänstleverantören erbjuder Hanterade layer 3-tjänster, kan du be anslutningsleverantören att aktivera Microsoft-peering för din krets.

Om nivå 3 hanteras av dig krävs följande information innan du fortsätter:

* Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.<br>
* Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.<br>
* Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.<br>
* AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.<br>
* Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.<br>
* Routningens registernamn: Du kan ange RIR / IR mot vilken AS-numret och prefixet är registrerade.

* **Valfritt** -kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS number, kan du ange det AS-nummer som de är registrerade.<br>
* **Valfritt** – en MD5-hash om du väljer att använda en.

Detaljerade anvisningar för att aktivera Microsoft-peering finns i följande artiklar:

* [Skapa Microsoft-peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Skapa Microsoft-peering med hjälp av Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Skapa Microsoft-peering med hjälp av Azure CLI](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Verifiera Microsoft-peering är aktiverat

Kontrollera att Microsoft-peering är aktiverat och annonserade offentliga prefix har statusen konfigurerade.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurera och koppla ett flödesfilter till kretsen

Som standard nya Microsoft-peering annonserar inte alla prefix tills ett flödesfilter ansluts till kretsen. När du skapar en flödesfilterregeln kan ange du listan över tjänstwebbgrupper för Azure-regioner som du vill använda för Azure PaaS-tjänster. Detta ger dig flexibilitet för att filtrera vägar enligt dina krav, enligt följande skärmbild:

![Sammanfoga offentlig peering](./media/how-to-move-peering/routefilter.jpg)

Konfigurera routningsfilter med hjälp av någon av följande artiklar:

* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure portal](how-to-routefilter-portal.md)<br>
* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Ta bort offentlig peering

När du har verifierat att den Microsoft-peering har konfigurerats och de prefix som du vill använda annonseras korrekt i Microsoft-peering, du kan sedan ta bort den offentliga peeringen. Ta bort offentlig peering genom att använda någon av följande artiklar:

* [Ta bort Azures offentliga peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Ta bort Azures offentliga peering med hjälp av Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Ta bort Azures offentliga peering med hjälp av CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Visa peerkopplingar
  
Du kan se en lista över alla ExpressRoute-kretsar och peer-kopplingar i Azure-portalen. Mer information finns i [se Microsofts peering-information](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
