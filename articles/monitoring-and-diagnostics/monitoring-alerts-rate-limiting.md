---
title: Hastighetsbegränsning för SMS, e-postmeddelanden, Azure App push-meddelanden och webhooks
description: Förstå hur Azure begränsar antalet möjliga SMS, e-post, Azure App push eller webhook-meddelande från en åtgärdsgrupp.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 7ac5890769809e3f6f8afe320922facc04195042
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166021"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Hastighetsbegränsning för röst, SMS, e-postmeddelanden, Azure App push-meddelanden och webhook inlägg
Hastighetsbegränsning är en upphävande av meddelanden som uppstår när för många skickas till en viss telefonnummer, e-postadress eller enhet. Hastighetsbegränsning säkerställer att aviseringar är hanterbara och rekommenderade åtgärder.

Tröskelvärden för hastighet gränsen är:

 - **SMS**: Mer än 1 SMS var femte minut.
 - **Röst**: Mer än 1 röstsamtal var femte minut.
 - **e-post**: Mer än 100 e-postmeddelanden på en timme.
 
 Andra åtgärder är inte begränsats.

## <a name="rate-limit-rules"></a>Priset gränsen regler
- Ett visst telefonnummer eller e-postmeddelandet begränsas när den får fler meddelanden än tröskelvärdet tillåter.
- Ett telefonnummer eller e-post kan inte ingå i åtgärdsgrupper för många prenumerationer. Hastighetsbegränsning gäller för alla prenumerationer. Det gäller när tröskelvärdet har uppnåtts, även om meddelanden skickas från flera prenumerationer.
- När en e-postadress är hastighet som är begränsad, skickas ett meddelande om ytterligare kommunicerar den hastighetsbegränsning. E-tillstånd när den hastighetsbegränsning upphör att gälla.

## <a name="next-steps"></a>Nästa steg ##
* Läs mer om [SMS Avisera beteende](monitoring-sms-alert-behavior.md).
* Hämta en [översikt över aktivitetsloggaviseringar](monitoring-overview-alerts.md), och lär dig hur du får aviseringar.  
* Lär dig hur du [konfigurera aviseringar när en avisering om tjänstens hälsa publiceras](../azure-monitor/platform/alerts-activity-log-service-notifications.md).
