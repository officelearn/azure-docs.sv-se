---
title: Azure delegerad resurshantering - Azure fyr
description: Hanterade tjänster erbjudanden tillåta tjänstleverantörer att sälja resource management erbjudanden till kunder i Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: cec6453cdf339e82420a1b12af6c8e60526fdc03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809969"
---
# <a name="azure-delegated-resource-management"></a>Azure delegerade resource Manager

Azure delegerade resource Manager är en av de viktigaste komponenterna i Azure fyr. Med Azure delegerade resurshantering förenkla tjänstleverantörer engagemanget och onboarding kundupplevelser, medan du hanterar delegerade resurser i stor skala med rörlighet och precision.

## <a name="what-is-azure-delegated-resource-management"></a>Vad är Azure delegerad resurshantering?

Azure delegerade resource Manager kan logiska projektion av resurser från en klient till en annan klient. På så sätt kan behöriga användare inom en Azure Active Directory (Azure AD) klient utföra hanteringsåtgärder i olika Azure AD klienter som hör till sina kunder. Tjänsteleverantörer kan logga in på sina egna Azure AD-klient och har behörighet att arbeta i delegerade kundprenumerationer och resursgrupper. På så sätt kan de utföra hanteringsåtgärder åt kunderna, utan att behöva logga in på varje kunds klient.

> [!NOTE]
> Azure delegerade resource Manager kan också användas i ett företag som har flera Azure AD-klienter för att förenkla hantering av flera klienter.

Med Azure delegerade resource Manager behöriga användare kan arbeta direkt i samband med en kundprenumeration utan att ha ett konto i kundens klient eller som en Medägare av kundens klient. De kan också [visa och hantera alla delegerade kundprenumerationer i den nya **mina kunder** sidan](../how-to/view-manage-customers.md) i Azure-portalen.

Den [mellan klientorganisationer hanteringsupplevelse](cross-tenant-management-experience.md) hjälper dig att arbeta mer effektivt med Azure management-tjänster som Azure Policy och Azure Security Center. All aktivitet för service provider spåras i aktivitetsloggen, som lagras i både tjänstleverantörens och kundens klienter. Det innebär att både kund- och service provider lätt kan identifiera användare som är associerade med de eventuella ändringar.

När du publicerar en kund till Azure delegerat resurshantering, de får åtkomst till den nya **onlineleverantörer** sidan på Azure portal, där de kan [bekräfta och hantera sina erbjudanden, leverantörer och delegerad resurser](../how-to/view-manage-service-providers.md). Om kunden vill någonsin återkalla åtkomst för en tjänstleverantör, kan de göra det här när som helst.

Du kan [publicera den nya typen av hanterade tjänster erbjudandet på Azure Marketplace](../how-to/publish-managed-services-offers.md) rätten att enkelt kunderna till Azure resource Manager. Du kan också [slutföra registreringsprocessen genom att distribuera Azure Resource Manager-mallar](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Hur Azure delegerad resurshantering fungerar

På hög nivå är här hur Azure delegerad resource management fungerar:

1. Som en tjänsteleverantör identifiera åtkomst (roller) som dina grupper eller tjänstens huvudnamn användare kommer att behöva hantera kundens Azure-resurser. Åtkomst-definitionen innehåller tjänstleverantörens klient-ID tillsammans med den nödvändiga åtkomsten för erbjudandet, definieras med hjälp av **principalId** identiteter från din klient som mappats till [inbyggda  **roleDefinition** värden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (deltagare, VM-deltagare, läsare, osv.).
2. Du anger den här åtkomsten och publicera kunden till Azure delegeras resurshantering på något av två sätt:
   - [Publicera ett erbjudande för Azure Marketplace-hanterade tjänster](../how-to/publish-managed-services-offers.md) (privat eller offentlig) som kunden accepterar
   - [Distribuera en Azure Resource Manager-mall till kundens klient](../how-to/onboard-customer.md) för en eller flera specifika prenumerationer eller resursgrupper
3. När kunden har integrerats kan behöriga användare logga in på din service provider-klient och utföra administrativa uppgifter i det angivna kund omfånget, baserat på den åtkomst som du har definierat.

## <a name="support-for-azure-delegated-resource-management"></a>Support för Azure delegerad resurshantering

Om du behöver hjälp som rör Azure delegerad resurshantering kan du öppna en supportförfrågan i Azure-portalen. För **typ av problem**, Välj **teknisk**. Välj en prenumeration och välj sedan **delegerad resurshantering** (under **övervakning och hantering**).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](cross-tenant-management-experience.md).
- Lär dig mer om [hanterade tjänster erbjudanden på Azure Marketplace](managed-services-offers.md).