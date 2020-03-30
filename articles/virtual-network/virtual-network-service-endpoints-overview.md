---
title: Tjänstslutpunkter för virtuellt nätverk i Azure
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar direktåtkomst till Azure-resurser från ett virtuellt nätverk med tjänstslutpunkter.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244789"
---
# <a name="virtual-network-service-endpoints"></a>Tjänstslutpunkter för virtuellt nätverk

Slutpunkter för virtuella nätverk (Virtual Network) utökar det privata adressutrymmet för det virtuella nätverket. Slutpunkterna utökar också identiteten på ditt virtuella nätverk till Azure-tjänsterna via en direkt anslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

Den här funktionen är tillgänglig för följande Azure-tjänster och regioner. *Microsoft.-resursen\* * är inom parentes. Aktivera den här resursen från undernätssidan när du konfigurerar tjänstslutpunkter för tjänsten:

**Allmänt tillgänglig**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allmänt tillgänglig i Azure-regioner där databastjänsten är tillgänglig.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allmänt tillgänglig i Azure-regioner där databastjänsten är tillgänglig.
- **[Azure Database för MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Allmänt tillgängligt i Azure-regioner där databastjänsten är tillgänglig.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Allmänt tillgängligt i alla Azure-regioner.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Allmänt tillgängligt i alla Azure-regioner där ADLS Gen1 är tillgängligt.
- **[Azure App Service:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** Allmänt tillgängligt i alla Azure-regioner där App-tjänsten är tillgänglig.

**Offentlig förhandsversion**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Förhandsversion tillgänglig i alla Azure-regioner där Azure Container Registry är tillgängligt.

De mest uppdaterade meddelandena finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Viktiga fördelar

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhet för dina Azure-tjänstresurser**: Virtuella nätverk privata adressutrymmen kan överlappa varandra. Du kan inte använda överlappande blanksteg för att unikt identifiera trafik som kommer från ditt virtuella nätverk. Tjänstslutpunkter ger möjlighet att skydda Azure-tjänstresurser till ditt virtuella nätverk genom att utöka VNet-identitet till tjänsten. När du har aktiverat tjänstslutpunkter i det virtuella nätverket kan du lägga till en virtuell nätverksregel för att skydda Azure-tjänstresurserna i det virtuella nätverket. Regeltillägget ger förbättrad säkerhet genom att helt ta bort offentlig internetåtkomst till resurser och endast tillåta trafik från det virtuella nätverket.
- **Optimal routning för Azure-tjänsttrafik från ditt virtuella nätverk:** Idag tvingar alla vägar i ditt virtuella nätverk som tvingar internettrafik till dina lokala och/eller virtuella enheter också Azure-tjänsttrafik att ta samma väg som internettrafiken. Med tjänstslutpunkter får du optimal routning för Azure-trafiken. 

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta att granska och övervaka den utgående internettrafiken från dina virtuella nätverk, via tvingad tunneltrafik, utan att tjänsttrafiken påverkas. Mer information om användardefinierade vägar och routning med tvingande tunnlar finns i [Azures routning för virtuell nätverkstrafik](virtual-networks-udr-overview.md).
- **Enkelt att konfigurera och lägre administrationskostnader**: du behöver inte längre reserverade, offentliga IP-adresser i ditt virtuella nätverk för att skydda Azure-resurser genom IP-brandväggen. Nat -enheter (Network Address Translation) eller gatewayenheter krävs för att konfigurera tjänstslutpunkterna. Du kan konfigurera tjänstslutpunkter genom ett enkelt klick på ett undernät. Det finns inga ytterligare omkostnader för att underhålla slutpunkterna.

## <a name="limitations"></a>Begränsningar

- Funktionen är bara tillgänglig för virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Slutpunkter aktiveras i undernät som konfigurerats i virtuella Azure-nätverk. Slutpunkter kan inte användas för trafik från dina lokaler till Azure-tjänster. Mer information finns i [Åtkomst till säker Azure-tjänst från lokala](#secure-azure-services-to-virtual-networks)
- För Azure SQL gäller en tjänstslutpunkt bara för Azure-tjänsttrafik i regionen för det virtuella nätverket. För Azure Storage omfattar slutpunkter även parkopplade regioner där du distribuerar det virtuella nätverket för att stödja rade-Access Geo-Redundant Storage (RA-GRS) och GRS-trafik (Geo-Redundant Storage). Mer information finns i [Azure-parade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- För ADLS-gen (Azure Data Lake Storage) Gen 1 är VNet-integreringsfunktionen endast tillgänglig för virtuella nätverk inom samma region. Observera också att integrering av virtuella nätverk för ADLS Gen1 använder slutpunktssäkerheten för den virtuella nätverkstjänsten mellan det virtuella nätverket och Azure Active Directory (Azure AD) för att generera ytterligare säkerhetsanspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst. *Microsoft.AzureActiveDirectory-taggen* som visas under tjänster som stöder tjänstslutpunkter används endast för att stödja tjänstslutpunkter till ADLS Gen 1. Azure AD stöder inte tjänstslutpunkter internt. Mer information om Azure Data Lake Store Gen 1 VNet-integrering finns [i Nätverkssäkerhet i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Säkra Azure-tjänster till virtuella nätverk

- En tjänstslutpunkt för virtuellt nätverk anger det virtuella nätverkets identitet för Azure-tjänsten. När du har aktiverat tjänstslutpunkter i det virtuella nätverket kan du lägga till en virtuell nätverksregel för att skydda Azure-tjänstresurserna i det virtuella nätverket.
- Idag används offentliga IP-adresser som IP-källadresser för Azure-tjänsttrafiken från ett virtuellt nätverk. Med tjänstslutpunkter använder tjänsttrafiken istället privata adresser i det virtuella nätverket som IP-källadresser vid åtkomst till Azure-tjänsten från det virtuella nätverket. Med det här bytet kan du komma åt tjänsterna utan att behöva reserverade, offentliga IP-adresser som används i brandväggar.

   >[!NOTE]
   > Med tjänstslutpunkter växlar källans IP-adresser för virtuella datorer i undernätet för tjänsttrafik från att använda offentliga IPv4-adresser till att istället använda privata. Befintliga brandväggsregler för Azure-tjänsten med hjälp av offentliga IP-adresser i Azure slutar att fungera via den här ändringen. Kontrollera att brandväggsreglerna för Azure-tjänsten tillåter den här växeln innan du konfigurerar tjänstslutpunkter. Det kan också uppstå tillfälligt avbrott för tjänsttrafik från det här undernätet när du konfigurerar tjänstslutpunkter. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Säker Azure-tjänståtkomst från lokalt

  Som standard kan Azure-tjänstresurser som är skyddade till virtuella nätverk inte nås från lokala nätverk. Om du vill tillåta trafik från lokala, måste du också tillåta offentliga (vanligtvis NAT) IP-adresser från din lokala eller ExpressRoute. Du kan lägga till dessa IP-adresser via IP-brandväggskonfigurationen för Azure-tjänstresurser.

  ExpressRoute: Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för offentlig peering eller Microsoft-peering från dina lokaler måste du identifiera NAT-IP-adresser som du använder. För offentlig peering använder varje ExpressRoute-krets två NAT-IP-adresser som standard tillämpas på Azure-tjänsttrafik när trafiken kommer in i Microsoft Azure-nätverkets ryggrad. För Microsoft-peering tillhandahålls NAT-IP-adresserna antingen kunden eller tillhandahålls av tjänsteleverantören.Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning.För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Mer information om NAT för ExpressRoute public och Microsoft peering finns i [ExpressRoute NAT-krav](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Konfigurera tjänstslutpunkter i ett undernät i ett virtuellt nätverk. Slutpunkter fungerar med valfri typ av beräkningsinstans som körs inom det undernätet.
- Du kan konfigurera flera tjänstslutpunkter för alla Azure-tjänster som stöds (Azure Storage eller Azure SQL Database, till exempel) i ett undernät.
- För Azure SQL Database måste virtuella nätverk finnas i samma region som Azure-tjänstresursen. Om du använder GRS- och RA-GRS-konton för Azure Storage måste det primära kontot finnas i samma region som det virtuella nätverket. För alla andra tjänster kan du skydda Azure-tjänstresurser till virtuella nätverk i alla regioner. 
- Det virtuella nätverket där slutpunkten konfigureras kan vara i samma prenumeration som Azure-tjänstresursen eller i en annan. Mer information om vilka behörigheter som krävs för att konfigurera slutpunkter och skydda Azure-tjänster finns under [Etablering](#provisioning).
- För tjänster som stöds kan du skydda nya eller befintliga resurser i virtuella nätverk som använder tjänstslutpunkter.

### <a name="considerations"></a>Överväganden

- När du har aktiverat en tjänstslutpunkt, källan IP-adresser för virtuella datorer i undernätsväxeln. Käll-IP-adresserna växlar från att använda offentliga IPv4-adresser till att använda sin privata IPv4-adress när du kommunicerar med tjänsten från det undernätet. Befintliga öppna TCP-anslutningar till tjänsten stängs under bytet. Kontrollera att inga kritiska uppgifter körs när du aktiverar eller inaktiverar en tjänstslutpunkt för en tjänst för ett undernät. Kontrollera även att dina program ansluter automatiskt till Azure-tjänster efter IP-adressbytet.

  IP-adressbytet påverkar bara tjänsttrafiken från ditt virtuella nätverk. Det finns ingen påverkan på någon annan trafik som är adresserad till eller från de offentliga IPv4-adresser som tilldelats dina virtuella datorer. För Azure-tjänster gäller att befintliga brandväggsregler där offentliga Azure-IP-adresser används slutar att fungera vid bytet till privata adresser i det virtuella nätverket.
- Med tjänstslutpunkter förblir DNS-poster för Azure-tjänster som är idag och fortsätter att matcha till offentliga IP-adresser som tilldelats Azure-tjänsten.

- Nätverkssäkerhetsgrupper (NSG) med tjänstslutpunkter:
  - Som standard tillåter NSG-grupper utgående internettrafik och tillåter även trafik från ditt virtuella nätverk till Azure-tjänster. Den här trafiken fortsätter att fungera med tjänstslutpunkter som den är. 
  - Om du vill neka all utgående internettrafik och endast tillåta trafik till specifika Azure-tjänster kan du göra det med hjälp av [tjänsttaggar](security-overview.md#service-tags) i dina NSG:er. Du kan ange Azure-tjänster som stöds som mål i dina NSG-regler och Azure tillhandahåller också underhåll av IP-adresser som ligger till grund för varje tagg. Mer information finns i [Azure-tjänsttaggar för NSG:er](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenarier

- **Peerkopplade, anslutna eller flera virtuella nätverk**: om du vill skydda Azure-tjänster i flera undernät inom ett virtuellt nätverk eller i flera virtuella nätverk kan du aktivera tjänstslutpunkter i vart och ett av dessa undernät separat och skydda Azure-tjänstresurser i samtliga undernät.
- **Filtrera utgående trafik från ett virtuellt nätverk till Azure-tjänster:** Om du vill inspektera eller filtrera trafiken som skickas till en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverksinstallation i det virtuella nätverket. Du kan sedan använda tjänstslutpunkter för undernätet där den virtuella nätverksinstallationen är distribuerad och endast skydda Azure-tjänstresurser i det här undernätet. Det här scenariot kan vara användbart om du vill använda filtrering av virtuella nätverksinstallationer för att begränsa Azure-tjänståtkomst från ditt virtuella nätverk endast till specifika Azure-resurser. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Skydda Azure-resurser till tjänster som distribueras direkt till virtuella nätverk**: Du kan distribuera olika Azure-tjänster direkt till specifika undernät i ett virtuellt nätverk. Du kan skydda Azure-tjänstresurser i undernät med [hanterade tjänster](virtual-network-for-azure-services.md) genom att konfigurera en tjänstslutpunkt i undernätet med hanterade tjänster.
- **Disktrafik från en virtuell Azure-dator:** Virtuell datordisktrafik för hanterade och ohanterade diskar påverkas inte av tjänstslutpunkter routningsändringar för Azure Storage. Den här trafiken inkluderar diskIO samt montera och avmontera. Du kan begränsa REST-åtkomst till sidblobar för att välja nätverk via tjänstslutpunkter och [Azure Storage-nätverksregler](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Loggning och felsökning

När du har konfigurerat tjänstslutpunkter till en viss tjänst verifierar du att tjänstens slutpunktsväg gäller genom att: 
 
- Verifiera IP-källadressen för alla tjänstbegäran i tjänstdiagnostiken. Alla nya begäranden med tjänstslutpunkter visar IP-källadressen för begäran som det virtuella nätverkets privata adress, som tilldelats till klienten som gör begäran från ditt virtuella nätverk. Utan slutpunkten är denna adress en offentlig IP-adress för Azure.
- Visa effektiva vägar i alla nätverksgränssnitt i ett undernät. Vägen till tjänsten:
  - Visar en mer specifik standardväg till adressprefixområden för varje tjänst
  - Har *VirtualNetworkServiceEndpoint* som nextHopType
  - Anger att en mer direkt anslutning till tjänsten är i kraft jämfört med alla rutter med påtvingad tunnel

>[!NOTE]
> Vägar för tjänstslutpunkter åsidosätter BGP- eller UDR-vägar vid matchning av adressprefix för en Azure-tjänst. Mer information finns i [felsökning med effektiva vägar](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Etablering

Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till ett virtuellt nätverk. För att skydda Azure-tjänstresurser till ett virtuellt nätverk måste användaren ha behörighet till *Microsoft.Network/virtualNetworks/undernät/joinViaServiceEndpoint/åtgärd* för de tillagda undernäten. De inbyggda tjänstadministratörsrollerna innehåller den här behörigheten som standard. Du kan ändra behörigheten genom att skapa anpassade roller.

Mer information om inbyggda roller finns [i Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Mer information om hur du tilldelar specifika behörigheter till anpassade roller finns i [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om det virtuella nätverket och Azure-tjänstresurser finns i olika prenumerationer måste resurserna finnas under samma AD-klientorganisation (Active Directory). 

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det finns ingen extra kostnad för att använda tjänstslutpunkter. Den aktuella prismodellen för Azure-tjänster (Azure Storage, Azure SQL Database, etc.) gäller som den är idag.

Det finns ingen gräns för det totala antalet tjänstslutpunkter i ett virtuellt nätverk.

Vissa Azure-tjänster, till exempel Azure Storage-konton, kan framtvinga gränser för antalet undernät som används för att skydda resursen. Mer information finns i dokumentationen för olika tjänster i avsnittet [Nästa steg.](#next-steps)

## <a name="vnet-service-endpoint-policies"></a>Principer för VNet-tjänstslutpunkt 

Med slutpunktsprinciper för VNet-tjänsten kan du filtrera virtuell nätverkstrafik till Azure-tjänster. Det här filtret tillåter endast specifika Azure-tjänstresurser över tjänstslutpunkter. Principer för tjänstslutpunkt ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster. Mer information finns i [slutpunktsprinciper för virtual network service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Vanliga frågor och svar

Vanliga frågor och andra frågor finns i [vanliga frågor om slutpunkt för virtual network service](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera slutpunkter för virtuella nätverkstjänster](tutorial-restrict-network-access-to-resources.md)
- [Skydda ett Azure Storage-konto till ett virtuellt nätverk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skydda en Azure SQL-databas till ett virtuellt nätverk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skydda ett Azure SQL Data Warehouse till ett virtuellt nätverk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-tjänstintegrering i virtuella nätverk](virtual-network-for-azure-services.md)
- [Slutpunktsprinciper för virtuella nätverkstjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

