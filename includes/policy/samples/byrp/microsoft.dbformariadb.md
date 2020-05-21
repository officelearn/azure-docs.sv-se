---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2378c49d5b2c95ffad3a1b320521d77b4ab59e2e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656779"
---
|Name |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Den här principen granskar alla Azure Database for MariaDB med Geo-redundant säkerhets kopiering inte aktive rad. |Granskning, inaktive rad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB-servern bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Den här principen granskar MariaDB-servrar som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. Mer information finns på [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork) . |AuditIfNotExists, inaktiverat |1.0.1 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Den privata slut punkten måste vara aktive rad för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Den här principen granskar MariaDB-servrar som inte har kon figurer ATS för att använda en privat slut punkt. Mer information finns på [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink) . |AuditIfNotExists, inaktiverat |1.0.1 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Åtkomst till offentliga nätverk ska inaktive ras för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Den här principen granskar MariaDB-servrar i din miljö med offentlig nätverks åtkomst aktive rad. Mer information finns på [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542) . |Granskning, inaktive rad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
