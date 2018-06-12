---
title: SMS-avisering beteende i åtgärdsgrupper
description: SMS-meddelandeformat och svara på SMS-meddelanden för att avbryta prenumerationen, prenumerera igen eller be om hjälp.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: f2f463f6c428ce6c72e2640472376fa17a2bfe5a
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263015"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS Varna beteende i åtgärdsgrupper
## <a name="overview"></a>Översikt ##
Åtgärdsgrupper kan du konfigurera en lista med åtgärder. Dessa grupper används när du definierar aviseringar; Se till att en viss grupp meddelas när en avisering utlöses. En av de åtgärder som stöds är SMS; SMS-meddelanden stöder dubbelriktad kommunikation. En användare kan svara på ett SMS till:

- **Prenumerationen aviseringar:** en användare kan avbryta prenumerationen på alla SMS-aviseringar för alla åtgärdsgrupper som eller en enda åtgärdsgrupp.
- **Prenumerera igen på varningar:** en användare kan prenumerera igen till alla SMS-aviseringar för alla åtgärdsgrupper som eller en enda åtgärdsgrupp.  
- **Be om hjälp:** en användare kan begära mer information om SMS. Du omdirigeras till den här artikeln.

Den här artikeln beskriver beteendet för SMS-aviseringar och svar åtgärder användaren kan utföra baserat på de nationella inställningarna för användaren:

## <a name="receiving-an-sms-alert"></a>Ta emot en SMS-avisering
En SMS-mottagare som konfigurerats som en del av en grupp får ett SMS när en avisering utlöses. SMS innehåller följande information:
* Kort filnamn i åtgärdsgruppen aviseringen har skickats till
- Rubrik på aviseringen

| SVARA | Beskrivning |
| ----- | ----------- |
| INAKTIVERA <Action Group Short name> | Inaktiverar ytterligare SMS från gruppen åtgärd |
| AKTIVERA <Action Group Short name> | Återaktiverar SMS från gruppen åtgärd |
| STANNA | Inaktiverar ytterligare SMS från alla grupper i åtgärd |
| STARTA | Återaktiverar SMS från alla åtgärdsgrupper |
| HJÄLP | Ett svar skickas till användaren med en länk till den här artikeln. |

>[!NOTE]
>Om en användare har avbrutit från SMS aviseringar, men läggs sedan till en ny åtgärdsgrupp; de ska ta emot SMS-aviseringar för den nya gruppen åtgärd, men förblir avbryta prenumerationen på alla åtgärdsgrupper för föregående.

## <a name="next-steps"></a>Nästa steg
Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md) och lär dig att få aviseringar  
Lär dig mer om [SMS hastighetsbegränsning](monitoring-alerts-rate-limiting.md)  
Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)
