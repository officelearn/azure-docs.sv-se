---
title: Vad är Azure Lighthouse?
description: Med Azure Lighthouse kan tjänsteleverantörer leverera hanterade tjänster för sina kunder med högre automatisering och effektivitet i stor skala.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646202"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure Lighthouse?

Azure Lighthouse erbjuder tjänsteleverantörer ett enda kontrollplan för att visa och hantera Azure för alla sina kunder med högre automatisering, skala och förbättrad styrning. Med Azure Lighthouse kan tjänsteleverantörer leverera hanterade tjänster med hjälp av omfattande och robusta hanteringsverktyg som är inbyggda i Azure-plattformen. Det här erbjudandet kan också gynna FÖRETAGETS IT-organisationer som hanterar resurser för flera klienter.

![Översiktsdiagram över Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure Lighthouse hjälper dig att på ett lönsamt och effektivt sätt skapa och leverera hanterade tjänster för dina kunder. Fördelarna innefattar:

- **Hantering i stor skala**: Kundengagemang och livscykelåtgärder för att hantera kundresurser är enklare och mer skalbara.
- **Större synlighet och precision för kunder:** Kunder vars resurser du hanterar får större insyn i dina åtgärder och exakt kontroll över omfattningen som de delegerar för hantering, medan din IP bevaras.
- **Omfattande och enhetliga plattformsverktyg: Vår verktygsupplevelse**behandlar viktiga tjänsteleverantörer, inklusive flera licensieringsmodeller som EA, CSP och användningsbaserad betalning. De nya funktionerna fungerar med befintliga verktyg och API:er, licensieringsmodeller och partnerprogram som [CSP (Cloud Solution Provider program).](https://docs.microsoft.com/partner-center/csp-overview) De Azure Lighthouse-alternativ du väljer kan integreras i dina befintliga arbetsflöden och program, och du kan spåra din inverkan på kundengagemang genom [att länka ditt partner-ID](../billing/billing-partner-admin-link-started.md).

Det finns inga extra kostnader för att använda Azure Lighthouse för att hantera dina kunders Azure-resurser.

## <a name="capabilities"></a>Funktioner

Azure Lighthouse innehåller flera sätt att effektivisera kundengagemang och hantering:

- **Azure delegerad resurshantering:** Hantera dina kunders Azure-resurser säkert inifrån din egen klientorganisation, utan att behöva byta kontext och styra plan. Mer information finns i [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- **Nya Azure-portalupplevelser:** Visa information över flera innehavare på den nya sidan **Mina kunder** i [Azure-portalen](https://portal.azure.com). Med ett motsvarande **serviceleverantörsblad** kan dina kunder visa och hantera tjänsteleverantören åtkomst. Mer information finns i [Visa och hantera kunder](./how-to/view-manage-customers.md) och Visa och hantera [tjänsteleverantörer](how-to/view-manage-service-providers.md).
- **Azure Resource Manager-mallar**: Utför hanteringsuppgifter enklare, inklusive introduktionskunder för Azure-delegerad resurshantering. Mer information finns i våra [exempel repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) och [ombord på en kund till Azure delegerad resurshantering](how-to/onboard-customer.md).
- **Managed Services erbjuder i Azure Marketplace:** Erbjud dina tjänster till kunder via privata eller offentliga erbjudanden och få dem automatiskt inbyggda i Azure-delegerad resurshantering, som ett alternativ till introduktion med Azure Resource Manager-mallar. Mer information finns [i Erbjudanden om hanterade tjänster på Azure Marketplace](concepts/managed-services-offers.md).
- **Azure-hanterade program:** Paketera och leverera program som är enkla för dina kunder att distribuera och använda i sina egna prenumerationer. Programmet distribueras till en resursgrupp som du har åtkomst till från din klientorganisation, så att du kan hantera tjänsten som en del av den övergripande Azure Lighthouse-upplevelsen. Mer information finns i [Översikt över Hanterade Azure-program](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- Lär dig mer om [hanteringsupplevelser mellan klienter.](concepts/cross-tenant-management-experience.md)
