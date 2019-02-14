---
title: Tjänstslutpunkter för virtuella Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar direktåtkomst till Azure-resurser från ett virtuellt nätverk med tjänstslutpunkter.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: sumeet.mittal
ms.custom: ''
ms.openlocfilehash: 60296400c40a1e79758dc53d60d2f1c61d79fd88
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108202"
---
# <a name="virtual-network-service-endpoints"></a>Slutpunkter för virtuellt nätverk

Med tjänstslutpunkter för Virtual Network (VNet) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

Den här funktionen är tillgänglig för följande Azure-tjänster och regioner:

**Allmänt tillgänglig**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)**: Allmänt tillgängligt i alla Azure-regioner.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla Azure-regioner.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Database for PostgreSQL-server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Offentligt tillgänglig i Azure-regioner där databastjänsten är tillgänglig.
- **[Azure Database for MySQL-server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Offentligt tillgänglig i Azure-regioner där databastjänsten är tillgänglig.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla offentliga Azure-molnregioner.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)**: Allmänt tillgängligt i alla offentliga Azure-molnregioner.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla offentliga Azure-molnregioner.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla offentliga Azure-molnregioner.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Allmänt tillgängligt i alla Azure-regioner där ADLS Gen1 är tillgängligt.

De mest uppdaterade meddelandena finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Viktiga fördelar

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhet för dina Azure-tjänstresurser**: Privat adressutrymme för virtuella nätverk kan överlappa och kan därför inte användas för att unikt identifiera trafik från ditt virtuella nätverk. Tjänstslutpunkter ger dig möjligheten att skydda Azure-tjänstresurser i ditt virtuella nätverk genom att utöka identitet för virtuellt nätverk till tjänsten. När tjänstslutpunkter är aktiverade i ditt virtuella nätverk kan du skydda Azure-tjänstresurserna i nätverket genom att lägga till en virtuell nätverksregel för resurserna. Detta ger bättre säkerhet genom att helt ta bort åtkomst till offentlig Internetåtkomst för resurser och endast tillåta nätverk från ditt virtuella nätverk.
- **Optimal routning för Azure-tjänsttrafik från ditt virtuella nätverk**: I dag tvingar alla routningar på ditt virtuella nätverk som tvingar internettrafik till dina lokala och/eller virtuella enheter, s.k. tvingad tunneltrafik, även Azure-tjänsttrafiken att ta samma väg som internettrafiken. Med tjänstslutpunkter får du optimal routning för Azure-trafiken. 

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta att granska och övervaka den utgående internettrafiken från dina virtuella nätverk, via tvingad tunneltrafik, utan att tjänsttrafiken påverkas. Läs mer om [användardefinierade vägar och tvingad tunneltrafik](virtual-networks-udr-overview.md).
- **Enkelt att konfigurera med mindre hanteringskostnader**: Du behöver inte längre reserverade, offentliga IP-adresser i dina virtuella nätverk för att skydda Azure-resurser med IP-brandvägg. Det behövs inga NAT- eller gatewayenheter för att konfigurera tjänstslutpunkterna. Tjänstslutpunkter konfigureras via ett enkelt klick på ett undernät. Det krävs inga ytterligare kostnader för att underhålla slutpunkterna.

## <a name="limitations"></a>Begränsningar

- Funktionen är bara tillgänglig för virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Slutpunkter aktiveras i undernät som konfigurerats i virtuella Azure-nätverk. Slutpunkter kan inte användas för trafik från ditt lokala nätverk till Azure-tjänster. Mer information finns i [Skydda Azure-tjänståtkomst från lokala nätverk](#securing-azure-services-to-virtual-networks).
- För Azure SQL gäller en tjänstslutpunkt bara för Azure-tjänsttrafik i regionen för det virtuella nätverket. I syfte att ge stöd för RA-GRS- och GRS-trafik för Azure Storage så utökas slutpunkter även med de hopparade regioner där det virtuella nätverket är distribuerat. Lär dig mer om [parade Azure-regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- För ADLS generation 1 finns VNet-integreringsfunktion endast tillgänglig för virtuella nätverk i samma region.

## <a name="securing-azure-services-to-virtual-networks"></a>Skydda Azure-tjänster i virtuella nätverk

- En tjänstslutpunkt i ett virtuellt nätverk anger det virtuella nätverkets identitet för Azure-tjänsten. När tjänstslutpunkter är aktiverade i ditt virtuella nätverk kan du skydda Azure-tjänstresurserna i nätverket genom att lägga till en virtuell nätverksregel för resurserna.
- Idag används offentliga IP-adresser som IP-källadresser för Azure-tjänsttrafiken från ett virtuellt nätverk. Med tjänstslutpunkter använder tjänsttrafiken istället privata adresser i det virtuella nätverket som IP-källadresser vid åtkomst till Azure-tjänsten från det virtuella nätverket. Med det här bytet kan du komma åt tjänsterna utan att behöva reserverade, offentliga IP-adresser som används i brandväggar.

>[!NOTE]
> Med tjänstslutpunkter växlar källans IP-adresser för virtuella datorer i undernätet för tjänsttrafik från att använda offentliga IPv4-adresser till att istället använda privata. Befintliga brandväggsregler för Azure-tjänsten med hjälp av offentliga IP-adresser i Azure slutar att fungera via den här ändringen. Kontrollera att brandväggsreglerna för Azure-tjänsten tillåter den här växeln innan du konfigurerar tjänstslutpunkter. Det kan också uppstå tillfälligt avbrott för tjänsttrafik från det här undernätet när du konfigurerar tjänstslutpunkter. 
 
- __Skydda Azure-tjänståtkomst från lokalt nätverk__:

  Som standard kan Azure-tjänstresurser som skyddas på virtuella nätverk inte nås från lokala nätverk. Om du vill tillåta trafik från lokala nätverk måste du även tillåta offentliga IP-adresser (normalt NAT) från dina lokala kretsar eller ExpressRoute. Dessa IP-adresser kan läggas till via IP-brandväggskonfiguration för Azure-tjänstresurser.

  ExpressRoute: Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är de NAT IP-adresser som används antingen tillhandahållna av kunden eller av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Tjänstslutpunkter konfigureras i ett undernät i ett virtuellt nätverk. Slutpunkter fungerar med valfri typ av beräkningsinstans som körs inom det undernätet.
- Du kan konfigurera flera tjänstslutpunkter för alla Azure-tjänster som stöds (till exempel Azure Storage eller Azure SQL Database) i ett undernät.
- För Azure SQL Database måste virtuella nätverk finnas i samma region som Azure-tjänstresursen. Om du använder GRS- och RA-GRS-konton för Azure Storage måste det primära kontot finnas i samma region som det virtuella nätverket. För alla andra tjänster kan Azure-serviceresurser säkras mot virtuella nätverk i en region. 
- Det virtuella nätverket där slutpunkten konfigureras kan vara i samma prenumeration som Azure-tjänstresursen eller i en annan. Mer information om vilka behörigheter som krävs för att konfigurera slutpunkter och skydda Azure-tjänster finns under [Etablering](#Provisioning).
- För tjänster som stöds kan du skydda nya eller befintliga resurser i virtuella nätverk som använder tjänstslutpunkter.

### <a name="considerations"></a>Överväganden

- När du har aktiverat en tjänstslutpunkt börjar IP-källadresserna för virtuella datorer i undernätet använda privata IPv4-adresser istället för offentliga IPv4-adresser vid kommunikation med tjänsten undernätet. Befintliga öppna TCP-anslutningar till tjänsten stängs under bytet. Kontrollera att inga kritiska uppgifter körs när du aktiverar eller inaktiverar en tjänstslutpunkt för en tjänst för ett undernät. Kontrollera även att dina program ansluter automatiskt till Azure-tjänster efter IP-adressbytet.

  IP-adressbytet påverkar bara tjänsttrafiken från ditt virtuella nätverk. Det påverkar inte hur någon annan trafik till eller från de offentliga IPv4-adresser som är tilldelade till dina virtuella datorer. För Azure-tjänster gäller att befintliga brandväggsregler där offentliga Azure-IP-adresser används slutar att fungera vid bytet till privata adresser i det virtuella nätverket.
- Med tjänstslutpunkter förblir DNS-posterna för Azure-tjänster som de är idag, och de fortsätter att matcha mot offentliga IP-adresser tilldelade till Azure-tjänsten.

- Nätverkssäkerhetsgrupper (NSG) med tjänstslutpunkter:
  - Som standard tillåter NSG:er utgående internettrafik och därmed även trafik från ditt VNet till Azure-tjänster. Detta fortsätter att fungera på samma sätt, med tjänstslutpunkter. 
  - Om du vill neka all utgående internettrafik och endast tillåta trafik till specifika Azure-tjänster kan du använda [tjänsttaggar](security-overview.md#service-tags) i dina NSG:er. Du kan ange Azure-tjänster som stöds som mål i NSG-reglerna och underhållet av underliggande IP-adresser för varje tagg anges av Azure. Mer information finns i [Azure-tjänsttaggar för NSG:er](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenarier

- **Peer-kopplade, anslutna eller flera virtuella nätverk**: Om du vill skydda Azure-tjänster i flera undernät inom ett virtuellt nätverk eller i flera virtuella nätverk kan du aktivera tjänstslutpunkter i vart och ett av dessa undernät separat och skydda Azure-tjänstresurser i samtliga undernät.
- **Filtrering av utgående trafik från ett virtuellt nätverk till Azure-tjänster**: Om du vill granska eller filtrera trafiken mot en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverksinstallation inom det virtuella nätverket. Du kan sedan använda tjänstslutpunkter för undernätet där den virtuella nätverksinstallationen är distribuerad och endast skydda Azure-tjänstresurser i det här undernätet. Det här scenariot kan vara användbart om du vill begränsa Azure-tjänståtkomst från ditt virtuella nätverk till endast specifika Azure-resurser, med hjälp av filtrering i den virtuella nätverksinstallationen. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Skydda Azure-resurser i tjänster distribuerade direkt på virtuella nätverk**: Olika Azure-tjänster kan distribueras direkt till specifika undernät på ett virtuellt nätverk. Du kan skydda Azure-tjänstresurser i undernät med [hanterade tjänster](virtual-network-for-azure-services.md) genom att konfigurera en tjänstslutpunkt i undernätet med hanterade tjänster.
- **Disktrafik från en virtuell Azure-dator**: Virtuell datordisktrafik (inklusive montering, demontering, diskIO), för hanterade/ohanterade diskar, påverkas inte av tjänstslutpunkternas ändrade routning för Azure Storage. Du kan begränsa REST-åtkomst till sidblobar för att välja nätverk via slutpunkter och [Azure Storage-nätverksregler](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Loggning och felsökning

När tjänstslutpunkterna har konfigurerats för en specifik tjänst verifierar du att tjänstslutpunktens routning gäller genom att: 
 
- Verifiera IP-källadressen för alla tjänstbegäran i tjänstdiagnostiken. Alla nya begäranden med tjänstslutpunkter visar IP-källadressen för begäran som det virtuella nätverkets privata adress, som tilldelats till klienten som gör begäran från ditt virtuella nätverk. Utan slutpunkten är denna adress en offentlig IP-adress för Azure.
- Visa effektiva vägar i alla nätverksgränssnitt i ett undernät. Vägen till tjänsten:
  - Visar en mer specifik standardväg till adressprefixområden för varje tjänst
  - Har *VirtualNetworkServiceEndpoint* som nextHopType
  - Anger att en mer direkt anslutning till tjänsten gäller, jämfört med tvingad tunneltrafik

>[!NOTE]
> Vägar för tjänstslutpunkter åsidosätter BGP- eller UDR-vägar vid matchning av adressprefix för en Azure-tjänst. Lär dig mer om [felsökning med gällande routningar](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Etablering

Tjänstslutpunkter kan konfigureras i virtuella nätverk separat, av en användare med skrivbehörighet för det virtuella nätverket. För att kunna skydda Azure-tjänstresurser i ett virtuellt nätverk måste behörigheten *Microsoft.Network/JoinServicetoaSubnet* för undernäten läggas till för användaren. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om det virtuella nätverket och Azure-tjänstresurser finns i olika prenumerationer måste resurserna finnas under samma AD-klientorganisation (Active Directory). 

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det tillkommer inga extra avgifter för att använda tjänstslutpunkter. Dagens aktuella prismodell för Azure-tjänster (Azure Storage, Azure SQL Database med mera) gäller.

Det finns ingen begränsning av det totala antalet tjänstslutpunkter i ett virtuellt nätverk.

För en Azure-tjänstresurs (till exempel ett Azure Storage-konto) kan det finnas begränsningar av antalet undernät för att skydda resursen. Mer information finns i dokumentationen för de olika tjänsterna i [Nästa steg](#next-steps).

## <a name="virtual-network-service-endpoint-policies"></a>Principer för tjänstslutpunkter för virtuella nätverk 

Med principer för tjänstslutpunkter för virtuella nätverk kan du filtrera trafik i virtuella nätverk till Azure-tjänster för att endast tillåta specifika Azure-tjänstresurser över tjänstslutpunkter. Principer för tjänstslutpunkt ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster. Mer information: [Principer för tjänstslutpunkter för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Vanliga frågor och svar

Vanliga frågor och svar finns i [vanliga frågor och svar om tjänstslutpunkt för virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Nästa steg

- Lär dig att [konfigurera tjänstslutpunkter i virtuellt nätverk](tutorial-restrict-network-access-to-resources.md)
- Lär dig att [skydda ett Azure Storage-konto i ett virtuellt nätverk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Lär dig att [skydda en Azure SQL-databas i ett virtuellt nätverk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Lär dig att [skydda ett Azure SQL Data Warehouse i ett virtuellt nätverk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- Lär dig om [Azure-tjänstintegrering i virtuella nätverk](virtual-network-for-azure-services.md)
- Lär dig om: [Principer för tjänstslutpunkter för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Snabbstart: [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) för att konfigurera tjänstslutpunkt på ett VNet:s undernät och säkra ett Azure Storage-konto till det undernätet.

