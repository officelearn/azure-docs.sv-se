---
title: Beteende för SMS-aviseringar i åtgärdsgrupper
description: SMS-meddelandeformat och svara på SMS-meddelanden för att avbryta prenumerationen, återprenumeration eller be om hjälp.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 481340dbab6a2ae7247a53cb78b17f3353edcd00
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346554"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS-avisering beteende i åtgärdsgrupper
## <a name="overview"></a>Översikt ##
Åtgärdsgrupper kan du konfigurera en lista med åtgärder. Dessa grupper används när du definierar aviseringar; Se till att en viss åtgärdsgrupp meddelas när en avisering utlöses. En av de åtgärder som stöds är SMS; SMS-meddelanden stöd för dubbelriktad kommunikation. En användare kan svara på ett SMS till:

- **Slutar prenumerera på aviseringar:** En användare kan avbryta prenumerationen på alla SMS-aviseringar för alla åtgärdsgrupper eller en enskild åtgärdsgrupp.
- **Återprenumeration aviseringar:** En användare kan prenumerera igen till alla SMS-aviseringar för alla åtgärdsgrupper eller en enskild åtgärdsgrupp.  
- **Be om hjälp:** En användare kan begära mer information om SMS. Du omdirigeras till den här artikeln.

Den här artikeln beskriver hur SMS-aviseringar och svarsåtgärder användaren kan utföras baserat på de nationella inställningarna för användaren:

## <a name="receiving-an-sms-alert"></a>Ta emot en SMS-avisering
En SMS-mottagare som konfigurerats som en del av en åtgärdsgrupp tar emot ett SMS när en avisering utlöses. SMS innehåller följande information:
* Kortnamn för den här aviseringen har skickats till åtgärdsgruppen
- Aviseringens titel

| SVARA | Beskrivning |
| ----- | ----------- |
| INAKTIVERA <Action Group Short name> | Inaktiverar ytterligare SMS från åtgärdsgruppen |
| AKTIVERA <Action Group Short name> | Återaktiverar SMS från åtgärdsgruppen |
| STANNA | Inaktiverar ytterligare SMS från alla åtgärdsgrupper |
| STARTA | Återaktiverar SMS från alla åtgärdsgrupper |
| HJÄLP | Ett svar skickas till användaren med en länk till den här artikeln. |

>[!NOTE]
>Om en användare har avbrutit SMS aviseringar, men läggs sedan till en ny åtgärdsgrupp; de ska ta emot SMS-aviseringar för den nya åtgärdsgruppen, men förblir avbryta prenumerationen på alla föregående åtgärdsgrupper.

## <a name="next-steps"></a>Nästa steg
Hämta en [översikt över aktivitetsloggaviseringar](alerts-overview.md) och lär dig att få varningar om  
Läs mer om [SMS hastighetsbegränsning](alerts-rate-limiting.md)  
Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md)
