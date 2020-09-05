---
title: Vad är Azure Lighthouse?
description: Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster för sina kunder med högre automatisering och effektivitet i stor skala.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 22bec7ec8944a11ce0cfdf51776f1f193a1aedaa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488821"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure Lighthouse?

Med Azure Lighthouse kan du hantera flera klientorganisationer, vilket ger högre automatisering, skalbarhet och förbättrad styrning av olika resurser och klientorganisationer. Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster med hjälp av omfattande och robusta hanterings verktyg som är inbyggda i Azure-plattformen. Det här erbjudandet kan också dra nytta av företagets IT-organisationer och hantera resurser över flera klienter.

![Översikts diagram över Azure-Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure Lighthouse hjälper dig att effektivt och effektivt bygga och leverera hanterade tjänster. Fördelarna innefattar:

- **Hantering i skala**: kund engagemang och livs cykel åtgärder för att hantera kund resurser är enklare och mer skalbar. Befintliga API: er, hanterings verktyg och arbets flöden kan användas med delegerade resurser, oavsett i vilka regioner de är placerade.
- **Bättre synlighet och precision för kunder**: kunderna får bättre insyn i dina åtgärder och precis kontroll över omfattningen som de delegerar för hantering, inklusive möjligheten att ta bort åtkomsten helt medan IP-adressen bevaras.
- **Omfattande och enhetlig plattforms verktyg**: vår verktygs upplevelse behandlar scenarier för nyckel tjänst leverantörer, inklusive flera licensierings modeller som EA, CSP och betala per användning. De nya funktionerna fungerar med befintliga verktyg och API: er, licensierings modeller och partner program som [Cloud Solution Provider-program (CSP)](/partner-center/csp-overview). Azure Lighthouse kan integreras i dina befintliga arbets flöden och program, och du kan spåra din påverkan på kund engagemang och få partner intjänad kredit genom [att länka ditt partner-ID](./how-to/partner-earned-credit.md).

Det finns inga ytterligare kostnader för att använda Azure-Lighthouse för att hantera Azure-resurser. Alla Azure-kunder och-partner kan använda Azure-Lighthouse.

## <a name="capabilities"></a>Funktioner

Azure Lighthouse innehåller flera sätt att effektivisera engagemang och hantering:

- **Azure-delegerad resurs hantering**: hantera dina kunders Azure-resurser på ett säkert sätt i din klient, utan att behöva byta kontext-och kontroll plan. Prenumerationer och resurs grupper kan delegeras till angivna användare och roller i hanterings klienten, med möjlighet att ta bort åtkomst vid behov. Mer information finns i [Azure delegerad resurs hantering](concepts/azure-delegated-resource-management.md).
- **Nya Azure Portal upplevelser**: Visa information om flera innehavare på sidan [ **Mina kunder** ](./how-to/view-manage-customers.md) i Azure Portal. På sidan [ **tjänst leverantörer** ](how-to/view-manage-service-providers.md) kan kunder se och hantera sin tjänst leverantörs åtkomst.
- **Azure Resource Manager mallar**: våra mallar hjälper dig att utföra hanterings uppgifter för flera innehavare och publicera delegerade kund resurser. Mer information finns i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) och [publicera en kund på Azure Lighthouse](how-to/onboard-customer.md).
- **Hanterade tjänst erbjudanden i Azure Marketplace**: erbjud dina tjänster till kunder via privata eller offentliga erbjudanden och få dem automatiskt att registreras i Azure-Lighthouse. Mer information finns i [hanterade tjänst erbjudanden i Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- Utforska [hanterings upplevelser mellan flera innehavare](concepts/cross-tenant-management-experience.md).
- Se hur du [använder Azure Lighthouse i ett företag](concepts/enterprise.md).
