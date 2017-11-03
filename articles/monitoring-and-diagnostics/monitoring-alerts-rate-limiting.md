---
title: "Betygsätt begränsande för SMS, e-postmeddelanden och webhooks | Microsoft Docs"
description: "Förstå hur Azure begränsar antalet möjliga SMS, e-post eller webhook meddelanden från en grupp."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Betygsätt begränsande för SMS-meddelanden, e-postmeddelanden och webhook inlägg
Hastighetsbegränsning är en upphävande av meddelanden som uppstår när för många meddelanden skickas till en viss telefonnummer eller e-postadress. Hastighetsbegränsning säkerställer att aviseringar är hanterbara och rekommenderade åtgärder.

Regler för SMS- och e-post är samma. Hastigheten med vilken gränsen tröskelvärdet är:

 - **SMS**: 10 meddelanden i en timme.
 - **E-post**: 100 meddelanden i en timme.

## <a name="rate-limit-rules"></a>Hastigheten med vilken gränsen regler
- Ett visst telefonnummer eller e-post är begränsad när den tar emot fler meddelanden än tröskelvärdet tillåter hastighet.
- Ett telefonnummer eller e-post kan vara en del av åtgärdsgrupper mellan många prenumerationer. Hastighetsbegränsning gäller över alla prenumerationer. Det gäller så snart tröskelvärdet har uppnåtts, även om meddelanden skickas från flera prenumerationer.  
- När ett telefonnummer eller e-post är begränsad hastighet, skickas ett meddelande med ytterligare kommunicera i hastighetsbegränsning. Meddelande tillstånd när den hastighetsbegränsning upphör att gälla.

## <a name="rate-limit-of-webhooks"></a>Hastighetsbegränsning för webhooks ##
Det finns ingen begränsning för webhooks hastighet.

## <a name="next-steps"></a>Nästa steg ##
* Lär dig mer om [SMS Varna beteende](monitoring-sms-alert-behavior.md).
* Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
