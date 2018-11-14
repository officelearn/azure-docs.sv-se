---
title: Skydda åtkomst till ett Azure Cosmos DB-konto med hjälp av Azure Virtual Network-tjänstslutpunkt
description: Det här dokumentet beskriver om virtuellt nätverk och undernät åtkomstkontroll för ett Azure Cosmos-konto.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 5f3ee4e5af32ff0ea90d8807c65d14ddce417e7b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626090"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Få åtkomst till Azure Cosmos DB-resurser från virtuella nätverk

Du kan konfigurera Azure Cosmos-konto för att tillåta åtkomst från ett specifikt undernät för virtuellt nätverk (VNET). Genom att aktivera [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för att komma åt Azure Cosmos DB i undernät inom ett virtuellt nätverk, trafik från undernätet är skickas till Azure Cosmos DB med identiteten för undernät och virtuellt nätverk. När Azure Cosmos DB-tjänstslutpunkt har aktiverats kan du begränsa åtkomsten till undernätet genom att lägga till ditt Azure Cosmos-konto.

Som standard är ett Azure Cosmos-konto kan nås från valfri källa om begäran åtföljs av en giltig auktoriseringstoken. När du lägger till en eller flera undernät i virtuella nätverk får ett giltigt svar med endast de förfrågningar som kommer från dessa undernät. Förfrågningar som kommer från någon annan källa får ett 404 (hittades inte) svar. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Här följer några vanliga frågor om hur du konfigurerar åtkomst från virtuella nätverk:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan jag ange både tjänstslutpunkt för virtuellt nätverk och IP-principer för åtkomstkontroll för ett Azure Cosmos-konto? 

Du kan aktivera både tjänstslutpunkt för virtuellt nätverk och en principer för IP-åtkomstkontroll (även kallat brandvägg) på ditt Azure Cosmos-konto. De här två funktionerna är kompletterande och kontrollera sammantaget isolering och säkerhet för Azure Cosmos-kontot. Med hjälp av IP säkerställer brandväggen att statiska IP-adresser kan komma åt ditt konto. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hur jag för att begränsa åtkomst till undernät i ett virtuellt nätverk? 

Det finns två steg som krävs för att begränsa åtkomsten till Azure Cosmos-konto från ett undernät. Först måste tillåta du trafik från undernätet för att utföra dess undernät och virtuella nätverksidentitet till Azure Cosmos DB. Detta görs genom att aktivera tjänstslutpunkten för Azure Cosmos DB på undernätet. Sedan lägger till en regel i Azure Cosmos-kontot som anger att det här undernätet en källa som-konton kan öppnas.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>ACL: er för virtuellt nätverk och IP-brandvägg avvisar begäranden eller anslutningar? 

När IP-brandvägg eller virtuella nätverk har åtkomst till regler har lagts till, bara begäranden från tillåtna källor get giltiga svar. Andra begäranden avvisas med 404 (hittades inte). Det är viktigt att skilja Brandvägg för Azure Cosmos-konto från en brandvägg för nivån. Källan kan fortfarande ansluta till tjänsten och anslutningarna själva avvisades inte.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mina begäranden igång komma blockeras när jag aktiverad tjänstslutpunkt till Azure Cosmos DB på undernätet. Vad hände?

När tjänstslutpunkt för Azure Cosmos DB är aktiverat på ett undernät, växlar källan för trafiken når kontot från offentlig IP-adress till virtuellt nätverk och undernät. Om ditt Azure Cosmos-konto har IP-baserad brandvägg endast trafik från aktiverad undernät inte längre matchar IP-brandväggsregler och därför avvisas. Gå igenom stegen för att migrera sömlöst från IP-baserad brandvägg till virtuella nätverk-baserad åtkomstkontroll.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Har peer-kopplade virtuella nätverk även åtkomst till Azure Cosmos-konto? 
Endast virtuella nätverk och deras undernät som har lagts till Azure Cosmos-kontot har åtkomst. Sina peer-kopplade virtuella nätverk kan inte komma åt kontot förrän undernät i peer-kopplade virtuella nätverk har lagts till kontot.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Vad är det maximala antalet undernät tillgång till ett enda Cosmos-konto? 
Du kan för närvarande kan ha högst 64 undernät tillåts för ett Azure Cosmos-konto.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan jag aktivera åtkomst från VPN och Expressroute? 
För att komma åt Azure Cosmos-konto via expressroute från lokalt, skulle du behöva aktivera Microsoft-peering. När du placerar IP-brandvägg eller åtkomstregler för virtuellt nätverk måste du lägga till de offentliga IP-adresser som används för Microsoft-peering i Azure Cosmos-kontot IP-brandväggen för att tillåta lokala tjänster åtkomst till Azure Cosmos-konto. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Behöver jag uppdatera regler för Nätverkssäkerhetsgrupper (NSG)? 
NSG-regler används för att begränsa anslutningsmöjligheten till och från ett undernät med ett virtuellt nätverk. När du lägger till tjänsteslutpunkt för Azure Cosmos DB till undernätet finns inget behov att öppna utgående anslutningar i NSG för ditt Azure Cosmos-konto. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Finns tjänstslutpunkter för alla virtuella nätverk?
Nej, endast Azure Resource Manager-nätverk kan ha tjänstslutpunkt aktiverad. Klassiska virtuella nätverk stöder inte tjänstslutpunkter.


## <a name="next-steps"></a>Nästa steg

* [Hur du begränsar åtkomsten för Azure Cosmos-kontot till undernät i virtuella nätverk](how-to-configure-vnet-service-endpoint.md)
* [Så här konfigurerar du IP-Brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)

