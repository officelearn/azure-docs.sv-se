---
title: Avsnitt om Azure Event Grid-partner
description: Skicka händelser från tredjeparts Event Grid SaaS-och PaaS-partner direkt till Azure-tjänster med Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691126"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Partner ämnen i Azure Event Grid (för hands version)
Med partner ämnen kan du ansluta händelse källor från tredje part direkt till Event Grid. Den här integrationen gör att du kan prenumerera på händelser från partner på samma sätt som du prenumererar på händelser från Azure-tjänster. 

## <a name="available-partners"></a>Tillgängliga partner
Den första partner som är tillgänglig via Event Grid partner ämnen är Auth0. I [avsnittet Auth0-partner](auth0-overview.md) kan du ansluta dina Auth0-och Azure-konton. Integrationen att reagera på, logga och övervaka Auth0-händelser i real tid.

[Prova](auth0-how-to.md) idag genom att logga in på ditt Auth0-konto och skapa en Event Grid-integration. När du klickar på Skapa i Auth0 visas ett väntande Auth0-ämne i ditt Azure-konto. Klicka på Aktivera och du kan skapa händelse prenumerationer, dirigera, filtrera och leverera dina händelser precis som du gör med andra händelse källor.

## <a name="pricing"></a>Priser
Partner ämnen debiteras enligt samma åtgärds takt som system ämnen.

## <a name="limits"></a>Gränser
Partner ämnena finns i offentlig för hands version. Under den offentliga för hands versionen omfattas partner ämnen av [samma gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) som system ämnen och anpassade ämnen.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hur gör jag för att bli en Event Grid-partner?
Den infrastruktur som skapats för att stödja den här lanseringen gör det enkelt och snabbt för nya partners att integrera sina händelse funktioner med Event Grid. Läs [dokumentationen om partner onboarding](partner-onboarding-overview.md) för mer information.

## <a name="next-steps"></a>Nästa steg

- [Auth0-partner ämne](auth0-overview.md)
- [Så här använder du Auth0-partner ämnet](auth0-how-to.md)
- [Bli en Event Grid-partner](partner-onboarding-overview.md)