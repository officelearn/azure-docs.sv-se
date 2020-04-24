---
title: Integrering med Azure-principer
description: Azure Policy är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer som tillämpar regler över dina resurser.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 74c087031a024b0c50d6c40057a4e2eda554be6f
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116085"
---
# <a name="integration-with-azure-policy"></a>Integrering med Azure Policy

Azure Policy är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer som upprätthåller regler över dina resurser för att se till att resurserna är kompatibla med företagets standarder och service nivå avtal. Azure Policy utvärderar resurser för att inte uppfylla de principer som du tilldelar. 

Azure Batch har två inbyggda tillägg som hjälper dig att hantera efterlevnad av principer. 

|**Namn**...|   **Beskrivning**|**Påverkan (ar)**|  **Version**|    **Källa**
|----------------|----------|----------|----------------|---------------|
|Diagnostikloggar i batch-konton måste vara aktiverade|   Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras|AuditIfNotExists, inaktiverat|  2.0.0|  GitHub|
|Mått varnings regler ska konfigureras för batch-konton| Granska konfigurationen av mått varnings regler för batch-kontot för att aktivera nödvändigt mått|   AuditIfNotExists, inaktiverat| 1.0.0|  GitHub|

Princip definitioner beskriver de villkor som måste uppfyllas. Ett villkor jämför resurs egenskapen med ett värde som krävs. Resurs egenskaps fält öppnas med fördefinierade alias. Du använder alias för egenskaper för att få åtkomst till vissa egenskaper för en resurs typ. Alias gör att du kan begränsa vilka värden eller villkor som tillåts för en egenskap på en resurs. Varje alias mappar till sökvägar i olika API-versioner för en specifik resurs typ. Under princip utvärderingen hämtar princip motorn egenskaps Sök vägen för API-versionen.

De resurser som krävs av batch inkluderar: account, Compute Node, pool, Job och Task. Därför använder du egenskaps Ali Aset för att få åtkomst till vissa egenskaper för dessa resurser. Lär dig mer om [alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

För att se till att du känner till aktuella alias och granskar dina resurser och principer, använder du Azure policy-tillägget för Visual Studio Code. Den kan installeras på alla plattformar som stöds av Visual Studio Code. Det här stödet omfattar Windows, Linux och macOS. Se [installations rikt linjer](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



