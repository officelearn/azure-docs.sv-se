---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 45cb4e589a00a2ab17fee280bb6066eaee4f24c9
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669176"
---
|Namn |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Behållar register ska krypteras med en kundhanterad nyckel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Granska behållar register som inte har kryptering aktiverat med Kundhanterade nycklar (CMK). Mer information om CMK-kryptering finns i: https://aka.ms/acr/CMK. |Granskning, inaktive rad |1.0.0 – för hands version |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Behållar register ska inte tillåta obegränsad nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Granska behållar register som inte har några nätverks-eller VNET-regler konfigurerade och som standard tillåter all nätverks åtkomst. Behållar register med minst en IP/brand Väggs regel eller ett konfigurerat virtuellt nätverk bedöms som kompatibla. Mer information om hur du Container Registry nätverks regler finns på: https://aka.ms/acr/vnet. |Granskning, inaktive rad |1.0.0 – för hands version |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
