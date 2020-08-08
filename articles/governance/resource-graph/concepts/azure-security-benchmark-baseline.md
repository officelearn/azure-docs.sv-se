---
title: Azure resurs diagram säkerhets bas linje för Azures säkerhets prestanda
description: Säkerhets bas linjen för Azure Resource Graph ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d2ef76a054642807f0d72a758ae084a19557caf8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009009"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Azure resurs diagram säkerhets bas linje för Azures säkerhets prestanda

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark](../../../security/benchmarks/overview.md) till Azure Resource Graph. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Resource Graph. **Kontroller** som inte gäller Azure Resource Graph har uteslutits. Om du vill se hur Azure Resource Graph fullständigt mappar till Azures säkerhets benchmark, kan du läsa mer i den [fullständiga Azure-Virtual Network säkerhets bas linje mappnings fil](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).



## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Resource Graph ger till gång till resurs typer och egenskaper baserat på rollbaserad åtkomst kontroll i Azure (Azure RBAC). Granska och granska åtkomst beviljad till säkerhets objekt (användare, grupper och tjänst konton) regelbundet för att se till att frågor returnerar resultat för lämpliga resurser.

* [Behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Så här använder du granskningar av Azure Identity Access](../../../active-directory/governance/access-reviews-overview.md)


**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll (RBAC) för att styra åtkomsten till data och resurser. Om du vill använda Azure Resource Graph måste du också ha nödvändig åtkomst till de resurser som du vill fråga. Den här åtkomsten bör begränsas till skrivskyddad och beviljas endast för nödvändig personal.

* [Behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Konfigurera RBAC i Azure](../../../role-based-access-control/role-assignments-rest.md)


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser som stöds i dina prenumerationer, hanterings grupper och klient organisationer. Se till att du har rätt behörigheter i din klient organisation och att du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

* [Så här skapar du frågor med Azure Resource Graph](../first-query-portal.md)

* [Förstå Azure RBAC](../../../role-based-access-control/overview.md)


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov. Använd Azure Resource Graph för att fråga efter godkända Azure-resurser och ändrings historik (för hands version) för att granska ögonblicks bilder och se vad som har ändrats.

* [Fråga Azure-resurser med Azure Resource Graph](../first-query-portal.md)

* [Hämta resursändringar](../how-to/get-resource-changes.md)


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera resurser i dina prenumerationer, hanterings grupper och klient organisationer. Se till att alla Azure-resurser i miljön är godkända.

* [Fråga Azure-resurser med Azure Resource Graph](../first-query-portal.md)

* [Exempel: Start frågor för Azure Resource Graph](../samples/starter.md)


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../../../security/benchmarks/security-baselines-overview.md)
