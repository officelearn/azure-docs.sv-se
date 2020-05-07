---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 20b78a883b71e7ea6d24097a5166f6d4a12d4d77
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838275"
---
|Name |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Behållar register ska krypteras med en kundhanterad nyckel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Granska behållar register som inte har kryptering aktiverat med Kundhanterade nycklar (CMK). Mer information om CMK-kryptering finns på: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Granskning, inaktive rad |1.0.0 – för hands version |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Behållar register ska inte tillåta obegränsad nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Granska behållar register som inte har några nätverks-eller VNET-regler konfigurerade och som standard tillåter all nätverks åtkomst. Behållar register med minst en IP/brand Väggs regel eller ett konfigurerat virtuellt nätverk bedöms som kompatibla. Mer information om hur du Container Registry nätverks regler finns i: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Granskning, inaktive rad |1.0.0 – för hands version |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Behållar register ska använda privata länkar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Granska behållar register som inte har minst en godkänd privat slut punkts anslutning. Klienter i ett virtuellt nätverk kan på ett säkert sätt få åtkomst till resurser som har privata slut punkts anslutningar via privata länkar. Mer information finns på: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Granskning, inaktive rad |1.0.0 – för hands version |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
