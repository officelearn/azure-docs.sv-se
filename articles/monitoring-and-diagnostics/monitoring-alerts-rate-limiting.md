---
title: "Betygsätt begränsande för SMS, e-postmeddelanden, Azure App push-meddelanden och webhooks | Microsoft Docs"
description: "Förstå hur Azure begränsar antalet möjliga SMS, e-post, Azure App push eller webhook meddelanden från en grupp."
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Betygsätt begränsande för SMS-meddelanden, e-postmeddelanden, Azure App push-meddelanden och webhook inlägg
Hastighetsbegränsning är en upphävande av meddelanden som uppstår när för många meddelanden skickas till ett visst telefonnummer, e-postadress eller enhet. Hastighetsbegränsning säkerställer att aviseringar är hanterbara och rekommenderade åtgärder.

Hastigheten med vilken gränsen tröskelvärden är:

 - **SMS**: fler än 1 SMS var femte minut.
 - **E-post**: 100 meddelanden i en timme.
 - **Azure App Push-meddelanden**: det finns inga hastighet begränsande för push-meddelanden.
 - **Webhooks**: det finns ingen begränsning för webhooks hastighet.

## <a name="rate-limit-rules"></a>Hastigheten med vilken gränsen regler
- Ett visst telefonnummer eller e-post är begränsad när den tar emot fler meddelanden än tröskelvärdet tillåter hastighet.
- Ett telefonnummer eller e-post kan vara en del av åtgärdsgrupper mellan många prenumerationer. Hastighetsbegränsning gäller över alla prenumerationer. Det gäller så snart tröskelvärdet har uppnåtts, även om meddelanden skickas från flera prenumerationer.  
- När en e-postadress är begränsad hastighet, skickas ett meddelande med ytterligare kommunicera i hastighetsbegränsning. Meddelande tillstånd när den hastighetsbegränsning upphör att gälla.

## <a name="next-steps"></a>Nästa steg ##
* Lär dig mer om [SMS Varna beteende](monitoring-sms-alert-behavior.md).
* Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
