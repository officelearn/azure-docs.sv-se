---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96477083"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Dataskydd |4.8 |Kryptera känslig information i vila |[Variabler för Automation-konton ska vara krypterade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> När du skapar en Automation-konto-variabel kan du ange dess kryptering och lagring genom att Azure Automation som en säker till gång. När du har skapat variabeln kan du inte ändra dess krypterings status utan att skapa variabeln på nytt. Om du har variabler för Automation-konto som lagrar känsliga data som inte redan är krypterade måste du ta bort dem och återskapa dem som krypterade variabler. En Azure Security Center rekommendation är att kryptera alla Azure Automation variabler enligt beskrivningen i [Automation-kontots variabler bör vara krypterade](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Om du har okrypterade variabler som du inte vill ska uteslutas från den här säkerhets rekommendationen läser du [undanta en resurs från rekommendationer och säkra Poäng](../../../../articles/security-center/exempt-resource.md) för att skapa en undantags regel.