---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 77a9689d7203f3c662a4d703f324079f5dbf4a08
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988117"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Identitets- och åtkomstkontroll |3.9 |Använda Azure Active Directory |[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Dataskydd |4.8 |Kryptera känslig information i vila |[Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för efterlevnad-NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Mer information om den här standarden för efterlevnad finns i [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC-2 (7) |Konto hanterings \| Role-Baseds scheman |[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

