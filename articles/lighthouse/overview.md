---
title: Vad är Azure Lighthouse?
description: Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster för sina kunder med högre automatisering och effektivitet i stor skala.
ms.date: 05/28/2020
ms.topic: overview
ms.openlocfilehash: 27f99cb398294d570b2d91131fcf40dd0648e981
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219061"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure Lighthouse?

Azure Lighthouse erbjuder tjänste leverantörer ett enda kontroll plan för att visa och hantera Azure i alla sina kunder med högre automatisering, skalbarhet och förbättrad styrning. Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster med hjälp av omfattande och robusta hanterings verktyg som är inbyggda i Azure-plattformen. Det här erbjudandet kan också dra nytta av företagets IT-organisationer och hantera resurser över flera klienter.

![Översikts diagram över Azure-Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure Lighthouse hjälper dig att effektivt skapa och leverera hanterade tjänster för dina kunder. Fördelarna innefattar:

- **Hantering i skala**: kund engagemang och livs cykel åtgärder för att hantera kund resurser är enklare och mer skalbar. Befintliga API: er, hanterings verktyg och arbets flöden kan användas med delegerade kund resurser, oavsett vilka regioner de befinner sig i.
- **Bättre synlighet och precision för kunder**: kunderna får bättre insyn i dina åtgärder och precis kontroll över omfattningen som de delegerar för hantering, inklusive möjligheten att ta bort åtkomsten helt medan IP-adressen bevaras.
- **Omfattande och enhetlig plattforms verktyg**: vår verktygs upplevelse behandlar scenarier för nyckel tjänst leverantörer, inklusive flera licensierings modeller som EA, CSP och betala per användning. De nya funktionerna fungerar med befintliga verktyg och API: er, licensierings modeller och partner program som [Cloud Solution Provider-program (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Azure Lighthouse kan integreras i dina befintliga arbets flöden och program, och du kan spåra din påverkan på kund engagemang genom [att länka ditt partner-ID](../billing/billing-partner-admin-link-started.md).

Det finns inga ytterligare kostnader för att använda Azure-Lighthouse för att hantera kundernas Azure-resurser. Alla Azure-kunder och-partner kan använda Azure-Lighthouse.

## <a name="capabilities"></a>Funktioner

Azure Lighthouse innehåller flera sätt att effektivisera kund engagemang och-hantering:

- **Azure-delegerad resurs hantering**: hantera dina kunders Azure-resurser på ett säkert sätt i din klient, utan att behöva byta kontext-och kontroll plan. Prenumerationer och resurs grupper kan delegeras till angivna användare och roller i hanterings klienten, med möjlighet att ta bort åtkomst vid behov. Mer information finns i [Azure delegerad resurs hantering](concepts/azure-delegated-resource-management.md).
- **Nya Azure Portal upplevelser**: Visa information om flera innehavare på sidan nya **mina kunder** i [Azure Portal](https://portal.azure.com). På ett motsvarande **Service Provider-** blad kan dina kunder se och hantera åtkomst till tjänst leverantören. Mer information finns i [Visa och hantera kunder](./how-to/view-manage-customers.md) och [Visa och hantera tjänst leverantörer](how-to/view-manage-service-providers.md).
- **Azure Resource Manager mallar**: utför hanterings uppgifter enklare, inklusive onboarding-kunder för Azure-delegerad resurs hantering. Mer information finns i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) och [publicera en kund till Azure-delegerad resurs hantering](how-to/onboard-customer.md).
- **Hanterade tjänst erbjudanden i Azure Marketplace**: erbjud dina tjänster till kunder via privata eller offentliga erbjudanden och låt dem automatiskt registreras i Azure delegerad resurs hantering, som ett alternativ till att registrera sig med hjälp av Azure Resource Manager mallar. Mer information finns i [hanterade tjänst erbjudanden i Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](concepts/cross-tenant-management-experience.md).
