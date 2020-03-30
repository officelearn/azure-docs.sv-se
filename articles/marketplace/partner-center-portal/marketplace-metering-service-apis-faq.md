---
title: Api:er för marknadstjänster för marknadsplatsmätning – vanliga frågor och svar | Azure Marketplace
description: Avge användning av ett SaaS-erbjudande på Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275789"
---
# <a name="marketplace-metering-service-apis---faq"></a>API:er för Marketplace Metering Service – vanliga frågor och svar

När en Azure-användare prenumererar på en SaaS-tjänst som innehåller fakturering med datapriser spårar du förbrukning för varje faktureringsdimension som används av kunden. Om förbrukningen överskrider de inkluderade kvantiteter som angetts för den term som kunden har valt, kommer tjänsten att skicka ut användningshändelser till Microsoft.

## <a name="emit-usage-events"></a>Avge användningshändelser

>[!Note]
>Detta avsnitt gäller endast för SaaS-erbjudanden, där minst en av planerna har mättjänstdimensioner definierade vid tidpunkten för publiceringen av erbjudandet.

![Avge användningshändelser](media/isv-emits-usage-event.png)

Se [SaaS-batchanvändningshändelse-API:et](./marketplace-metering-service-apis.md#batch-usage-event) för information om API-kontraktet för avgivande av användningshändelser.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hur ofta förväntas det att avge användning?

Helst förväntas du avge användning varje timme under den senaste timmen, endast om det finns användning i föregående timme.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Vad är den maximala fördröjningen mellan den tidpunkt då en händelse inträffar och den tidpunkt då en användningshändelse avges till Microsoft?

Helst avges användningshändelse varje timme för händelser som inträffat under den senaste timmen. Förseningar väntas dock. Den maximala tillåtna fördröjningen är 24 timmar, varefter användningshändelser inte accepteras.

Om till exempel en användningshändelse inträffar klockan 13.00 på en dag har du fram till 13.00 nästa dag på dig att avge en användningshändelse som är associerad med den här händelsen. Detta innebär att när det gäller systemet avger användning har en ner tid, kan det återställa och sedan skicka användningshändelsen för timintervallet där användningen inträffade, utan förlust av trohet.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Vad händer när du skickar mer än en användningshändelse samma timme?

Endast en användningshändelse accepteras för timintervallet. Timintervallet börjar minut 0 och slutar minut 59.  Om mer än en användningshändelse avges för samma timintervall, tas alla efterföljande användningshändelser bort som dubbletter.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Vad händer när du avger användning för en SaaS-prenumeration som redan har avslutat prenumerationen?

Alla användningshändelseer som släpps ut till marknadsplatsplattformen accepteras inte när en SaaS-prenumeration har tagits bort.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kan du få en lista över alla SaaS-prenumerationer, inklusive aktiva och avprenumerade prenumerationer?

Ja, när du `GET /saas/subscriptions` anropar API:et innehåller det en lista över alla SaaS-prenumerationer. Statusfältet i svaret för varje SaaS-prenumeration fångar om prenumerationen är aktiv eller avsluta prenumerationen. Anropet till listan Prenumerationer returnerar högst 100 prenumerationer vid den tidpunkten.

## <a name="next-steps"></a>Nästa steg

- Mer information [finns i API:er](./marketplace-metering-service-apis.md) för tjänsten Marketplace för mer information.
