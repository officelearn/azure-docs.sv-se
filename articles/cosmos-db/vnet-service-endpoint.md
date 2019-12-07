---
title: Säker åtkomst till ett Azure Cosmos DB-konto med hjälp av Azure Virtual Network-tjänstens slut punkt
description: Det här dokumentet beskriver om virtuellt nätverk och åtkomst kontroll för undernät för ett Azure Cosmos-konto.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 0023710ff3cfe180b628d1da14b8a3ea9c136026
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896238"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Åtkomst Azure Cosmos DB från virtuella nätverk (VNet)

Du kan konfigurera Azure Cosmos-kontot så att det bara tillåter åtkomst från ett speciellt undernät i ett virtuellt nätverk (VNet). Genom att aktivera [tjänstens slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) för att komma åt Azure Cosmos db i under nätet i ett virtuellt nätverk skickas trafiken från det under nätet till Azure Cosmos dB med under nätets identitet och Virtual Network. När Azure Cosmos DB tjänstens slut punkt har Aktiver ATS kan du begränsa åtkomsten till under nätet genom att lägga till den i ditt Azure Cosmos-konto.

Som standard är ett Azure Cosmos-konto tillgängligt från alla källor om begäran åtföljs av en giltig autentiseringstoken. När du lägger till ett eller flera undernät inom virtuella nätverk får endast begär Anden som kommer från dessa undernät ett giltigt svar. Begär Anden som kommer från en annan källa får ett 403-svar (ej tillåtet). 

## <a name="frequently-asked-questions"></a>Vanliga frågor

Här följer några vanliga frågor om hur du konfigurerar åtkomst från virtuella nätverk:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan jag ange både Virtual Network Service-slutpunkt och princip för IP-åtkomstkontroll på ett Azure Cosmos-konto? 

Du kan aktivera både Virtual Network Service-slutpunkten och en kontroll princip för IP-aka på ditt Azure Cosmos-konto. Dessa två funktioner är kompletterande och säkerställer gemensamt isolering och säkerhet för ditt Azure Cosmos-konto. Att använda IP-brandvägg säkerställer att statiska IP-adresser kan komma åt ditt konto. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hur gör jag för att begränsa åtkomsten till undernät i ett virtuellt nätverk? 

Det finns två steg som krävs för att begränsa åtkomsten till Azure Cosmos-kontot från ett undernät. Först tillåter du att trafik från undernät bär under nätet och den virtuella nätverks identiteten till Azure Cosmos DB. Detta görs genom att aktivera tjänstens slut punkt för Azure Cosmos DB på under nätet. Sedan lägger du till en regel i Azure Cosmos-kontot som anger det här under nätet som en källa från vilken kontot kan nås.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Kommer virtuella nätverks-ACL: er och IP-brandvägg avvisa begär Anden eller anslutningar? 

När IP-brandvägg eller åtkomst regler för virtuella nätverk läggs till får endast förfrågningar från tillåtna källor giltiga svar. Andra begär Anden avvisas med en 403 (förbjuden). Det är viktigt att skilja Azure Cosmos-kontots brand vägg från en brand vägg för anslutnings nivå. Källan kan fortfarande ansluta till tjänsten och själva anslutningarna avvisas inte.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mina förfrågningar började bli blockerade när jag aktiverade tjänst slut punkten till Azure Cosmos DB på under nätet. Vad hände?

När tjänstens slut punkt för Azure Cosmos DB har Aktiver ATS på ett undernät når trafiken till konto växlarna från offentlig IP-adress till virtuellt nätverk och undernät. Om ditt Azure Cosmos-konto har en IP-baserad brand vägg, skulle trafik från tjänstens aktiverade undernät inte längre matcha IP-brandväggens regler och därför avvisas. Gå igenom stegen för att sömlöst migrera från IP-baserad brand vägg till virtuell nätverks baserad åtkomst kontroll.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Krävs ytterligare RBAC-behörigheter för Azure Cosmos-konton med VNET-tjänstens slut punkter?

När du har lagt till tjänst slut punkterna för VNet i ett Azure Cosmos-konto måste du ha åtkomst till `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` åtgärden för alla virtuella nätverk som kon figurer ATS på ditt Azure Cosmos-konto för att göra ändringar i konto inställningarna. Den här behörigheten krävs eftersom verifierings processen validerar åtkomst till resurser (till exempel databas och virtuella nätverks resurser) innan du utvärderar några egenskaper.
 
Auktoriseringen verifierar behörighet för åtgärden VNet-resurs även om användaren inte anger de virtuella nätverkets ACL: er med Azure CLI. För närvarande har Azure Cosmos-kontots kontroll plan stöd för att ange det fullständiga läget för Azure Cosmos-kontot. En av parametrarna för kontroll planets anrop är `virtualNetworkRules`. Om den här parametern inte anges gör Azure CLI ett Hämta databas anrop för att hämta `virtualNetworkRules` och använder det här värdet i uppdaterings anropet.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Har de peer-virtuella nätverken också åtkomst till Azure Cosmos-kontot? 
Endast virtuella nätverk och deras undernät som har lagts till i Azure Cosmos-kontot har åtkomst. Deras peer-virtuella nätverk kan inte komma åt kontot förrän under näten i peer-kopplat virtuella nätverk har lagts till i kontot.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Vilket är det maximala antalet undernät som får åtkomst till ett enskilt Cosmos-konto? 
För närvarande kan du ha högst 64 undernät tillåtna för ett Azure Cosmos-konto.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan jag aktivera åtkomst från VPN och Express Route? 
För att få åtkomst till Azure Cosmos-kontot via Express Route från lokalt måste du aktivera Microsoft-peering. När du har angett IP-brandvägg eller åtkomst regler för virtuella nätverk kan du lägga till de offentliga IP-adresser som används för Microsoft-peering i ditt Azure Cosmos-konto IP-brandvägg för att ge lokala tjänster åtkomst till Azure Cosmos-kontot. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Måste jag uppdatera reglerna för nätverks säkerhets grupper (NSG)? 
NSG-regler används för att begränsa anslutningen till och från ett undernät med ett virtuellt nätverk. När du lägger till tjänst slut punkten för Azure Cosmos DB till under nätet behöver du inte öppna en utgående anslutning i NSG för ditt Azure Cosmos-konto. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Är tjänst slut punkter tillgängliga för alla virtuella nätverk?
Nej, endast Azure Resource Manager virtuella nätverk kan ha tjänstens slut punkt aktive rad. Klassiska virtuella nätverk har inte stöd för tjänst slut punkter.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan jag "acceptera anslutningar från offentliga Azure-datacenter" när åtkomst till tjänst slut punkten är aktive rad för Azure Cosmos DB?  
Detta krävs bara om du vill att ditt Azure Cosmos DBs konto ska kunna nås av andra Azure-tjänster från första part som Azure Data Factory, Azure Kognitiv sökning eller någon tjänst som distribueras i en specifik Azure-region.


## <a name="next-steps"></a>Nästa steg

* [Så här begränsar du Azure Cosmos-kontots åtkomst till undernät i virtuella nätverk](how-to-configure-vnet-service-endpoint.md)
* [Så här konfigurerar du IP-brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)

