---
title: Azure-delegerad resurshantering
description: Med hanterade tjänster kan tjänsteleverantörer sälja resurshanteringserbjudanden till kunder på Azure Marketplace.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904310"
---
# <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Azure-delegerad resurshantering är en av de viktigaste komponenterna i Azure Lighthouse. Med Azure-delegerad resurshantering kan tjänsteleverantörer förenkla kundengagemang och introduktionsupplevelser, samtidigt som de delegerade resurserna hanteras i stor skala med smidighet och precision.

## <a name="what-is-azure-delegated-resource-management"></a>Vad är Azure-delegerad resurshantering?

Azure-delegerad resurshantering möjliggör logisk projektion av resurser från en klient till en annan klient. På så sätt kan behöriga användare i en Azure Active Directory-klient (Azure AD) utföra hanteringsåtgärder för olika Azure AD-klienter som tillhör deras kunder. Tjänstleverantörer kan logga in på sin egen Azure AD-klient och ha auktorisering att arbeta i delegerade kundprenumerationer och resursgrupper. På så sätt kan de utföra hanteringsåtgärder på uppdrag av sina kunder, utan att behöva logga in på varje enskild kundklient.

> [!NOTE]
> Azure-delegerad resurshantering kan också användas [i ett företag som har flera Azure AD-klienter för egen hand](enterprise.md) för att förenkla hantering av flera innehavare.

Med Azure-delegerad resurshantering kan behöriga användare arbeta direkt i samband med en kundprenumeration utan att ha ett konto i kundens klientorganisation eller vara delägare i kundens klientorganisation. De kan också [visa och hantera alla delegerade kundprenumerationer på den nya sidan Mina **kunder** ](../how-to/view-manage-customers.md) i Azure-portalen.

[Hanteringsupplevelsen mellan innehavare](cross-tenant-management-experience.md) hjälper dig att arbeta mer effektivt med Azure-hanteringstjänster som Azure Policy, Azure Security Center med mera. All tjänsteprovideraktivitet spåras i aktivitetsloggen, som lagras i både tjänsteleverantörens och kundens klienter. Detta innebär att både kunden och tjänsteleverantören enkelt kan identifiera användaren som är associerad med eventuella ändringar.

När du går ombord på en kund till Azure-delegerad resurshantering har de åtkomst till den nya **tjänstleverantörssidan** i Azure-portalen, där de kan [bekräfta och hantera sina erbjudanden, tjänsteleverantörer och delegerade resurser](../how-to/view-manage-service-providers.md). Om kunden någonsin vill återkalla åtkomst för en tjänsteleverantör, kan de göra det här när som helst.

Du kan [publicera den nya erbjudandetypen för hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md) för att enkelt gå ombord på kunder till Azure-delegerad resurshantering. Du kan också [slutföra introduktionsprocessen genom att distribuera Azure Resource Manager-mallar](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Så här fungerar Azure-delegerad resurshantering

Så här fungerar Azure-delegerad resurshantering på hög nivå:

1. Som tjänsteleverantör identifierar du den åtkomst (roller) som dina grupper, tjänsthuvudnamn eller användare behöver för att hantera kundens Azure-resurser. Åtkomstdefinitionen innehåller tjänsteleverantörens klient-ID tillsammans med den nödvändiga åtkomsten för erbjudandet, definierad med **principalId-identiteter** från klienten mappade till [inbyggda **rollDefinitionsvärden** ](../../role-based-access-control/built-in-roles.md) (Deltagare, VM-deltagare, läsare osv.).
2. Du anger den här åtkomsten och ombord på kunden till Azure-delegerad resurshantering på ett av två sätt:
   - [Publicera ett erbjudande om hanterade azure marketplace-tjänster](../how-to/publish-managed-services-offers.md) (privat eller offentligt) som kunden accepterar
   - [Distribuera en Azure Resource Manager-mall till kundens klientorganisation](../how-to/onboard-customer.md) för en eller flera specifika prenumerationer eller resursgrupper
3. När kunden har varit ombord kan behöriga användare logga in på din tjänsteleverantörsklient och utföra hanteringsuppgifter i det angivna kundomfånget, baserat på den åtkomst som du har definierat.

> [!NOTE]
> Delegering av en prenumeration mellan två klienter över separata moln stöds inte.

## <a name="support-for-azure-delegated-resource-management"></a>Stöd för Azure-delegerad resurshantering

Om du behöver hjälp med Azure-delegerad resurshantering kan du öppna en supportbegäran i Azure-portalen. För **ärendetyp**väljer du **Teknisk**. Välj en prenumeration och välj sedan **Fyr** (under **Övervakning & Hantering).**

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanteringsupplevelser mellan klienter.](cross-tenant-management-experience.md)
- Läs mer om [erbjudanden om hanterade tjänster på Azure Marketplace](managed-services-offers.md).