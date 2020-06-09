---
title: Avsnitt om Azure Event Grid-partner
description: Skicka händelser från tredjeparts Event Grid SaaS-och PaaS-partner direkt till Azure-tjänster med Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560278"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Partner ämnen i Azure Event Grid (för hands version)
Med hjälp av partner ämnen kan du ansluta tredjeparts händelse källor direkt till Azure Event Grid. Den här integrationen gör att du kan prenumerera på händelser från partner på samma sätt som du prenumererar på händelser från Azure-tjänster. 

## <a name="available-partners"></a>Tillgängliga partner
Den första partner som är tillgänglig via Event Grid partner ämnen är Auth0. Du kan använda [Auth0-partner ämnet](auth0-overview.md) för att ansluta dina Auth0-och Azure-konton. Integrationen gör att du kan reagera på, logga och övervaka Auth0-händelser i real tid.

[Om du vill testa det](auth0-how-to.md)loggar du in på ditt Auth0-konto och skapar en Event Grid-integration. När du har valt **skapa** i Auth0 visas ett väntande Auth0-ämne i ditt Azure-konto. Välj **Aktivera**och du kan skapa Event Grid prenumerationer för att dirigera, filtrera och leverera dina händelser precis som du gör med andra händelse källor.

## <a name="pricing"></a>Prissättning
Partner ämnen debiteras enligt samma åtgärds takt som system ämnen.

## <a name="limits"></a>Gränser
Partner ämnena finns i offentlig för hands version. Under den offentliga för hands versionen omfattas partner ämnen av [samma gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) som system ämnen och anpassade ämnen.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hur gör jag för att bli en Event Grid-partner?
Den infrastruktur som skapats för att stödja den här lanseringen gör det enkelt och snabbt för nya partners att integrera sina händelse funktioner med Event Grid. Mer information finns i dokumentationen om [partner onboarding](partner-onboarding-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Auth0-partner ämne](auth0-overview.md)
- [Så här använder du Auth0-partner ämnet](auth0-how-to.md)
- [Bli en Event Grid-partner](partner-onboarding-overview.md)