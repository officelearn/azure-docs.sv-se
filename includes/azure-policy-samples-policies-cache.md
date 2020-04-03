---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: a90f51ea2ec554e6a651d0a0ee0801a3d91f39a5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623939"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Endast säkra anslutningar till Redis-cachen ska aktiveras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granskning aktivera endast anslutningar via SSL till Redis Cache. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från nätverkslagerattacker som man-in-the-middle, avlyssning och sessionskapning |Granskning, neka, inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
