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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378696"
---
# <a name="virtual-network-service-endpoints"></a>Tjänstslutpunkter för virtuellt nätverk

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**Allmänt tillgänglig**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**Offentlig förhandsversion**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

De mest uppdaterade meddelandena finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Viktiga fördelar

Tjänstslutpunkter har följande fördelar:

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Med tjänstslutpunkter får du optimal routning för Azure-trafiken. 

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta att granska och övervaka den utgående internettrafiken från dina virtuella nätverk, via tvingad tunneltrafik, utan att tjänsttrafiken påverkas. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Enkelt att konfigurera och lägre administrationskostnader**: du behöver inte längre reserverade, offentliga IP-adresser i ditt virtuella nätverk för att skydda Azure-resurser genom IP-brandväggen. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>Begränsningar

- Funktionen är bara tillgänglig för virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Slutpunkter aktiveras i undernät som konfigurerats i virtuella Azure-nätverk. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- För Azure SQL gäller en tjänstslutpunkt bara för Azure-tjänsttrafik i regionen för det virtuella nätverket. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. For more information, see [Azure paired regions](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- En tjänstslutpunkt för virtuellt nätverk anger det virtuella nätverkets identitet för Azure-tjänsten. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- Idag används offentliga IP-adresser som IP-källadresser för Azure-tjänsttrafiken från ett virtuellt nätverk. Med tjänstslutpunkter använder tjänsttrafiken istället privata adresser i det virtuella nätverket som IP-källadresser vid åtkomst till Azure-tjänsten från det virtuella nätverket. Med det här bytet kan du komma åt tjänsterna utan att behöva reserverade, offentliga IP-adresser som används i brandväggar.

   >[!NOTE]
   > Med tjänstslutpunkter växlar källans IP-adresser för virtuella datorer i undernätet för tjänsttrafik från att använda offentliga IPv4-adresser till att istället använda privata. Befintliga brandväggsregler för Azure-tjänsten med hjälp av offentliga IP-adresser i Azure slutar att fungera via den här ändringen. Kontrollera att brandväggsreglerna för Azure-tjänsten tillåter den här växeln innan du konfigurerar tjänstslutpunkter. Det kan också uppstå tillfälligt avbrott för tjänsttrafik från det här undernätet när du konfigurerar tjänstslutpunkter. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. If you want to allow traffic from on-premises, you must also allow public (typically, NAT) IP addresses from your on-premises or ExpressRoute. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Configure service endpoints on a subnet in a virtual network. Slutpunkter fungerar med valfri typ av beräkningsinstans som körs inom det undernätet.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- För Azure SQL Database måste virtuella nätverk finnas i samma region som Azure-tjänstresursen. Om du använder GRS- och RA-GRS-konton för Azure Storage måste det primära kontot finnas i samma region som det virtuella nätverket. For all other services, you can secure Azure service resources to virtual networks in any region. 
- Det virtuella nätverket där slutpunkten konfigureras kan vara i samma prenumeration som Azure-tjänstresursen eller i en annan. Mer information om vilka behörigheter som krävs för att konfigurera slutpunkter och skydda Azure-tjänster finns under [Etablering](#provisioning).
- För tjänster som stöds kan du skydda nya eller befintliga resurser i virtuella nätverk som använder tjänstslutpunkter.

### <a name="considerations"></a>Överväganden

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. Befintliga öppna TCP-anslutningar till tjänsten stängs under bytet. Kontrollera att inga kritiska uppgifter körs när du aktiverar eller inaktiverar en tjänstslutpunkt för en tjänst för ett undernät. Kontrollera även att dina program ansluter automatiskt till Azure-tjänster efter IP-adressbytet.

  IP-adressbytet påverkar bara tjänsttrafiken från ditt virtuella nätverk. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. För Azure-tjänster gäller att befintliga brandväggsregler där offentliga Azure-IP-adresser används slutar att fungera vid bytet till privata adresser i det virtuella nätverket.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Nätverkssäkerhetsgrupper (NSG) med tjänstslutpunkter:
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. Mer information finns i [Azure-tjänsttaggar för NSG:er](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenarier

- **Peerkopplade, anslutna eller flera virtuella nätverk**: om du vill skydda Azure-tjänster i flera undernät inom ett virtuellt nätverk eller i flera virtuella nätverk kan du aktivera tjänstslutpunkter i vart och ett av dessa undernät separat och skydda Azure-tjänstresurser i samtliga undernät.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. Du kan sedan använda tjänstslutpunkter för undernätet där den virtuella nätverksinstallationen är distribuerad och endast skydda Azure-tjänstresurser i det här undernätet. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. Du kan skydda Azure-tjänstresurser i undernät med [hanterade tjänster](virtual-network-for-azure-services.md) genom att konfigurera en tjänstslutpunkt i undernätet med hanterade tjänster.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Loggning och felsökning

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- Verifiera IP-källadressen för alla tjänstbegäran i tjänstdiagnostiken. Alla nya begäranden med tjänstslutpunkter visar IP-källadressen för begäran som det virtuella nätverkets privata adress, som tilldelats till klienten som gör begäran från ditt virtuella nätverk. Utan slutpunkten är denna adress en offentlig IP-adress för Azure.
- Visa effektiva vägar i alla nätverksgränssnitt i ett undernät. Vägen till tjänsten:
  - Visar en mer specifik standardväg till adressprefixområden för varje tjänst
  - Har *VirtualNetworkServiceEndpoint* som nextHopType
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> Vägar för tjänstslutpunkter åsidosätter BGP- eller UDR-vägar vid matchning av adressprefix för en Azure-tjänst. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Etablering

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om det virtuella nätverket och Azure-tjänstresurser finns i olika prenumerationer måste resurserna finnas under samma AD-klientorganisation (Active Directory). 

## <a name="pricing-and-limits"></a>Priser och begränsningar

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. Principer för tjänstslutpunkt ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Vanliga frågor och svar

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Nästa steg

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Principer för tjänstslutpunkter för virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

