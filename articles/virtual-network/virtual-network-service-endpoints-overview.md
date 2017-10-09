---
title: "Azure Virtual Network-tjänstslutpunkter | Microsoft Docs"
description: "Lär dig hur du aktiverar direktåtkomst till Azure-resurser från ett virtuellt nätverk med tjänstslutpunkter."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: e2359bc6002bd5c823467a33a4660ebccd116374
ms.contentlocale: sv-se
ms.lasthandoff: 09/26/2017

---

# <a name="virtual-network-service-endpoints-preview"></a>Virtual Network-tjänstslutpunkter (förhandsversion)

Med tjänstslutpunkter för Virtual Network (VNet) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

Den här funktionen är tillgänglig som förhandsversion för följande Azure-tjänster och regioner:

- **Azure Storage**: västra centrala USA, västra USA 2, östra USA, västra USA, östra Australien och sydöstra Australien.
- **Azure SQL Database**: västra centrala USA, västra USA 2 och östra USA.

De mest uppdaterade meddelandena om förhandsversionen finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

>[!NOTE]
> Som förhandsversion kanske funktionen inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Viktiga fördelar

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhets för dina Azure-tjänstresurser**: Med tjänstslutpunkter kan Azure-tjänstresurser skyddas i ditt virtuella nätverk. När tjänstresurser skyddas i ett virtuellt nätverk ökar säkerheten genom att den offentliga internetåtkomsten till resurserna tas bort helt, så att endast trafik från ditt virtuella nätverk tillåts.
- **Optimala vägar för Azure-tjänsttrafik från ditt virtuella nätverk**: I dag tvingar alla vägar i ditt virtuella nätverk som tvingar internettrafik till dina lokala och/eller virtuella enheter, s.k. tvingad tunneltrafik, även Azure-tjänsttrafiken att ta samma väg som internettrafiken. Med tjänstslutpunkter får du optimala vägar för Azure-trafiken. 

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta att granska och övervaka den utgående internettrafiken från dina virtuella nätverk, via tvingad tunneltrafik, utan att tjänsttrafiken påverkas. Läs mer om [användardefinierade vägar och tvingad tunneltrafik](virtual-networks-udr-overview.md).
- **Enkelt att konfigurera och lägre administrationskostnader**: du behöver inte längre reserverade, offentliga IP-adresser i ditt virtuella nätverk för att skydda Azure-resurser genom IP-brandväggen. Det behövs inga NAT- eller gatewayenheter för att konfigurera tjänstslutpunkterna. Tjänstslutpunkter konfigureras via ett enkelt klick på ett undernät. Det krävs inga ytterligare kostnader för att underhålla slutpunkterna.

## <a name="limitations"></a>Begränsningar

- Funktionen är bara tillgänglig för virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Slutpunkter aktiveras i undernät som konfigurerats i virtuella Azure-nätverk. Slutpunkter kan inte användas för trafik från ditt lokala nätverk till Azure-tjänster. Mer information finns i [Skydda Azure-tjänståtkomst från lokala nätverk](#securing-azure-services-to-virtual-networks).
- En tjänstslutpunkt gäller bara för Azure-tjänsttrafik i regionen för det virtuella nätverket. I syfte att ge stöd för RA-GRS- och GRS-trafik för Azure Storage så utökas slutpunkter även med de hopparade regioner där det virtuella nätverket är distribuerat. Lär dig mer om [parade Azure-regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).

## <a name="securing-azure-services-to-virtual-networks"></a>Skydda Azure-tjänster i virtuella nätverk

- En tjänstslutpunkt i ett virtuellt nätverk anger det virtuella nätverkets identitet för Azure-tjänsten. När tjänstslutpunkter är aktiverade i ditt virtuella nätverk kan du skydda Azure-tjänstresurserna i nätverket genom att lägga till en virtuell nätverksregel för resurserna.
- Idag används offentliga IP-adresser som IP-källadresser för Azure-tjänsttrafiken från ett virtuellt nätverk. Med tjänstslutpunkter använder tjänsttrafiken istället privata adresser i det virtuella nätverket som IP-källadresser vid åtkomst till Azure-tjänsten från det virtuella nätverket. Med det här bytet kan du komma åt tjänsterna utan att behöva reserverade, offentliga IP-adresser som används i brandväggar.
- Skydda Azure-tjänståtkomst från lokala nätverk: som standard kan Azure-tjänstresurser som skyddas i virtuella nätverk inte nås från lokala nätverk. Om du vill tillåta trafik från lokala nätverk måste du även tillåta NAT IP-adresser från dina lokala kretsar eller ExpressRoute-kretsar. NAT IP-adresser kan läggas till via IP-brandväggskonfiguration för Azure-tjänstresurser.
- ExpressRoute: Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) från ditt lokala nätverk använder varje ExpressRoute-krets två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta de här två IP-adresserna i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser [öppnar du ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen.

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Tjänstslutpunkter konfigureras i ett undernät i ett virtuellt nätverk. Slutpunkter fungerar med valfri typ av beräkningsinstans som körs inom det undernätet.
- Endast en tjänstslutpunkt kan aktiveras för en viss tjänst från ett undernät. Du kan konfigurera flera tjänstslutpunkter för alla Azure-tjänster som stöds (till exempel Azure Storage eller Azure SQL Database) i ett undernät.
- Virtuella nätverk måste finnas i samma region som Azure-tjänstresursen. Om du använder GRS- och RA-GRS-konton för Azure Storage måste det primära kontot finnas i samma region som det virtuella nätverket.
- Det virtuella nätverket där slutpunkten konfigureras kan vara i samma prenumeration som Azure-tjänstresursen eller i en annan. Mer information om vilka behörigheter som krävs för att konfigurera slutpunkter och skydda Azure-tjänster finns under [Etablering](#Provisioning).
- För tjänster som stöds kan du skydda nya eller befintliga resurser i virtuella nätverk som använder tjänstslutpunkter.

### <a name="considerations"></a>Överväganden

- När du har aktiverat en tjänstslutpunkt börjar IP-källadresserna för virtuella datorer i undernätet använda privata IPv4-adresser istället för offentliga IPv4-adresser vid kommunikation med tjänsten undernätet. Befintliga öppna TCP-anslutningar till tjänsten stängs under bytet. Kontrollera att inga kritiska uppgifter körs när du aktiverar eller inaktiverar en tjänstslutpunkt för en tjänst för ett undernät. Kontrollera även att dina program ansluter automatiskt till Azure-tjänster efter IP-adressbytet.

  IP-adressbytet påverkar bara tjänsttrafiken från ditt virtuella nätverk. Det påverkar inte hur någon annan trafik till eller från de offentliga IPv4-adresser som är tilldelade till dina virtuella datorer. För Azure-tjänster gäller att befintliga brandväggsregler där offentliga Azure-IP-adresser används slutar att fungera vid bytet till privata adresser i det virtuella nätverket.
- Med tjänstslutpunkter förblir DNS-posterna för Azure-tjänster som de är idag, och de fortsätter att matcha mot offentliga IP-adresser tilldelade till Azure-tjänsten.
- Nätverkssäkerhetsgrupper med tjänstslutpunkter:
  - Tillåter fortfarande utgående internettrafik och därför även trafik från ett virtuellt nätverk till Azure-tjänsters offentliga IP-adresser.
  - Gör det möjligt att neka trafik till offentliga IP-adresser förutom från Azure-tjänstens adresser, med hjälp av [tjänsttaggar](security-overview.md#service-tags) i dina nätverkssäkerhetsgrupper. Du kan ange godkända Azure-tjänster som mål i reglerna för nätverkssäkerhetsgruppen. Azure sköter underhållet av IP-adresserna under respektive tagg.

### <a name="scenarios"></a>Scenarier

- **Peerkopplade, anslutna eller flera virtuella nätverk**: om du vill skydda Azure-tjänster i flera undernät inom ett virtuellt nätverk eller i flera virtuella nätverk kan du aktivera tjänstslutpunkter i vart och ett av dessa undernät separat och skydda Azure-tjänstresurser i samtliga undernät.
- **Filtrering av utgående trafik från ett virtuellt nätverk till Azure-tjänster**: om du vill granska eller filtrera trafiken till en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverksinstallation inom det virtuella nätverket. Du kan sedan använda tjänstslutpunkter för undernätet där den virtuella nätverksinstallationen är distribuerad och endast skydda Azure-tjänstresurser i det här undernätet. Det här scenariot kan vara användbart om du vill begränsa Azure-tjänståtkomst från ditt virtuella nätverk till endast specifika Azure-resurser, med hjälp av filtrering i den virtuella nätverksinstallationen. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Skydda Azure-resurser för tjänster som distribuerats direkt i ett virtuellt nätverk**: olika Azure-tjänster kan distribueras direkt i specifika undernät i ett virtuellt nätverk. Du kan skydda Azure-tjänstresurser i undernät med [hanterade tjänster](virtual-network-for-azure-services.md) genom att konfigurera en tjänstslutpunkt i undernätet med hanterade tjänster.

### <a name="logging-and-troubleshooting"></a>Loggning och felsökning

När tjänstslutpunkterna har konfigurerats för en specifik tjänst verifierar du att tjänstslutpunktens routning gäller genom att: 

- Verifiera IP-källadressen för alla tjänstbegäran i tjänstdiagnostiken. Alla nya begäranden med tjänstslutpunkter visar IP-källadressen för begäran som det virtuella nätverkets privata adress, som tilldelats till klienten som gör begäran från ditt virtuella nätverk. Utan slutpunkten är denna adress en offentlig IP-adress för Azure.
- Visa effektiva vägar i alla nätverksgränssnitt i ett undernät. Vägen till tjänsten:
  - Visar en mer specifik standardväg till adressprefixområden för varje tjänst
  - Har *VirtualNetworkServiceEndpoint* som nextHopType
  - Anger att en mer direkt anslutning till tjänsten gäller, jämfört med tvingad tunneltrafik

>[!NOTE]
> Vägar för tjänstslutpunkter åsidosätter BGP- eller UDR-vägar vid matchning av adressprefix för en Azure-tjänst. Lär dig mer om [felsökning med gällande routningar](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>Etablering

Tjänstslutpunkter kan konfigureras i virtuella nätverk separat, av en användare med skrivbehörighet för det virtuella nätverket. För att kunna skydda Azure-tjänstresurser i ett virtuellt nätverk måste behörigheten *Microsoft.Network/JoinServicetoaSubnet* för undernäten läggas till för användaren. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och att tilldela specifika behörigheter till [anpassade roller](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om det virtuella nätverket och Azure-tjänstresurser finns i olika prenumerationer måste resurserna finnas under samma AD-klientorganisation (Active Directory) i förhandsversionen. 

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det tillkommer inga extra avgifter för att använda tjänstslutpunkter. Dagens aktuella prismodell för Azure-tjänster (Azure Storage, Azure SQL Database) gäller.

Det finns ingen begränsning av det totala antalet tjänstslutpunkter i ett virtuellt nätverk.

För en Azure-tjänstresurs (till exempel ett Azure Storage-konto) kan det finnas begränsningar av antalet undernät för att skydda resursen. Mer information finns i dokumentationen för de olika tjänsterna i [Nästa steg](#next-steps).

## <a name="next-steps"></a>Nästa steg

- Lär dig att [konfigurera tjänstslutpunkter i virtuellt nätverk](virtual-network-service-endpoints-configure.md)
- Lär dig att [skydda ett Azure Storage-konto i ett virtuellt nätverk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Lär dig att [skydda en Azure SQL-databas i ett virtuellt nätverk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Lär dig om [Azure-tjänstintegrering i virtuella nätverk](virtual-network-for-azure-services.md)


