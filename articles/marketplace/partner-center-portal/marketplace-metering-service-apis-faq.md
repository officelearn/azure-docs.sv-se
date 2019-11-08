---
title: 'API: er för Marketplace avläsning av tjänst – vanliga frågor och svar | Azure Marketplace'
description: Generera användning av ett SaaS-erbjudande på Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: c4f51adbcaa5e5b750169f53a1333544365fd4f3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825500"
---
# <a name="marketplace-metering-service-apis---faq"></a>API:er för Marketplace Metering Service – vanliga frågor och svar

När en Azure-användare prenumererar på en SaaS-tjänst som inkluderar mätning av mätare spårar du förbrukningen för varje fakturerings dimension som används av kunden. Om förbrukningen överskrider den inkluderade mängden som har angetts för den period som kunden har valt, kommer tjänsten att generera användnings händelser till Microsoft.

## <a name="emit-usage-events"></a>Genererar användnings händelser

>[!Note]
>Det här avsnittet gäller endast för SaaS-erbjudanden, där minst ett av planerna har mått för tjänste dimensioner som definieras vid tidpunkten för publiceringen av erbjudandet.

![Genererar användnings händelser](media/isv-emits-usage-event.png)

Se [händelse-API: et för batch-SaaS](./marketplace-metering-service-apis.md#batch-usage-event) för information om API-kontraktet för sändning av användnings händelser.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hur ofta förväntas att kunna generera användning?

Vi rekommenderar att du genererar användning varje timme den senaste timmen, bara om det finns användning i föregående timme.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Vad är den maximala fördröjningen mellan tiden en händelse inträffar och hur länge en användnings händelse skickas till Microsoft?

Vi rekommenderar att användnings händelsen genereras varje timme för händelser som inträffat under den senaste timmen. Fördröjningar förväntas dock. Högsta tillåtna fördröjning är 24 timmar, efter vilken användnings händelser inte kommer att godkännas.

Om en användnings händelse inträffar till en dag på en dag, har du till och med 1 PM på nästa dag för att generera en användnings händelse som är associerad med den här händelsen. Det innebär att den system som avger användningen har en drifts tid, kan återställas och sedan skicka användnings händelsen för det tidsintervall då användningen skedde, utan att du förlorar åter givningen.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Vad händer när du skickar fler än en användnings händelse på samma timme?

Endast en användnings händelse godkänns för Tim intervallet. Tim intervallet börjar vid minut 0 och slutar vid minut 59.  Om fler än en användnings händelse genereras för samma Tim intervall, släpps efterföljande användnings händelser som dubbletter.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Vad händer när du genererar användning för en SaaS-prenumeration som redan har avbrutit prenumerationen?

All användnings händelse som släpps till Marketplace-plattform godkänns inte efter att en SaaS-prenumeration har tagits bort.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kan du hämta en lista över alla SaaS-prenumerationer, inklusive aktiva och avbrutna prenumerationer?

Ja, när du anropar `GET /saas/subscriptions` API innehåller en lista över alla SaaS-prenumerationer. Fältet Status i svaret för varje SaaS-prenumeration fångar om prenumerationen är aktiv eller inte prenumererad. Anropet till List-prenumerationer returnerar högst 100 prenumerationer för tillfället.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [API: er för API för avläsning av tjänst](./marketplace-metering-service-apis.md) .
