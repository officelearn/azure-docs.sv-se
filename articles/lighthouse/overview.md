---
title: Vad är Azure fyr?
description: Azure fyr kan tjänsteleverantörer leverera hanterade tjänster för sina kunder med högre automation och effektivitet i stor skala.
author: JnHs
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: eb55af5a1121eb193bb76efc9f9e0b833f4b5a1f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809822"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure fyr?

Azure fyr erbjuder tjänstleverantörer en enda kontrollplanet för att visa och hantera Azure i sina kunder med högre automation, skala och förbättrad styrning. Med Azure fyr kan tjänsteleverantörer leverera hanterade tjänster med omfattande och robust hanteringsverktyg som är inbyggda i Azure-plattformen. Det här erbjudandet kan också dra företagens IT organisationer som hanterar resurser över flera klienter.

![Översiktsdiagram över i Azure fyr](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure fyr hjälper dig att större lönsamhet och effektivt sätt skapa och leverera hanterade tjänster för dina kunder. Fördelarna innefattar:

- **Hantering i större skala**: Customer engagement och livscykeln för är att hantera kundresurser enklare och mer skalbart.
- **Bättre synlighet och precision för kunder**: Kunder vars resurser som du hanterar har förbättrad insyn i dina åtgärder och exakt kontroll över vilka de delegera för hantering, samtidigt som din IP-adress har bevarats.
- **Omfattande och enhetlig plattform verktyg**: Vår erfarenhet adresser nyckel providern scenarier, till exempel flera licensieringsmodellerna som EA, CSP och betala per användning. De nya funktionerna fungerar med befintliga verktyg och API: er, licensiering modeller, och partner program som den [programmet Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Azure fyr-alternativ som du väljer kan integreras i dina befintliga arbetsflöden och program och du kan spåra din inverkan på uppdrag av [länka ditt partner-ID](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Det finns inga ytterligare kostnader i samband med för att hantera dina kunders Azure-resurser med hjälp av Azure fyr.

## <a name="capabilities"></a>Funktioner

Azure fyr innehåller flera olika sätt för att förenkla Kundengagemang och hantering:

- **Azure delegerad resurshantering**: Hantera dina kunders Azure-resurser på ett säkert sätt från din egen klient utan att behöva växla kontext och kontroll plan. Mer information finns i [Azure delegerad resurshantering](./concepts/azure-delegated-resource-management.md).
- **Nya Azure-portalen inträffar**: Visa information för flera klienter i den nya **mina kunder** sidan i den [Azure-portalen](https://portal.azure.com). En motsvarande **tjänsteleverantörer** bladet kan kunderna visa och hantera tjänsten providerns åtkomst. Mer information finns i [visa och hantera kunder](./how-to/view-manage-customers.md) och [visa och hantera leverantörer](./how-to/view-manage-service-providers.md).
- **Azure Resource Manager-mallar**: Utföra hanteringsåtgärder enklare, inklusive registrering av kunder för Azure delegerade resource Manager. Mer information finns i vår [exempel lagringsplatsen](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) och [publicerat en kund till Azure delegerad resurshantering](how-to/onboard-customer.md).
- **Hanterade tjänster erbjudanden på Azure Marketplace**: Erbjuda dina tjänster till kunder via privata eller offentliga erbjudanden och ha dem automatiskt har integrerats i Azure delegerade resource Manager, som ett alternativ till registrering med Azure Resource Manager-mallar. Mer information finns i [hanterade tjänster erbjudanden på Azure Marketplace](./concepts/managed-services-offers.md).
- **Azure-hanterade program**: Paket- och leverera program som är lättare för kunderna att distribuera och använda i sina egna prenumerationer. Programmet har distribuerats i en resursgrupp som du komma åt från din klient så att du kan hantera tjänsten som en del av den övergripande Azure fyr-upplevelsen. Mer information finns i [Azure hanterade program, översikt](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> De funktioner som beskrivs ovan är för närvarande tillgängliga i offentliga moln. Regional tillgänglighet av enskilda tjänster finns [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).