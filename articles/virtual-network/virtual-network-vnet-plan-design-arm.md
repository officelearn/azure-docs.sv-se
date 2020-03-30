---
title: Planera virtuella Azure-nätverk | Microsoft-dokument
description: Lär dig att planera virtuella datornätverk som baseras på dina krav på isolering, anslutningar och plats.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: 6e066d28afc4b0959b15284378cde682fbc05615
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190468"
---
# <a name="plan-virtual-networks"></a>Planera virtuella nätverk

Det är enkelt att skapa ett virtuellt nätverk att experimentera med, men chansen är stor att du distribuerar flera virtuella nätverk över tid för att stödja organisationens produktionsbehov. Med viss planering kommer du att kunna distribuera virtuella nätverk och ansluta de resurser du behöver mer effektivt. Informationen i den här artikeln är till stor hjälp om du redan är bekant med virtuella nätverk och har viss erfarenhet av att arbeta med dem. Om du inte är bekant med virtuella nätverk rekommenderar vi att du läser [översikt över virtuella nätverk](virtual-networks-overview.md).

## <a name="naming"></a>Namngivning

Alla Azure-resurser har ett namn. Namnet måste vara unikt inom ett scope som kan variera för varje resurstyp. Namnet på ett virtuellt nätverk måste till exempel vara unikt inom en [resursgrupp,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)men kan dupliceras i en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) eller [Azure-region](https://azure.microsoft.com/regions/#services). Det är praktiskt att definiera en namngivningskonvention som du kan använda konsekvent när du namnger resurser när du hanterar flera nätverksresurser över tid. Förslag finns i [Namngivningskonventioner](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Regioner

Alla Azure-resurser skapas i en Azure-region och prenumeration. En resurs kan bara skapas i ett virtuellt nätverk som finns i samma region och prenumeration som resursen. Du kan dock ansluta virtuella nätverk som finns i olika prenumerationer och regioner. Mer information finns i [anslutning](#connectivity). När du bestämmer vilka eller vilka regioner som ska distribueras i bör du tänka på var resursernas konsumenter är fysiskt placerade:

- Resurskonsumenter vill vanligtvis ha den lägsta nätverksfördröjningen för sina resurser. Information om hur du tar hänsyn till relativa svarstider mellan en angiven plats och Azure-regioner finns i [Visa relativa svarstider](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Har du krav på datahemvist, suveränitet, efterlevnad eller återhämtning? Om så är fallet är det viktigt att välja den region som anpassar sig till kraven. Mer information finns i [Azure-geografiska områden](https://azure.microsoft.com/global-infrastructure/geographies/).
- Kräver du återhämtning i Azure-tillgänglighetszoner inom samma Azure-region för de resurser du distribuerar? Du kan distribuera resurser, till exempel virtuella datorer (VM) till olika tillgänglighetszoner inom samma virtuella nätverk. Alla Azure-regioner stöder dock inte tillgänglighetszoner. Mer information om tillgänglighetszoner och de regioner som stöder dem finns i [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Prenumerationer

Du kan distribuera så många virtuella nätverk som krävs i varje prenumeration, upp till [gränsen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Vissa organisationer har olika prenumerationer för olika avdelningar, till exempel. Mer information och överväganden kring prenumerationer finns i [Prenumerationsstyrning](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentering

Du kan skapa flera virtuella nätverk per prenumeration och per region. Du kan skapa flera undernät i varje virtuellt nätverk. De överväganden som följer hjälper dig att avgöra hur många virtuella nätverk och undernät du behöver:

### <a name="virtual-networks"></a>Virtuella nätverk

Ett virtuellt nätverk är en virtuell, isolerad del av Det offentliga Azure-nätverket. Varje virtuellt nätverk är dedikerat till din prenumeration. Saker att tänka på när du bestämmer dig för om du vill skapa ett virtuellt nätverk eller flera virtuella nätverk i en prenumeration:

- Finns det några organisatoriska säkerhetskrav för att isolera trafik till separata virtuella nätverk? Du kan välja att ansluta virtuella nätverk eller inte. Om du ansluter virtuella nätverk kan du implementera en virtuell nätverksinstallation, till exempel en brandvägg, för att styra trafikflödet mellan de virtuella nätverken. Mer information finns i [säkerhet](#security) och [anslutning](#connectivity).
- Finns det några organisatoriska krav för att isolera virtuella nätverk till separata [prenumerationer](#subscriptions) eller [regioner?](#regions)
- Ett [nätverksgränssnitt](virtual-network-network-interface.md) gör det möjligt för en virtuell dator att kommunicera med andra resurser. Varje nätverksgränssnitt har tilldelats en eller flera privata IP-adresser. Hur många nätverksgränssnitt och [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) behöver du i ett virtuellt nätverk? Det finns [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) för antalet nätverksgränssnitt och privata IP-adresser som du kan ha i ett virtuellt nätverk.
- Vill du ansluta det virtuella nätverket till ett annat virtuellt nätverk eller lokalt nätverk? Du kan välja att ansluta vissa virtuella nätverk till varandra eller lokala nätverk, men inte andra. Mer information finns i [anslutning](#connectivity). Varje virtuellt nätverk som du ansluter till ett annat virtuellt nätverk, eller lokalt nätverk, måste ha ett unikt adressutrymme. Varje virtuellt nätverk har ett eller flera offentliga eller privata adressintervall som tilldelats adressutrymmet. Ett adressintervall anges i CIDR-format (Classless Internet Domain Routing), till exempel 10.0.0.0/16. Läs mer om [adressintervall](manage-virtual-network.md#add-or-remove-an-address-range) för virtuella nätverk.
- Har du några organisatoriska administrationskrav för resurser i olika virtuella nätverk? Om så är fallet kan du separera resurser i separata virtuella nätverk för att förenkla [behörighetstilldelningen](#permissions) till enskilda personer i organisationen eller för att tilldela olika principer till olika virtuella nätverk.
- När du distribuerar vissa Azure-tjänstresurser till ett virtuellt nätverk skapar de ett eget virtuellt nätverk. Information om huruvida en Azure-tjänst skapar ett eget virtuellt nätverk finns i information om varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Undernät

Ett virtuellt nätverk kan segmenteras i ett eller flera undernät upp till [gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Saker att tänka på när du bestämmer dig för om du vill skapa ett undernät eller flera virtuella nätverk i en prenumeration:

- Varje undernät måste ha ett unikt adressintervall, angivet i CIDR-format, inom det virtuella nätverkets adressutrymme. Adressintervallet kan inte överlappa med andra undernät i det virtuella nätverket.
- Om du planerar att distribuera vissa Azure-tjänstresurser till ett virtuellt nätverk kan de kräva eller skapa sitt eget undernät, så det måste finnas tillräckligt med oallokerat utrymme för att de ska kunna göra det. Information om huruvida en Azure-tjänst skapar ett eget undernät finns i information om varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Om du till exempel ansluter ett virtuellt nätverk till ett lokalt nätverk med hjälp av en Azure VPN-gateway måste det virtuella nätverket ha ett dedikerat undernät för gatewayen. Läs mer om [gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure dirigerar nätverkstrafik mellan alla undernät i ett virtuellt nätverk, som standard. Du kan åsidosätta Azures standardroutning för att förhindra Azure-routning mellan undernät eller för att dirigera trafik mellan undernät via en virtuell nätverksinstallation, till exempel. Om du behöver den trafiken mellan resurser i samma virtuella nätverksflöde via en virtuell nätverksinstallation (NVA) distribuerar du resurserna till olika undernät. Läs mer i [säkerhet](#security).
- Du kan begränsa åtkomsten till Azure-resurser, till exempel ett Azure-lagringskonto eller Azure SQL-databas, till specifika undernät med en slutpunkt för virtuella nätverkstjänster. Vidare kan du neka åtkomst till resurserna från Internet. Du kan skapa flera undernät och aktivera en tjänstslutpunkt för vissa undernät, men inte andra. Läs mer om [tjänstslutpunkter](virtual-network-service-endpoints-overview.md)och de Azure-resurser som du kan aktivera dem för.
- Du kan associera noll eller en nätverkssäkerhetsgrupp till varje undernät i ett virtuellt nätverk. Du kan associera samma eller en annan nätverkssäkerhetsgrupp till varje undernät. Varje nätverkssäkerhetsgrupp innehåller regler som tillåter eller nekar trafik till och från källor och destinationer. Läs mer om [nätverkssäkerhetsgrupper](#traffic-filtering).

## <a name="security"></a>Säkerhet

Du kan filtrera nätverkstrafik till och från resurser i ett virtuellt nätverk med hjälp av nätverkssäkerhetsgrupper och virtuella nätverksinstallationer. Du kan styra hur Azure dirigerar trafik från undernät. Du kan också begränsa vem i organisationen som kan arbeta med resurser i virtuella nätverk.

### <a name="traffic-filtering"></a>Trafikfiltrering

- Du kan filtrera nätverkstrafik mellan resurser i ett virtuellt nätverk med hjälp av en nätverkssäkerhetsgrupp, en NVA som filtrerar nätverkstrafik eller båda. Information om hur du distribuerar en NVA, till exempel en brandvägg, för att filtrera nätverkstrafik finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). När du använder en NVA skapar du också anpassade vägar för att dirigera trafik från undernät till NVA. Läs mer om [trafikroutning](#traffic-routing).
- En nätverkssäkerhetsgrupp innehåller flera standardsäkerhetsregler som tillåter eller nekar trafik till eller från resurser. En nätverkssäkerhetsgrupp kan associeras till ett nätverksgränssnitt, undernätet som nätverksgränssnittet finns i eller både och. För att förenkla hanteringen av säkerhetsregler rekommenderar vi att du associerar en nätverkssäkerhetsgrupp med enskilda undernät i stället för enskilda nätverksgränssnitt i undernätet när det är möjligt.
- Om olika virtuella datorer i ett undernät behöver olika säkerhetsregler som tillämpas på dem kan du associera nätverksgränssnittet i den virtuella datorn till en eller flera programsäkerhetsgrupper. En säkerhetsregel kan ange en programsäkerhetsgrupp i dess källa, mål eller båda. Den regeln gäller sedan endast för nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Läs mer om [nätverkssäkerhetsgrupper](security-overview.md) och [programsäkerhetsgrupper](security-overview.md#application-security-groups).
- Azure skapar flera standardsäkerhetsregler inom varje nätverkssäkerhetsgrupp. En standardregel gör att all trafik kan flöda mellan alla resurser i ett virtuellt nätverk. Om du vill åsidosätta det här beteendet använder du nätverkssäkerhetsgrupper, anpassad routning för att dirigera trafik till en NVA eller båda. Vi rekommenderar att du bekantar dig med alla Azures [standardsäkerhetsregler](security-overview.md#default-security-rules) och förstår hur nätverkssäkerhetsgruppsregler tillämpas på en resurs.

Du kan visa exempeldesigner för att implementera ett perimeternätverk (kallas även DMZ) mellan Azure och internet med hjälp av en [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Trafikroutning

Azure skapar flera standardvägar för utgående trafik från ett undernät. Du kan åsidosätta Azures standardroutning genom att skapa en vägtabell och associera den till ett undernät. Vanliga orsaker till att åsidosätta Azures standardroutning är:
- Eftersom du vill att trafiken mellan undernät ska flöda genom en NVA. Om du vill veta mer om hur [du konfigurerar vägtabeller för att tvinga trafik genom en NVA](tutorial-create-route-table-portal.md).
- Eftersom du vill tvinga all internetbunden trafik via en NVA, eller lokalt, via en Azure VPN-gateway. Att tvinga internettrafik i plats för inspektion och avverkning kallas ofta påtvingad tunnel. Läs mer om hur du [konfigurerar tvångstunneler](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Om du behöver implementera anpassad routning rekommenderar vi att du bekantar dig med [routning i Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Anslutning

Du kan ansluta ett virtuellt nätverk till andra virtuella nätverk med hjälp av virtuell nätverks peering eller till ditt lokala nätverk med hjälp av en Azure VPN-gateway.

### <a name="peering"></a>Peering

När du använder [virtuell nätverks peering](virtual-network-peering-overview.md)kan de virtuella nätverken vara i samma eller olika Azure-regioner som stöds. De virtuella nätverken kan finnas i samma eller olika Azure-prenumerationer (även prenumerationer som tillhör olika Azure Active Directory-klienter). Innan du skapar en peering rekommenderar vi att du bekantar dig med alla [peering-krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints). Bandbredden mellan resurser i virtuella nätverk som peer-datorer i samma region är densamma som om resurserna fanns i samma virtuella nätverk.

### <a name="vpn-gateway"></a>VPN gateway

Du kan använda en Azure [VPN-gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta ett virtuellt nätverk till ditt lokala nätverk med hjälp av en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använda en dedikerad anslutning med Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan kombinera peering och en VPN-gateway för att skapa [nav- och ekrarnätverk,](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)där eker virtuella nätverk ansluter till ett virtuellt nav och navet ansluter till ett lokalt nätverk, till exempel.

### <a name="name-resolution"></a>Namnmatchning

Resurser i ett virtuellt nätverk kan inte matcha namnen på resurser i ett peer-virtuellt nätverk med Azures [inbyggda DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Om du vill matcha namn i ett peer-virtuellt nätverk [distribuerar du din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)eller använder privata Azure DNS-domäner . [private domains](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) För att matcha namn mellan resurser i ett virtuellt nätverk och lokala nätverk måste du också distribuera din egen DNS-server.

## <a name="permissions"></a>Behörigheter

Azure använder [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) till resurser. Behörigheter tilldelas till ett [scope](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) i följande hierarki: hanteringsgrupp, prenumeration, resursgrupp och enskild resurs. Mer information om hierarkin finns i [Ordna dina resurser](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill arbeta med virtuella Azure-nätverk och alla deras relaterade funktioner, till exempel peering, nätverkssäkerhetsgrupper, tjänstslutpunkter och vägtabeller, kan du tilldela medlemmar i organisationen till de inbyggda [rollerna Ägare,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) [Deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)eller [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och sedan tilldela rollen till lämpligt scope. Om du vill tilldela specifika behörigheter för en delmängd av virtuella nätverksfunktioner skapar du en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och tilldelar de specifika behörigheter som krävs för [virtuella nätverk,](manage-virtual-network.md#permissions) [undernät och tjänstslutpunkter,](virtual-network-manage-subnet.md#permissions) [nätverksgränssnitt,](virtual-network-network-interface.md#permissions) [peering,](virtual-network-manage-peering.md#permissions) [nätverks- och programsäkerhetsgrupper](manage-network-security-group.md#permissions)eller [vägtabeller](manage-route-table.md#permissions) till rollen.

## <a name="policy"></a>Princip

Med Azure Policy kan du skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpar olika regler över dina resurser, så att resurserna förblir kompatibla med dina organisationsstandarder och servicenivåavtal. Azure Policy kör en utvärdering av dina resurser och söker efter resurser som inte är kompatibla med de principdefinitioner du har. Du kan till exempel definiera och tillämpa en princip som gör det möjligt att skapa virtuella nätverk i endast en viss resursgrupp eller region. En annan princip kan kräva att varje undernät har en nätverkssäkerhetsgrupp som är associerad med det. Principerna utvärderas sedan när resurser skapas och uppdateras.

Principer tillämpas på följande hierarki: hanteringsgrupp, prenumeration och resursgrupp. Läs mer om [Azure-principen](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller distribuera några exempel på [mallar för](policy-samples.md) virtuella nätverk.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om alla uppgifter, inställningar och alternativ för ett [virtuellt nätverk,](manage-virtual-network.md)en slutpunkt för [undernät och tjänst,](virtual-network-manage-subnet.md) [nätverksgränssnitt,](virtual-network-network-interface.md) [peering,](virtual-network-manage-peering.md) [nätverks- och programsäkerhetsgrupp](manage-network-security-group.md)eller [vägtabell](manage-route-table.md).
