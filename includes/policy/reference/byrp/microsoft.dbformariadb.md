---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a68c3ff276c4bd1a9e33084f9745beac48f4b83d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992605"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Med Azure Database for MariaDB kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB-servern bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Virtuella nätverksbaserade brand Väggs regler används för att aktivera trafik från ett speciellt undernät till Azure Database for MariaDB samtidigt som trafiken förblir inom Azure-gränser. Den här principen gör det möjligt att granska om Azure Database for MariaDB har en tjänst slut punkt för virtuella nätverk som används. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Den privata slut punkten måste vara aktive rad för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Anslutningar för privata slut punkter tvingar säker kommunikation genom att aktivera privat anslutning till Azure Database for MariaDB. Konfigurera en privat slut punkts anslutning för att ge åtkomst till trafik som kommer från kända nätverk och förhindra åtkomst från alla andra IP-adresser, inklusive i Azure. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Åtkomst till offentliga nätverk ska inaktive ras för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Inaktive ring av den offentliga nätverks åtkomst egenskapen förbättrar säkerheten genom att se till att Azure Database for MariaDB endast kan nås från en privat slut punkt. Den här konfigurationen inaktiverar strikt åtkomst från något offentligt adress utrymme utanför Azure IP-intervall och nekar alla inloggningar som matchar IP-eller virtuella nätverksbaserade brand Väggs regler. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
