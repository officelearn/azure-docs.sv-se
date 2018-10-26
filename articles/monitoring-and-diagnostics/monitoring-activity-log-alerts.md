---
title: Aviseringar för aktivitetsloggar i Azure Monitor
description: Ett SMS, webhook, SMS, e-post och mer, när vissa händelser i aktivitetsloggen.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6f007ca3aacb338c14bf481ee58407596c8290ad
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091631"
---
# <a name="alerts-on-activity-log"></a>Aviseringar för aktivitetsloggen 

## <a name="overview"></a>Översikt
Aktivitetsloggsaviseringar är aviseringar som aktiverar när en ny händelse i aktivitetsloggen inträffar som matchar de villkor som anges i aviseringen. De är Azure-resurser, så att de kan skapas med hjälp av en Azure Resource Manager-mall. De kan också vara skapas, uppdateras eller tas bort i Azure-portalen. Den här artikeln beskrivs koncepten bakom aktivitetsloggaviseringar. Den sedan visar hur du använder Azure-portalen för att ställa in en avisering på händelser i aktivitetsloggen. Mer information om användning finns i [skapa och hantera aktivitetsloggaviseringar](alert-activity-log.md).

> [!NOTE]
> Aviseringar **kan inte** skapas för händelser i aviseringskategori av aktivitetsloggen

Normalt kan du skapa aviseringar för aktivitetsloggen kan ta emot meddelanden när:

* Specifika åtgärder inträffar på resurser i din Azure-prenumeration som omfattar ofta enskilda resursgrupper eller resurser. Du kanske exempelvis vill meddelas när alla virtuella datorer i myProductionResourceGroup tas bort. Eller så kanske du vill få ett meddelande om eventuella nya roller har tilldelats en användare i din prenumeration.
* En service health-händelse inträffar. Service health-händelser innehåller aviseringar incidenter och underhållshändelser som gäller för resurser i din prenumeration.

En enkel liknelse för förstå villkor som Varningsregler kan skapas på aktivitetslogg, är att utforska eller Filtrera händelser via [aktivitetsloggen i Azure Portal](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). I Azure Monitor - aktivitetslogg, en filtrera eller hitta nödvändiga evenemang och sedan skapa en avisering med hjälp av den **Lägg till aktivitetsloggavisering** knappen.

I båda fallen övervakar en aktivitetsloggavisering endast för händelser i prenumerationen som aviseringen har skapats.

Du kan konfigurera en aktivitetsloggavisering baserat på översta egenskaper i JSON-objekt för en händelse i aktivitetsloggen. Mer information finns i [översikt över Azure-aktivitetsloggen](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Läs mer om service health-händelser i [ta emot aviseringar för aktivitetsloggar för tjänstmeddelanden](./monitoring-activity-log-alerts-on-service-notifications.md). 

Aktivitetsloggsaviseringar har några vanliga alternativ:

- **Kategori**: administration, tjänstens hälsotillstånd, automatisk skalning, säkerhet, principer och rekommendation. 
- **Omfång**: enskild resurs eller uppsättning resurser som aviseringen i aktivitetsloggen har definierats. Omfattning för en aktivitetsloggavisering kan definieras på olika nivåer:
    - Resursnivå: till exempel för en specifik virtuell dator
    - Resursgruppsnivå: till exempel alla virtuella datorer i en specifik resursgrupp
    - Prenumerationsnivå: till exempel alla virtuella datorer i en prenumeration (eller) alla resurser i en prenumeration
- **Resursgrupp**: som standard varningsregeln har sparats i samma resursgrupp som mål som definierats i omfånget. Du kan också definiera resursgruppen där varningsregeln ska sparas.
- **Resurstypen**: Resource Manager definierats namnområdet för målet för aviseringen.

- **Åtgärdens namn**: The Resource Manager Role-Based Access Control åtgärdens namn.
- **Nivå**: allvarlighetsgraden för händelse (utförlig, information, varning, fel eller kritiskt).
- **Status för**: status för händelsen, vanligtvis igång, misslyckades eller lyckades.
- **Händelsen initierad av**: kallas även ”anroparen”. E-postadress eller Azure Active Directory-ID för användaren som utförde åtgärden.

> [!NOTE]
> I en prenumeration upp till 100 Varningsregler kan skapas för en aktivitet av scope på antingen: en enskild resurs, alla resurser i resource group (eller) hela prenumerationsnivå.

När en aktivitetsloggavisering är aktiverad, används en åtgärdsgrupp för att generera åtgärder eller aviseringar. En åtgärdsgrupp är en återanvändbar uppsättning meddelande olika mottagare, till exempel e-postadresser, telefonnummer för webhook-URL: er eller SMS. Mottagarna kan refereras från flera aviseringar centralisera och gruppera dina meddelandekanaler. När du definierar din aktivitetsloggsavisering har du två alternativ. Du kan:

* Använd en befintlig åtgärdsgrupp i din aktivitetsloggsavisering.
* Skapa en ny åtgärdsgrupp.

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper i Azure-portalen](monitoring-action-groups.md).


## <a name="next-steps"></a>Nästa steg
- Hämta en [översikt över aviseringar](monitoring-overview-alerts.md).
- Lär dig mer om [skapa och ändra aviseringar för aktivitetsloggar](alert-activity-log.md).
- Granska den [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).
- Lär dig mer om [service health meddelanden](monitoring-service-notifications.md).

