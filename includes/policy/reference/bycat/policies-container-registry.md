---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 52997495b5c46c89d3abfbb836f90d2363ee6d4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91820723"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Behållar register ska krypteras med en kundhanterad nyckel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Granska behållar register som inte har kryptering aktiverat med Kundhanterade nycklar (CMK). Azure krypterar automatiskt register innehållet i vila med tjänst hanterade nycklar. Du kan komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av en nyckel som du skapar och hanterar i Azure Key Vault. Mer information om CMK-kryptering finns på: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Behållar register ska inte tillåta obegränsad nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Granska behållar register som inte har några nätverks-eller brand Väggs regler konfigurerade och så tillåter all nätverks åtkomst som standard. Genom att begränsa nätverks åtkomst skyddar du behållar register från potentiella hot. Behållar register med minst en IP/brand Väggs regel eller ett konfigurerat virtuellt nätverk bedöms som kompatibla. Mer information om Container Registry nätverks regler finns i: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) och [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Behållar register ska använda privata länkar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Granska behållar register som inte har minst en godkänd privat slut punkts anslutning. Klienter i ett virtuellt nätverk kan på ett säkert sätt få åtkomst till resurser som har privata slut punkts anslutningar via privata länkar. Offentlig åtkomst kan sedan inaktive ras för att säkerställa att endast privata länkar kan användas för att ansluta till registret. Mer information finns på: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
