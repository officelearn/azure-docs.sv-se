---
title: Planera Azure-nätverk | Microsoft Docs
description: Lär dig hur du planerar för virtuella nätverk baserat på din isolering, anslutningar och platskrav.
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
ms.openlocfilehash: cf540caebd5f993cdba0d85f4109a6e78e201658
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378762"
---
# <a name="plan-virtual-networks"></a>Planera virtuella datornätverk

Det är enkelt att skapa ett virtuellt nätverk för att experimentera med, men det finns goda chanser att du ska distribuera flera virtuella nätverk över tid för produktion-behoven i din organisation. Med lite planering kommer du att kunna distribuera virtuella nätverk och ansluta de resurser du behöver mer effektivt. Informationen i den här artikeln är mest användbart om du redan är bekant med virtuella nätverk och har viss erfarenhet av att arbeta med dem. Om du inte är bekant med virtuella nätverk, bör du läsa [översikt över virtuella nätverk](virtual-networks-overview.md).

## <a name="naming"></a>Namngivning

Alla Azure-resurser ha ett namn. Namnet måste vara unika inom en omfattning som kan variera för varje resurstyp. Till exempel namnet på ett virtuellt nätverk måste vara unika inom en [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), men kan vara flera gånger i en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) eller Azure [region](https://azure.microsoft.com/regions/#services). Det är bra att definiera en namngivningskonvention som du kan använda konsekvent när du namnger resurser när du hanterar flera nätverksresurser över tid. Förslag finns i [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Regioner

Alla Azure-resurser skapas i ett Azure-region och prenumeration. En resurs kan bara skapas i ett virtuellt nätverk som finns i samma region och prenumeration som resursen. Men du kan ansluta virtuella nätverk som finns i olika prenumerationer och regioner. Mer information finns i [anslutning](#connectivity). Tänk på att där resurserna som använder befinner sig fysiskt när du bestämmer vilken region du distribuerar resurser i:

- Konsumenter av resurser vill förmodligen den lägsta Nätverksfördröjningen till sina resurser. För att fastställa relativ fördröjning mellan en angiven plats och Azure-regioner, se [visa relativa svarstider](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Har du datakrav för datahemvist, datasuveränitet, efterlevnad eller återhämtning? I så, fall är det viktigt att välja den region som är knutet till kraven. Mer information finns i [geografiska Azure-områden](https://azure.microsoft.com/global-infrastructure/geographies/).
- Behöver du återhämtning Azures tillgänglighetszoner inom samma Azure-region för de resurser som du distribuerar? Du kan distribuera resurser, till exempel virtuella datorer (VM) till olika tillgänglighetszoner inom samma virtuella nätverk. Inte alla Azure-regioner stöder tillgänglighetszoner men. Läs mer om tillgänglighetszoner och de regioner som stöder dem i [tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Prenumerationer

Du kan distribuera så många virtuella nätverk som krävs för varje prenumeration, upp till den [gränsen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Vissa organisationer har olika prenumerationer för olika avdelningar, till exempel. Mer information och överväganden kring prenumerationer finns i [Prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentering

Du kan skapa flera virtuella nätverk per prenumeration och region. Du kan skapa flera undernät inom varje virtuellt nätverk. Överväganden som följer hjälper dig att avgöra hur många virtuella nätverk och undernät du behöver:

### <a name="virtual-networks"></a>Virtuella nätverk

Ett virtuellt nätverk är en virtuell, isolerad del av Azures offentliga nätverk. Varje virtuellt nätverk är dedikerad till din prenumeration. Saker att tänka på när du bestämmer dig att skapa ett virtuellt nätverk eller flera virtuella nätverk i en prenumeration:

- Eventuella organisationens säkerhetskrav finns för att isolera trafiken i separata virtuella nätverk? Du kan välja att ansluta virtuella nätverk eller inte. Om du ansluter virtuella nätverk kan implementera du en virtuell nätverksenhet, till exempel en brandvägg för att styra trafikflödet mellan virtuella nätverk. Mer information finns i [security](#security) och [anslutning](#connectivity).
- Gör alla organisatoriska krav som finns för att isolera virtuella nätverk i separata [prenumerationer](#subscriptions) eller [regioner](#regions)?
- En [nätverksgränssnittet](virtual-network-network-interface.md) gör det möjligt för en virtuell dator ska kunna kommunicera med andra resurser. Varje nätverksgränssnitt har en eller flera privata IP-adresser tilldelade till den. Hur många nätverksgränssnitt och [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) behöver du i ett virtuellt nätverk? Det finns [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) för antalet nätverksgränssnitt och privata IP-adresser som du kan ha inom ett virtuellt nätverk.
- Vill du ansluta virtuella nätverk till ett annat virtuellt nätverk eller lokala nätverk? Du kan välja att ansluta till vissa virtuella nätverk till varandra eller lokala nätverk, men inte för andra. Mer information finns i [anslutning](#connectivity). Varje virtuellt nätverk som du ansluter till ett annat virtuellt nätverk eller lokala nätverk måste ha ett unikt adressutrymme. Varje virtuellt nätverk har minst en offentlig eller privat adressintervall tilldelats sitt adressutrymme. Ett adressintervall har angetts i classless internet domän (CIDR)-format för routning, till exempel 10.0.0.0/16. Läs mer om [-adressintervall](manage-virtual-network.md#add-or-remove-an-address-range) för virtuella nätverk.
- Har du några krav på organisationens administration i resurser i olika virtuella nätverk? Om så du kan hålla resurser åtskilda i separata virtuella nätverk för att förenkla [behörighetstilldelningen](#permissions) till personer i din organisation eller för att tilldela olika [principer](#policies) till olika virtuella nätverk.
- När du distribuerar en Azure-tjänstresurser i ett virtuellt nätverk kan skapa de sina egna virtuella nätverk. För att avgöra om en Azure-tjänst skapas en egen virtuellt nätverk, se information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Undernät

Ett virtuellt nätverk kan segmenteras i ett eller flera undernät upp till den [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Saker att tänka på när du bestämmer dig att skapa ett undernät eller flera virtuella nätverk i en prenumeration:

- Varje undernät måste ha ett unikt adressintervall som anges i CIDR-format inom adressutrymmet för det virtuella nätverket. Adressintervallet får inte överlappa med andra undernät i det virtuella nätverket.
- Om du planerar att distribuera vissa Azure-tjänstresurser i ett virtuellt nätverk, kan de kräver, eller skapa sina egna undernät, det måste vara tillräckligt mycket ledigt utrymme för dem att göra detta. För att avgöra om en Azure-tjänst skapas ett eget undernät, se information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Om du ansluter ett virtuellt nätverk till ett lokalt nätverk med hjälp av Azure VPN Gateway, måste det virtuella nätverket ha ett dedikerat undernät för gatewayen. Läs mer om [gatewayundernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure dirigerar nätverkstrafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan åsidosätta Azures standard routning för att förhindra att Azure routning mellan undernät, eller att dirigera trafik mellan undernät via en virtuell nätverksenhet, till exempel. Om du behöver den trafiken mellan resurser i samma virtuella nätverk-flöde via en virtuell nätverksinstallation (NVA) kan du distribuera resurserna till olika undernät. Läs mer i [security](#security).
- Du kan begränsa åtkomsten till Azure-resurser, till exempel ett Azure storage-konto eller Azure SQL-databas till specifika undernät med en tjänstslutpunkt för virtuellt nätverk. Dessutom kan du neka åtkomst till resurser från internet. Du kan skapa flera undernät och aktivera en tjänstslutpunkt för vissa undernät, men inte för andra. Läs mer om [tjänstslutpunkter](virtual-network-service-endpoints-overview.md), och Azure-resurser kan du aktivera dem för.
- Du kan associera noll eller en nätverkssäkerhetsgrupp till varje undernät i ett virtuellt nätverk. Du kan associera samma eller en annan, nätverks-säkerhetsgrupp för varje undernät. Varje grupp innehåller regler som tillåter eller nekar trafik till och från källor och mål. Läs mer om [nätverkssäkerhetsgrupper](#traffic-filtering).

## <a name="security"></a>Säkerhet

Du kan filtrera nätverkstrafik till och från resurser i ett virtuellt nätverk med nätverkssäkerhetsgrupper och virtuella nätverksenheter. Du kan styra hur Azure dirigerar trafik från undernät. Du kan också begränsa vem i organisationen kan arbeta med resurser i virtuella nätverk.

### <a name="traffic-filtering"></a>Trafikfiltrering

- Du kan filtrera nätverkstrafik mellan resurser i ett virtuellt nätverk med en nätverkssäkerhetsgrupp, en NVA som filtrerar nätverkstrafik, eller båda. Om du vill distribuera en NVA, till exempel en brandvägg för att filtrera nätverkstrafik, den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). När du använder en NVA kan skapa du också anpassade vägar för att dirigera trafik från undernät till NVA. Läs mer om [trafikroutning](#traffic-routing).
- En nätverkssäkerhetsgrupp innehåller flera standard-säkerhetsregler som tillåter eller nekar trafik till eller från resurser. En nätverkssäkerhetsgrupp kan kopplas till ett nätverksgränssnitt, det undernät som nätverksgränssnittet finns i eller båda. Om du vill förenkla hanteringen av säkerhetsregler, rekommenderar vi att du associerar en nätverkssäkerhetsgrupp till enskilda undernät i stället för enskilda nätverksgränssnitt i undernätet, när det är möjligt.
- Om olika säkerhetsregler tillämpas på de olika virtuella datorer i ett undernät måste associera du nätverksgränssnittet i den virtuella datorn till en eller flera programsäkerhetsgrupper. En säkerhetsregel kan ange en programsäkerhetsgrupp i dess källa, mål, eller båda. Regeln sedan gäller endast för nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Läs mer om [nätverkssäkerhetsgrupper](security-overview.md) och [programsäkerhetsgrupper](security-overview.md#application-security-groups).
- Azure skapar flera säkerhetsregler i varje nätverkssäkerhetsgrupp. En standardregel tillåter all trafik mellan alla resurser i ett virtuellt nätverk. Om du vill åsidosätta detta använder du nätverkssäkerhet grupperar, anpassad routning för att dirigera trafik till en NVA eller båda. Vi rekommenderar att du bekanta dig med alla Azures [standardsäkerhetsregler](security-overview.md#default-security-rules) och förstå hur reglerna för nätverkssäkerhetsgrupper tillämpas på en resurs.

Du kan visa exempel utformning för att implementera en DMZ mellan Azure och internet med en [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) eller [nätverkssäkerhetsgrupper](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>Routning av nätverkstrafik

Azure skapar flera standardvägar för utgående trafik från ett undernät. Du kan åsidosätta Azures standard routning genom att skapa en routningstabell och kopplar den till ett undernät. Vanliga orsaker för att åsidosätta Azures standard routning är:
- Eftersom du vill att trafik mellan undernät kan passera en NVA. Mer information om hur du [konfigurera routningstabeller för att tvinga trafik via NVA](tutorial-create-route-table-portal.md).
- Eftersom du vill tvinga all internetbunden trafik via en NVA eller lokalt, via en Azure VPN-gateway. Att tvinga internet trafik lokalt för granskning och loggning kallas ofta Tvingad tunneltrafik. Mer information om hur du konfigurerar [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Om du behöver implementera anpassad routning, bör du bekanta dig med [routning i Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Anslutning

Du kan ansluta ett virtuellt nätverk till andra virtuella nätverk med peerkoppling av virtuella nätverk eller till ditt lokala nätverk, med en Azure VPN-gateway.

### <a name="peering"></a>Peering

När du använder [virtuell nätverkspeering](virtual-network-peering-overview.md), de virtuella nätverken kan finnas i samma eller olika, Azure-regioner som stöds. De virtuella nätverken kan finnas i samma eller olika Azure-prenumerationer (även prenumerationer som hör till olika Azure Active Directory-klienter). Innan du skapar en peering, bör du bekanta dig med alla peer-kopplingen [krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints). Bandbredd mellan resurser i virtuella nätverk peer-kopplats i samma region är samma som om resurserna fanns i samma virtuella nätverk.

### <a name="vpn-gateway"></a>VPN gateway

Du kan använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta ett virtuellt nätverk till ditt lokala nätverk med hjälp av en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller använda en dedikerad anslutning med Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan kombinera peering och en VPN-gateway för att skapa [NAV och ekrar nätverk](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), där virtuella ekernätverk ansluta till ett virtuellt nätverk med hub och hubben ansluter till ett lokalt nätverk, till exempel.

### <a name="name-resolution"></a>Namnmatchning

Resurser i ett virtuellt nätverk kan inte matcha namnen på resurser i ett peer-kopplat virtuellt nätverk med Azures [inbyggd DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Att matcha namn i ett peer-kopplade virtuella nätverk, [distribuera dina egna DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), eller använda Azure DNS [privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Namnmatchning mellan resurser i ett virtuellt nätverk och lokala nätverk måste du även distribuera dina egna DNS-server.

## <a name="permissions"></a>Behörigheter

Azure använder [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) till resurser. Behörigheter har tilldelats en [omfång](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) i följande hierarki: prenumeration, hanteringsgruppen, resursgrupp och enskild resurs. Läs mer om hierarkin i [organisera dina resurser](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill arbeta med Azure-nätverk och alla deras relaterade funktioner, till exempel peering, nätverkssäkerhetsgrupper, slutpunkter och routningstabeller du kan tilldela medlemmar i din organisation till inbyggt [ägare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), eller [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roller och sedan tilldela rollen till lämplig omfattning. Om du vill tilldela specifika behörigheter för en delmängd av funktionerna för virtuellt nätverk skapar du en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och tilldela specifika behörigheter som krävs för [virtuella nätverk](manage-virtual-network.md#permissions), [ undernät och tjänstslutpunkter](virtual-network-manage-subnet.md#permissions), [nätverksgränssnitt](virtual-network-network-interface.md#permissions), [peering](virtual-network-manage-peering.md#permissions), [nätverk och säkerhetsgrupper](manage-network-security-group.md#permissions), eller [routningstabeller](manage-route-table.md#permissions) till rollen.

## <a name="policy"></a>Princip

Azure Policy kan du skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpar olika regler på resurserna, så att resurserna kompatibla med din organisations normer och servicenivåavtal. Azure Policy kör en utvärdering av resurserna och söker efter resurser som inte är kompatibla med de principdefinitioner som du har. Du kan till exempel definiera och tillämpa en princip som tillåter generering av virtuella nätverk i en specifik resursgrupp eller region. En annan princip kan kräva att varje undernät som har en nätverkssäkerhetsgrupp som är associerade med den. Principerna utvärderas sedan resurser skapas och uppdateras.

Principer som används för följande hierarki: prenumerationen, hanteringsgrupp och resursgruppen. Läs mer om [Azure policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller distribuera ett virtuellt nätverk [Principmall](policy-samples.md) exempel.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om alla aktiviteter, inställningar och alternativ för en [virtuellt nätverk](manage-virtual-network.md), [undernät och tjänsten endpoint](virtual-network-manage-subnet.md), [nätverksgränssnittet](virtual-network-network-interface.md), [peering](virtual-network-manage-peering.md), [säkerhetsgrupp för nätverk och](manage-network-security-group.md), eller [routningstabellen](manage-route-table.md).
