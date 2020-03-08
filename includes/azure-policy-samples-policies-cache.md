---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 8e1d2fae03612c925949a4dcd9cba35b2f4e7f51
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669219"
---
|Namn |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Endast säkra anslutningar till din Redis Cache ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Redis Cache. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
