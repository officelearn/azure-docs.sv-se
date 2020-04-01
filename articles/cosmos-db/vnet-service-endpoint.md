---
title: Säker åtkomst till Azure Cosmos DB-konton med hjälp av slutpunkten för virtuella nätverkstjänsten
description: Det här dokumentet beskriver om virtuellt nätverk och åtkomstkontroll för undernät för ett Azure Cosmos-konto.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c1c5bdd1d210a1933699cad52dbf123b50048e01
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421318"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Åtkomst till Azure Cosmos DB från virtuella nätverk (VNet)

Du kan konfigurera Azure Cosmos-kontot så att åtkomst endast tillåts från ett specifikt undernät i virtuellt nätverk (VNet). Genom att aktivera [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för åtkomst till Azure Cosmos DB i undernätet i ett virtuellt nätverk skickas trafiken från det undernätet till Azure Cosmos DB med identiteten för undernätet och det virtuella nätverket. När slutpunkten för Azure Cosmos DB-tjänsten är aktiverad kan du begränsa åtkomsten till undernätet genom att lägga till den i ditt Azure Cosmos-konto.

Som standard är ett Azure Cosmos-konto tillgängligt från valfri källa om begäran åtföljs av en giltig auktoriseringstoken. När du lägger till ett eller flera undernät i virtuella nätverk får endast begäranden som kommer från dessa undernät ett giltigt svar. Begäranden från någon annan källa får ett 403 -svar (förbjudet). 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Här är några vanliga frågor om hur du konfigurerar åtkomst från virtuella nätverk:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan jag ange både slutpunkt för virtuella nätverkstjänster och IP-åtkomstkontrollprincip på ett Azure Cosmos-konto? 

Du kan aktivera både slutpunkten för den virtuella nätverkstjänsten och en IP-åtkomstkontrollprincip (aka-brandvägg) på ditt Azure Cosmos-konto. Dessa två funktioner kompletterar varandra och säkerställer isolering och säkerhet för ditt Azure Cosmos-konto. Med hjälp av IP-brandväggen säkerställer du att statiska IP-adresser kan komma åt ditt konto. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hur begränsar jag åtkomsten till undernätet i ett virtuellt nätverk? 

Det finns två steg som krävs för att begränsa åtkomsten till Azure Cosmos-kontot från ett undernät. Först tillåter du trafik från undernätet att bära dess undernät och virtuella nätverksidentitet till Azure Cosmos DB. Detta görs genom att aktivera tjänstslutpunkt för Azure Cosmos DB i undernätet. Nästa är att lägga till en regel i Azure Cosmos-kontot som anger det här undernätet som en källa från vilket konto kan nås.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Kommer virtuella nätverks-åtkomstkontrollistor och IP-brandväggen att avvisa begäranden eller anslutningar? 

När IP-brandvägg eller virtuella nätverksåtkomstregler läggs till får endast begäranden från tillåtna källor giltiga svar. Andra begäranden kasseras med en 403 (Förbjudet). Det är viktigt att skilja Azure Cosmos-kontots brandvägg från en brandvägg på anslutningsnivå. Källan kan fortfarande ansluta till tjänsten och själva anslutningarna avvisas inte.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mina begäranden började blockeras när jag aktiverade tjänstslutpunkten till Azure Cosmos DB i undernätet. Vad hände?

När tjänstslutpunkten för Azure Cosmos DB är aktiverad i ett undernät växlar källan till den trafik som når kontot från offentlig IP till virtuellt nätverk och undernät. Om ditt Azure Cosmos-konto endast har IP-baserad brandvägg, skulle trafik från tjänstaktiverat undernät inte längre matcha IP-brandväggsreglerna och därför avvisas. Gå igenom stegen för att sömlöst migrera från IP-baserad brandvägg till virtuell nätverksbaserad åtkomstkontroll.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Behövs ytterligare RBAC-behörigheter för Azure Cosmos-konton med slutpunkter för VNET-tjänsten?

När du har lagt till slutpunkterna för VNet-tjänsten i ett Azure Cosmos-konto `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` för att göra eventuella ändringar i kontoinställningarna behöver du åtkomst till åtgärden för alla virtuella nätverk som konfigurerats på ditt Azure Cosmos-konto. Den här behörigheten krävs eftersom auktoriseringsprocessen validerar åtkomst till resurser (till exempel databasresurser och virtuella nätverksresurser) innan du utvärderar några egenskaper.
 
Auktoriseringen validerar behörigheten för VNet-resursåtgärd även om användaren inte anger VNET-nätkontrollanterna med Azure CLI. För närvarande stöder Azure Cosmos-kontots kontrollplan att ange det fullständiga tillståndet för Azure Cosmos-kontot. En av parametrarna för kontrollplansamtalen är `virtualNetworkRules`. Om den här parametern inte anges gör Azure CLI ett `virtualNetworkRules` hämta databasanrop för att hämta och använder det här värdet i uppdateringsanropet.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Har de peer-in-virtuella nätverken också åtkomst till Azure Cosmos-kontot? 
Endast virtuellt nätverk och deras undernät som läggs till i Azure Cosmos-kontot har åtkomst. Deras peered virtuella nätverk kan inte komma åt kontot förrän undernäten i peer-indelade virtuella nätverk läggs till i kontot.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Vad är det maximala antalet undernät som får komma åt ett enda Cosmos-konto? 
För närvarande kan du ha högst 256 undernät tillåtna för ett Azure Cosmos-konto.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan jag aktivera åtkomst från VPN och Express Route? 
För att komma åt Azure Cosmos-konto via Express-vägen från lokalt måste du aktivera Microsoft-peering. När du har placerat IP-brandväggen eller reglerna för åtkomst till virtuella nätverk kan du lägga till de offentliga IP-adresser som används för Microsoft-peering på ip-brandväggen för Azure Cosmos-kontot för att tillåta åtkomst till Azure Cosmos-kontot på lokala tjänster. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Måste jag uppdatera reglerna för NSG-grupper (Network Security Groups). 
NSG-regler används för att begränsa anslutningen till och från ett undernät med virtuellt nätverk. När du lägger till tjänstslutpunkt för Azure Cosmos DB i undernätet behöver du inte öppna utgående anslutning i NSG för ditt Azure Cosmos-konto. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Är tjänstslutpunkter tillgängliga för alla virtuella nätverk?
Nej, endast virtuella Azure Resource Manager-nätverk kan ha tjänstslutpunkten aktiverad. Klassiska virtuella nätverk stöder inte tjänstslutpunkter.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan jag "Acceptera anslutningar från offentliga Azure-datacenter" när tjänstslutpunktsåtkomst är aktiverad för Azure Cosmos DB?  
Detta krävs bara när du vill att ditt Azure Cosmos DB-konto ska nås av andra Azure-första parts tjänster som Azure Data factory, Azure Cognitive Search eller någon tjänst som distribueras i en viss Azure-region.


## <a name="next-steps"></a>Nästa steg

* [Begränsa Azure Cosmos-kontoåtkomst till undernät i virtuella nätverk](how-to-configure-vnet-service-endpoint.md)
* [Konfigurera IP-brandväggen för ditt Azure Cosmos-konto](how-to-configure-firewall.md)

