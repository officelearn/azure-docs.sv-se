---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cf56786656d830d2fea4c9e916f350fc392f5cf0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379272"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Princip<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |1,1 |Skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandväggen på din Virtual Network |[Den privata slut punkten måste vara aktive rad för MySQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|Dataskydd |4.4 |Kryptera all känslig information i överföring |[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|Dataåterställning |9,1 |Säkerställ regelbunden automatisk säkerhets kopiering |[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|Dataåterställning |9,2 |Utför fullständiga säkerhets kopieringar och säkerhetskopiera alla Kundhanterade nycklar |[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för regelefterlevnad-CIS Microsoft Azure grunderna för benchmark-1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Mer information om den här standarden för efterlevnad finns i [CIS Microsoft Azure Stiftelses benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domain |Kontroll-ID |Kontroll rubrik |Princip<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Databas tjänster |4,11 |Se till att tvinga SSL-anslutning är inställt på ENABLEd för MySQL-databasserver |[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

