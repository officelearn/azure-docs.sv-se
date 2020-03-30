---
title: SMS-varningsbeteende i åtgärdsgrupper
description: SMS-meddelandeformat och svara på SMS-meddelanden för att avsluta prenumerationen, prenumerera på nytt eller begära hjälp.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665314"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS-varningsbeteende i åtgärdsgrupper

## <a name="overview"></a>Översikt 
Med åtgärdsgrupper kan du konfigurera en lista över åtgärder. Dessa grupper används när aviseringar definieras. se till att en viss åtgärdsgrupp meddelas när aviseringen utlöses. En av de åtgärder som stöds är SMS; SMS-meddelanden stöder dubbelriktad kommunikation. En användare kan svara på ett SMS för att:

- **Avsluta prenumerationen på aviseringar:** En användare kan avsluta prenumerationen på alla SMS-aviseringar för alla åtgärdsgrupper eller en enda åtgärdsgrupp.
- **Prenumerera på aviseringar igen:** En användare kan prenumerera på alla SMS-aviseringar för alla åtgärdsgrupper eller en enda åtgärdsgrupp.  
- **Be om hjälp:** En användare kan be om mer information om SMS. De omdirigeras till den här artikeln.

Den här artikeln beskriver hur SMS-aviseringar och svarsåtgärder som användaren kan vidta baserat på användarens språk:

## <a name="receiving-an-sms-alert"></a>Ta emot en SMS-varning
En SMS-mottagare som konfigurerats som en del av en åtgärdsgrupp får ett SMS när en avisering utlöses. SMS:et innehåller följande information:
* Kortnamn för den åtgärdsgrupp som den här aviseringen skickades till
* Registreringens namn

| REPLY | Beskrivning |
| ----- | ----------- |
| Inaktivera`<Action Group Short name>` | Inaktiverar ytterligare SMS från åtgärdsgruppen |
| Aktivera`<Action Group Short name>` | Återaktiverar SMS från åtgärdsgruppen |
| Stanna | Inaktiverar ytterligare SMS från alla åtgärdsgrupper |
| Börja | Återaktiverar SMS från ALLA åtgärdsgrupper |
| HJÄLP | Ett svar skickas till användaren med en länk till den här artikeln. |

>[!NOTE]
>Om en användare har avslutat prenumerationen på SMS-aviseringar, men sedan läggs till i en ny åtgärdsgrupp. de kommer att få SMS-aviseringar för den nya åtgärdsgruppen, men förblir avprenumererade från alla tidigare åtgärdsgrupper.

## <a name="next-steps"></a>Efterföljande moment
Få en [översikt över aktivitetsloggaviseringar](alerts-overview.md) och lär dig hur du får aviseringar  
Läs mer om [begränsning av SMS-hastighet](alerts-rate-limiting.md)  
Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md)

