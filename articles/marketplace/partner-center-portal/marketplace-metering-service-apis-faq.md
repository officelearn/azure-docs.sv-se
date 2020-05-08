---
title: Vanliga frågor och svar om API för avläsning av tjänst – Microsofts kommersiella marknads platser
description: 'Vanliga frågor om API: er för mätnings tjänsten för SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.'
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857897"
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

Om en användnings händelse inträffar till en dag på en dag, har du till och med 1 PM på nästa dag för att generera en användnings händelse som är associerad med den här händelsen. När system sändnings användningen har en tids gräns, återställs den och sedan skickas användnings händelsen för det tidsintervall då användningen skedde, utan att det går att förlora åter givningen.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Vad händer när du skickar fler än en användnings händelse på samma timme?

Endast en användnings händelse godkänns för Tim intervallet. Tim intervallet börjar vid minut 0 och slutar vid minut 59.  Om fler än en användnings händelse genereras för samma Tim intervall, släpps efterföljande användnings händelser som dubbletter.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Vad händer när du genererar användning för en SaaS-prenumeration som redan har avbrutit prenumerationen?

All användnings händelse som släpps till Marketplace-plattform godkänns inte efter att en SaaS-prenumeration har tagits bort.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kan du hämta en lista över alla SaaS-prenumerationer, inklusive aktiva och avbrutna prenumerationer?

Ja, när du anropar `GET /saas/subscriptions` API: et innehåller en lista över alla SaaS-prenumerationer. Fältet Status i svaret för varje SaaS-prenumeration fångar om prenumerationen är aktiv eller inte prenumererad. Anropet till List-prenumerationer returnerar högst 100 prenumerationer för tillfället.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Vad händer om marknads mätnings tjänsten har ett avbrott?

Om ISV: en skickar en anpassad mätare och tar emot ett fel, ska ISV vänta och sedan försöka igen.

Om felet kvarstår skickar du om den anpassade mätaren nästa timma (ackumulera antalet). Fortsätt med den här processen tills ett icke-felsvar tas emot.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [API: er för Marketplace för avläsning av tjänst](./marketplace-metering-service-apis.md).
