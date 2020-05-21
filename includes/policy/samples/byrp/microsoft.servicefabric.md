---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22f6f040d333a526940a688c16277be568520f50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651193"
---
|Name |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric tillhandahåller tre skydds nivåer (ingen, sign och EncryptAndSign) för nod-till-nod-kommunikation med ett primärt kluster certifikat. Ange skydds nivå för att säkerställa att alla nod-till-nod-meddelanden är krypterade och digitalt signerade |Granska, neka, inaktive rad |1.1.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Granska användningen av klientautentisering endast via Azure Active Directory i Service Fabric |Granska, neka, inaktive rad |1.1.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
