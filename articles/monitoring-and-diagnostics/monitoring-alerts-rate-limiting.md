---
title: Betygsätt begränsande för SMS, e-postmeddelanden, Azure App push-meddelanden och webhooks | Microsoft Docs
description: Förstå hur Azure begränsar antalet möjliga SMS, e-post, Azure App push eller webhook meddelanden från en grupp.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2018
ms.author: dukek
ms.openlocfilehash: 9216a64dbd8201ff09ea5c9283b4db465682a3bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Betygsätt begränsande för röst, SMS, e-postmeddelanden, Azure App push-meddelanden och webhook inlägg
Hastighetsbegränsning är en upphävande av meddelanden som uppstår när för många skickas till ett visst telefonnummer, e-postadress eller enhet. Hastighetsbegränsning säkerställer att aviseringar är hanterbara och rekommenderade åtgärder.

Hastigheten med vilken gränsen tröskelvärden är:

 - **SMS**: fler än 1 SMS var femte minut.
 - **Röst**: fler än 1 röst anropa var femte minut.
 - **E-post**: fler än 100 e-postmeddelanden på en timme.
 
 Andra åtgärder är inte begränsad hastighet.

## <a name="rate-limit-rules"></a>Hastigheten med vilken gränsen regler
- Ett visst telefonnummer eller e-post är begränsad när den tar emot fler meddelanden än tröskelvärdet tillåter hastighet.
- Ett telefonnummer eller e-post kan vara en del av åtgärdsgrupper mellan många prenumerationer. Hastighetsbegränsning gäller över alla prenumerationer. Det gäller så snart tröskelvärdet har uppnåtts, även om meddelanden skickas från flera prenumerationer.
- När en e-postadress är begränsad hastighet, skickas ett meddelande med ytterligare kommunicera i hastighetsbegränsning. E-tillstånd när den hastighetsbegränsning upphör att gälla.

## <a name="next-steps"></a>Nästa steg ##
* Lär dig mer om [SMS Varna beteende](monitoring-sms-alert-behavior.md).
* Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
