---
title: Hastighetsbegränsning för SMS, e-post, push-meddelanden
description: Förstå hur Azure begränsar antalet möjliga SMS-, e-post-, Azure App-push- eller webhook-meddelanden från en åtgärdsgrupp.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114331"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Hastighetsbegränsning för röst-, SMS-meddelanden, e-postmeddelanden, Azure App push-meddelanden och webhook-inlägg
Hastighetsbegränsning är en avstängning av meddelanden som inträffar när för många skickas till ett visst telefonnummer, e-postadress eller enhet. Hastighetsbegränsning säkerställer att aviseringar är hanterbara och genomförbara.

Tröskelvärdena för hastighetsbegränsningar är:

- **SMS**: Högst 1 SMS var 5:e minut.
- **Röst:** Högst 1 Röstsamtal var femte minut.
- **E-post:** Inte mer än 100 e-postmeddelanden på en timme.
 
  Andra åtgärder är inte räntebegränsade.

## <a name="rate-limit-rules"></a>Regler för hastighetsbegränsning
- Ett visst telefonnummer eller e-postmeddelande är begränsad när det tar emot fler meddelanden än tröskelvärdet tillåter.
- Ett telefonnummer eller e-postmeddelande kan ingå i åtgärdsgrupper i många prenumerationer. Hastighetsbegränsning gäller för alla prenumerationer. Det gäller så snart tröskelvärdet har uppnåtts, även om meddelanden skickas från flera prenumerationer.
- När en e-postadress är begränsad skickas ytterligare ett meddelande för att meddela prisbegränsningen. E-postmeddelandet anger när prisbegränsningen upphör att gälla.

## <a name="next-steps"></a>Nästa steg ##
* Läs mer om [SMS-varningsbeteende](alerts-sms-behavior.md).
* Få en översikt över aviseringar för [aktivitetsloggar](alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett hälsomeddelande för tjänsten publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

