---
title: Planera virtuella Azure-nätverk | Microsoft Docs
description: Lär dig hur du planerar för virtuella nätverk som baseras på isolering-, anslutnings- och platskrav.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: jdial
ms.openlocfilehash: fd290420c2c755e07f6949750e3a88bcb64682f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656915"
---
# <a name="plan-virtual-networks"></a>Planera virtuella nätverk

Det är enkelt att skapa ett virtuellt nätverk om du vill experimentera med, men troligen har du ska distribuera flera virtuella nätverk över tid för att stödja produktion behoven för din organisation. Med lite planering kommer du att kunna distribuera virtuella nätverk och ansluta de resurser du behöver mer effektivt. Informationen i den här artikeln är mest användbart om du redan är bekant med virtuella nätverk och har viss erfarenhet av att arbeta med dem. Om du inte är bekant med virtuella nätverk, bör du läsa [översikt över virtuella nätverk](virtual-networks-overview.md).

## <a name="naming"></a>Namngivning

Alla Azure-resurser ha ett namn. Namnet måste vara unika inom ett område som kan variera för varje resurstyp av. Till exempel namnet på ett virtuellt nätverk måste vara unika inom en [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), men kan flera gånger i en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) eller Azure [region](https://azure.microsoft.com/regions/#services). Definiera en namngivningskonvention som du kan använda konsekvent när naming resurser är användbart när du hanterar flera nätverksresurser över tid. Förslag finns [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Regioner

Alla Azure-resurser skapas i en Azure-region och prenumeration. En resurs kan bara skapas i ett virtuellt nätverk som finns i samma region och prenumerationen som resursen. Däremot kan du ansluta virtuella nätverk som finns i olika prenumerationer och regioner. Mer information finns i [anslutning](#connectivity). När du bestämmer vilka regioner att distribuera resurser i övervägas där konsumenter av resurser befinner sig fysiskt:

- Konsumenter av resurser vill förmodligen den lägsta Nätverksfördröjningen till sina resurser. Information om relativa fördröjningar mellan en angiven plats och Azure-regioner finns [visa relativa svarstiderna](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Har du kraven för land, suveränitet, kompatibilitet eller återhämtning? I så fall, är det viktigt att välja den region som överensstämmer med kraven. Mer information finns i [Azure geografiska områden](https://azure.microsoft.com/global-infrastructure/geographies/).
- Du behöver återhämtning över Azure tillgänglighet zoner inom samma Azure-region för de resurser som du distribuerar? Du kan distribuera resurser, till exempel virtuella datorer (VM) till olika tillgänglighet zoner inom samma virtuella nätverk. Inte alla Azure-regioner stöder dock tillgänglighet zoner. Mer information om tillgänglighet zoner och regioner som stöds finns [tillgänglighet zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Prenumerationer

Du kan distribuera så många virtuella nätverk som krävs inom varje prenumeration upp till den [gränsen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Vissa organisationer har olika prenumerationer för olika avdelningar, till exempel. Mer information och överväganden kring prenumerationer finns i [prenumeration styrning](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentering

Du kan skapa flera virtuella nätverk per prenumeration och region. Du kan skapa flera undernät i varje virtuellt nätverk. Överväganden som följer hjälpa dig att avgöra hur många virtuella nätverk och undernät som du behöver:

### <a name="virtual-networks"></a>Virtuella nätverk

Ett virtuellt nätverk är en virtuell, isolerade del av det offentliga Azure-nätverket. Varje virtuellt nätverk är dedikerad till din prenumeration. Saker att tänka på när du bestämmer dig att skapa ett virtuellt nätverk eller flera virtuella nätverk på en prenumeration:

- Alla organisationens säkerhetskrav finns för att isolera trafiken i separata virtuella nätverk? Du kan välja att ansluta virtuella nätverk eller inte. Om du ansluter virtuella nätverk, kan du implementera en virtuell nätverksenhet, till exempel en brandvägg för att kontrollera trafikflödet mellan virtuella nätverk. Mer information finns i [säkerhet](#security) och [anslutning](#connectivity).
- Gör eventuella organisatoriska krav som finns för att isolera virtuella nätverk i separata [prenumerationer](#subscriptions) eller [regioner](#regions)?
- En [nätverksgränssnittet](virtual-network-network-interface.md) gör att en virtuell dator att kommunicera med andra resurser. Varje nätverksgränssnitt har en eller flera privata IP-adresser kopplade till den. Hur många nätverksgränssnitt och [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) behöver du i ett virtuellt nätverk? Det finns [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) antalet nätverksgränssnitt och privata IP-adresser som du kan ha ett virtuellt nätverk.
- Vill du ansluta virtuella nätverk till ett annat virtuellt nätverk eller lokala nätverk? Du kan välja att ansluta vissa virtuella nätverk till varandra eller lokala nätverk, men inte andra. Mer information finns i [anslutning](#connectivity). Varje virtuellt nätverk som du ansluter till ett annat virtuellt nätverk eller lokala nätverk måste ha ett unikt adressutrymme. Varje virtuellt nätverk har en eller flera offentliga eller privata adressintervall tilldelats dess adressutrymme. Ett adressintervall anges i classless internet domän routning (CIDR) format, till exempel 10.0.0.0/16. Lär dig mer om [-adressintervall](manage-virtual-network.md#add-or-remove-an-address-range) för virtuella nätverk.
- Har du alla organisationens administration krav för resurser i olika virtuella nätverk? Om så är fallet bör du kan dela resurser i separat virtuellt nätverk för att förenkla [behörighetstilldelningen](#permissions) till personer i din organisation eller för att tilldela olika [principer](#policies) till olika virtuella nätverk.
- När du distribuerar några resurser i Azure-tjänst till ett virtuellt nätverk kan skapa de sina egna virtuella nätverk. För att avgöra om en Azure-tjänst skapar egna virtuella nätverk finns information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Undernät

Ett virtuellt nätverk kan segmenterat till ett eller flera undernät upp till den [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Saker att tänka på när du bestämmer dig att skapa ett undernät eller flera virtuella nätverk på en prenumeration:

- Varje undernät måste ha ett unikt adressintervall som anges i CIDR-format, inom adressutrymmet för det virtuella nätverket. Adressintervallet får inte överlappa med andra undernät i det virtuella nätverket.
- Om du planerar att distribuera resurser i Azure-tjänsten till ett virtuellt nätverk, kan de kräver, eller skapa egna undernät, det måste vara tillräckligt mycket ledigt utrymme att göra det. För att avgöra om en Azure-tjänst skapas sitt eget undernät, visas information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Om du ansluter ett virtuellt nätverk till ett lokalt nätverk med hjälp av en Azure VPN-Gateway, måste det virtuella nätverket har en dedikerad undernät för gatewayen. Lär dig mer om [gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure skickar nätverkstrafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan åsidosätta Azures standard routning att hindra Azure routning mellan undernät eller vidarebefordra trafik mellan undernät via en virtuell nätverksenhet, t.ex. Om du behöver trafiken mellan resurser i samma flöde för virtuellt nätverk via en virtuell nätverksenhet (NVA) kan du distribuera resurserna till olika undernät. Läs mer i [säkerhet](#security).
- Du kan begränsa åtkomst till Azure-resurser, till exempel ett Azure storage-konto eller Azure SQL database till specifika undernät med en tjänstslutpunkt för virtuellt nätverk. Dessutom kan du neka åtkomst till resurser från internet. Du kan skapa flera undernät och aktivera en tjänstslutpunkt för vissa undernät, men inte andra. Lär dig mer om [tjänstens slutpunkter](virtual-network-service-endpoints-overview.md), och Azure-resurser du kan aktivera dem för.
- Du kan associera noll eller en säkerhetsgrupp för nätverk för varje undernät i ett virtuellt nätverk. Du kan associera samma eller en annan, nätverk säkerhetsgrupp för varje undernät. Varje säkerhetsgrupp för nätverk innehåller regler som tillåter eller nekar trafik till och från källor och mål. Lär dig mer om [nätverkssäkerhetsgrupper](#traffic-filtering).

## <a name="security"></a>Säkerhet

Du kan filtrera nätverkstrafik till och från resurser i ett virtuellt nätverk med nätverkssäkerhetsgrupper och virtuella nätverksenheter. Du kan styra hur Azure dirigerar trafik från undernät. Du kan också begränsa vem i din organisation kan arbeta med resurserna i virtuella nätverk.

### <a name="traffic-filtering"></a>Trafikfiltrering

- Du kan filtrera nätverkstrafiken mellan resurser i ett virtuellt nätverk med en nätverkssäkerhetsgrupp, en NVA som filtrerar trafik eller båda. För att distribuera en NVA, till exempel en brandvägg för att filtrera nätverkstrafik, finns det [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). När du använder en NVA kan skapa du också anpassade vägar för att dirigera trafik från undernät till en NVA. Lär dig mer om [trafikroutning](#traffic-routing).
- En nätverkssäkerhetsgrupp innehåller flera säkerhet standardregler som tillåter eller nekar trafik till eller från resurser. En nätverkssäkerhetsgrupp kan vara kopplad till ett nätverksgränssnitt, nätverksgränssnittet är i undernätet eller båda. Om du vill förenkla hanteringen av säkerhetsregler rekommenderar vi att du kopplar en nätverkssäkerhetsgrupp till enskilda undernät i stället för enskilda nätverksgränssnitt i undernätet, när det är möjligt.
- Om olika regler olika virtuella datorer i ett undernät måste associera du nätverksgränssnitt på den virtuella datorn till en eller flera säkerhetsgrupper för programmet. En säkerhetsregel kan ange en säkerhetsgrupp för programmet i källan, mål- eller båda. Regeln endast gäller för nätverksgränssnitt som är medlemmar i säkerhetsgruppen program. Lär dig mer om [nätverkssäkerhetsgrupper](security-overview.md) och [programmet säkerhetsgrupper](security-overview.md#application-security-groups).
- Azure skapar flera standard säkerhetsregler inom varje nätverkssäkerhetsgruppen. En standardregel kan alla trafik mellan alla resurser i ett virtuellt nätverk. Åsidosätta detta genom att använda nätverkssäkerhet grupper, anpassad routning för att dirigera trafik till en NVA eller båda. Vi rekommenderar att du bekanta dig med alla Azures [standard säkerhetsregler](security-overview.md#default-security-rules) och förstå hur reglerna för nätverkssäkerhetsgrupper tillämpas på en resurs.

Du kan visa exempel Designer för att implementera en DMZ mellan Azure och internet med en [NVA](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) eller [nätverkssäkerhetsgrupper](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>Routning av nätverkstrafik

Azure skapar flera standardvägar för utgående trafik från ett undernät. Du kan åsidosätta Azures standard routning genom att skapa en routingtabell och koppla det till ett undernät. Vanliga orsaker för åsidosättning av Azures standard routning är:
- Eftersom du vill att trafik mellan undernät att strömma genom en NVA. Mer information om hur du [konfigurera routningstabeller för att tvinga trafik genom en NVA](tutorial-create-route-table-portal.md).
- Eftersom du vill tvinga alla internet-bunden trafik genom en NVA eller lokalt, via en Azure VPN-gateway. Att tvinga internet-trafik lokalt för granskning och loggning kallas ofta Tvingad tunneltrafik. Mer information om hur du konfigurerar [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Om du behöver implementera anpassad routning bör du bekanta dig med [routning i Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Anslutning

Du kan ansluta ett virtuellt nätverk till andra virtuella nätverk som använder virtuella nätverk peering eller till ditt lokala nätverk med en Azure VPN-gateway.

### <a name="peering"></a>Peering

När du använder [virtuellt nätverk peering](virtual-network-peering-overview.md), virtuella nätverk kan vara i samma eller olika, Azure-regioner som stöds. Virtuella nätverk kan vara i de samma eller olika Azure-prenumerationerna, förutsatt att båda prenumerationer har tilldelats samma Azure Active Directory-klienten. Innan du skapar en peering, bör du bekanta dig med alla peering [kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints). Bandbredden mellan resurser i virtuella nätverk är peerkopplat i samma region är samma som om resurserna som fanns i samma virtuella nätverk.

### <a name="vpn-gateway"></a>VPN gateway

Du kan använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta ett virtuellt nätverk till ditt lokala nätverk med hjälp av en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller att använda en dedikerad anslutning med Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan kombinera peering och en VPN-gateway för att skapa [NAV och ekrar nätverk](/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)där ekrar virtuella nätverk ansluta till ett virtuellt nätverk med nav och navet ansluter till ett lokalt nätverk, t.ex.

### <a name="name-resolution"></a>Namnmatchning

Resurser i ett virtuellt nätverk går inte att matcha namnen på de resurser i ett peered virtuellt nätverk med Azures [inbyggda DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Att matcha namn i ett peered virtuellt nätverk [Distribuera DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), eller använda Azure DNS [privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Matcha namn mellan resurser i ett virtuellt nätverk och lokala nätverk måste du även distribuera DNS-servern.

## <a name="permissions"></a>Behörigheter

Azure använder [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) till resurser. Behörigheter som har tilldelats en [omfång](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) i följande hierarki: prenumeration, hanteringsgruppen, resursgruppen och enskild resurs. Mer information om hierarkin finns [ordna dina resurser](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill arbeta med virtuella Azure-nätverk och alla deras relaterade funktioner, till exempel peering, nätverkssäkerhetsgrupper, slutpunkter och vägtabeller, kan du tilldela medlemmar i din organisation inbyggt [ägare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), eller [Network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roller och sedan tilldela rollen till en lämplig omfattning. Om du vill tilldela specifika behörigheter för en delmängd av funktionerna för virtuellt nätverk, skapar du en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och tilldela särskilda behörigheter som krävs för [virtuella nätverk](manage-virtual-network.md#permissions), [ undernät och Tjänsteslutpunkter](virtual-network-manage-subnet.md#permissions), [nätverksgränssnitt](virtual-network-network-interface.md#permissions), [peering](virtual-network-manage-peering.md#permissions), [nätverks- och säkerhetsgrupper](manage-network-security-group.md#permissions), eller [routningstabeller](manage-route-table.md#permissions) till rollen.

## <a name="policy"></a>Princip

Azure princip kan du skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpa olika regler över dina resurser, så att resurserna som uppfyller organisationens normer och servicenivåavtal. Azure princip körs en utvärdering av dina resurser, söka efter resurser som inte är kompatibla med principdefinitioner som du har. Du kan till exempel definiera och tillämpa en princip som tillåter generering av virtuella nätverk i en specifik resursgrupp eller region. En annan princip kan kräva att alla undernät har en nätverkssäkerhetsgrupp som är kopplade till den. Principerna utvärderas sedan när du skapar och uppdaterar resurser.

Principer tillämpas på följande hierarki: prenumeration, hanteringsgrupp och resursgruppen. Lär dig mer om [Azure princip](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller distribuera ett virtuellt nätverk [Principmall](policy-samples.md) prover.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om alla aktiviteter, inställningar och alternativ för en [virtuellt nätverk](manage-virtual-network.md), [undernät och tjänsten endpoint](virtual-network-manage-subnet.md), [nätverksgränssnittet](virtual-network-network-interface.md), [peering](virtual-network-manage-peering.md), [nätverks- och säkerhetsgrupp](manage-network-security-group.md), eller [routningstabellen](manage-route-table.md).
