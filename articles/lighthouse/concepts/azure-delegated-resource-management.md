---
title: Azure-delegerad resurshantering
description: Med hanterade tjänster kan tjänste leverantörer sälja resurs hanterings erbjudanden till kunder på Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: c2aa393f04a8db470dd8b739c0ed003477df0221
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615420"
---
# <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Azure-delegerad resurs hantering är en av de viktigaste komponenterna i Azure dataLighthouses. Med Azure-delegerad resurs hantering kan tjänst leverantörer förenkla kund engagemang och onboarding-upplevelser, samtidigt som de delegerade resurserna hanteras i stor skala med flexibilitet och precision.

## <a name="what-is-azure-delegated-resource-management"></a>Vad är Azure-delegerad resurs hantering?

Azure-delegerad resurs hantering möjliggör logisk projektion av resurser från en klient organisation till en annan klient organisation. Detta gör det möjligt för behöriga användare i en Azure Active Directory (Azure AD) att utföra hanterings åtgärder för olika Azure AD-klienter som tillhör sina kunder. Tjänste leverantörer kan logga in på sin egen Azure AD-klient och ha behörighet att arbeta i delegerade kund prenumerationer och resurs grupper. Detta gör det möjligt för dem att utföra hanterings åtgärder på uppdrag av sina kunder, utan att behöva logga in på varje enskild kund klient.

> [!NOTE]
> Azure-delegerad resurs hantering kan också användas [inom ett företag som har flera Azure AD-klienter för](enterprise.md) att förenkla hanteringen av flera innehavare.

Med Azure-delegerad resurs hantering kan behöriga användare arbeta direkt i kontexten för en kund prenumeration utan att ha ett konto i den kundens klient organisation eller som medägare till kundens klient. De kan också [Visa och hantera alla delegerade kund prenumerationer på sidan nya **Mina kunder** ](../how-to/view-manage-customers.md) i Azure Portal.

Med [hanterings upplevelsen för flera innehavare](cross-tenant-management-experience.md) kan du arbeta effektivare med Azures hanterings tjänster som Azure Policy, Azure Security Center och mycket annat. All service provider-aktivitet spåras i aktivitets loggen, som lagras i både tjänst leverantörens och kundens klienter. Det innebär att både kund-och tjänst leverantören enkelt kan identifiera den användare som är kopplad till eventuella ändringar.

När du registrerar en kund i Azure-delegerad resurs hantering kommer de att ha åtkomst till sidan nya **tjänst leverantörer** i Azure Portal, där de kan [Bekräfta och hantera sina erbjudanden, tjänst leverantörer och delegerade resurser](../how-to/view-manage-service-providers.md). Om kunden någonsin vill återkalla åtkomsten för en tjänst leverantör kan de göra det när som helst.

Du kan [publicera det nya erbjudandet för hanterings tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md) för att enkelt publicera kunder till Azure-delegerad resurs hantering. Du kan också [slutföra onboarding-processen genom att distribuera Azure Resource Manager mallar](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Så här fungerar Azure-delegerad resurs hantering

Så här fungerar Azure-delegerad resurs hantering på hög nivå:

1. Som tjänst leverantör identifierar du åtkomsten (roller) som dina grupper, tjänstens huvud namn eller användare behöver för att hantera kundens Azure-resurser. Åtkomst definitionen innehåller tjänst leverantörens klient-ID tillsammans med den nödvändiga åtkomsten för erbjudandet, definierad med **principalId** -identiteter från din klient mapp som är mappad till [inbyggda **roll definitions** -värden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (deltagare, VM Deltagare, läsare osv.).
2. Du kan ange den här åtkomsten och publicera kunden till Azure-delegerad resurs hantering på något av följande sätt:
   - [Publicera ett erbjudande för Azure Marketplace Managed Services](../how-to/publish-managed-services-offers.md) (privat eller offentlig) som kunden accepterar
   - [Distribuera en Azure Resource Manager-mall till kundens klient](../how-to/onboard-customer.md) för en eller flera specifika prenumerationer eller resurs grupper
3. När kunden har registrerats kan behöriga användare logga in på din tjänst leverantörs klient och utföra hanterings uppgifter för den angivna kund omfattningen, baserat på den åtkomst som du har definierat.

## <a name="support-for-azure-delegated-resource-management"></a>Stöd för Azure-delegerad resurs hantering

Om du behöver hjälp som rör Azure-delegerad resurs hantering kan du öppna en supportbegäran i Azure Portal. Välj **teknisk**för **typ av problem**. Välj en prenumeration och välj sedan **delegerad resurs hantering** (under **övervakning & hantering**).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig mer om [Managed Services-erbjudanden på Azure Marketplace](managed-services-offers.md).