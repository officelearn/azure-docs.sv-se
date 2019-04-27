---
title: Aviseringar för aktivitetsloggar i Azure Monitor
description: Ett SMS, webhook, SMS, e-post och mer, när vissa händelser i aktivitetsloggen.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 3459ef4caa99a2147144354165bef7d2852e87cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777822"
---
# <a name="alerts-on-activity-log"></a>Aviseringar för aktivitetsloggen 

## <a name="overview"></a>Översikt
Aktivitetsloggsaviseringar är aviseringar som aktiverar när en ny händelse i aktivitetsloggen inträffar som matchar de villkor som anges i aviseringen. De är Azure-resurser, så att de kan skapas med hjälp av en Azure Resource Manager-mall. De kan också vara skapas, uppdateras eller tas bort i Azure-portalen. Den här artikeln beskrivs koncepten bakom aktivitetsloggaviseringar. Den sedan visar hur du använder Azure-portalen för att ställa in en avisering på händelser i aktivitetsloggen. Mer information om användning finns i [skapa och hantera aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-activity-log.md).

> [!NOTE]
> Aviseringar **kan inte** skapas för händelser i aviseringskategori av aktivitetsloggen

Normalt kan du skapa aviseringar för aktivitetsloggen kan ta emot meddelanden när:

* Specifika åtgärder inträffar på resurser i din Azure-prenumeration som omfattar ofta enskilda resursgrupper eller resurser. Du kanske exempelvis vill meddelas när alla virtuella datorer i myProductionResourceGroup tas bort. Eller så kanske du vill få ett meddelande om eventuella nya roller har tilldelats en användare i din prenumeration.
* En service health-händelse inträffar. Service health-händelser innehåller aviseringar incidenter och underhållshändelser som gäller för resurser i din prenumeration.

En enkel liknelse för förstå villkor som Varningsregler kan skapas på aktivitetslogg, är att utforska eller Filtrera händelser via [aktivitetsloggen i Azure Portal](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). I Azure Monitor - aktivitetslogg, en filtrera eller hitta nödvändiga evenemang och sedan skapa en avisering med hjälp av den **Lägg till aktivitetsloggavisering** knappen.

I båda fallen övervakar en aktivitetsloggavisering endast för händelser i prenumerationen som aviseringen har skapats.

Du kan konfigurera en aktivitetsloggavisering baserat på översta egenskaper i JSON-objekt för en händelse i aktivitetsloggen. Mer information finns i [översikt över Azure-aktivitetsloggen](./../../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log). Läs mer om service health-händelser i [ta emot aviseringar för aktivitetsloggar för tjänstmeddelanden](./../../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

Aktivitetsloggsaviseringar har några vanliga alternativ:

- **Kategori**: Administration, tjänstens hälsotillstånd, automatisk skalning, säkerhet, principer och rekommendation. 
- **Omfång**: Enskild resurs eller uppsättning resurser som aviseringen i aktivitetsloggen har definierats. Omfattning för en aktivitetsloggavisering kan definieras på olika nivåer:
    - Resursnivå: Till exempel för en specifik virtuell dator
    - Resursgruppsnivå: Till exempel alla virtuella datorer i en specifik resursgrupp
    - Prenumerationsnivå: Till exempel alla virtuella datorer i en prenumeration (eller) alla resurser i en prenumeration
- **Resursgrupp**: Som standard sparas varningsregeln i samma resursgrupp som mål som definierats i omfånget. Du kan också definiera resursgruppen där varningsregeln ska sparas.
- **Resurstyp**: Resource Manager definierats namnområdet för målet för aviseringen.

- **Åtgärdens namn**: Åtgärdsnamnet rollbaserad åtkomstkontroll i Resource Manager.
- **Nivå**: Allvarlighetsgraden för händelse (utförlig, information, varning, fel eller kritiskt).
- **Status**: Status för händelsen, vanligtvis igång, misslyckades eller lyckades.
- **Händelsen initierad av**: Även känt som ”anroparen”. E-postadress eller Azure Active Directory-ID för användaren som utförde åtgärden.

> [!NOTE]
> I en prenumeration upp till 100 Varningsregler kan skapas för en aktivitet av scope på antingen: en enskild resurs, alla resurser i resource group (eller) hela prenumerationsnivå.

När en aktivitetsloggavisering är aktiverad, används en åtgärdsgrupp för att generera åtgärder eller aviseringar. En åtgärdsgrupp är en återanvändbar uppsättning meddelande olika mottagare, till exempel e-postadresser, telefonnummer för webhook-URL: er eller SMS. Mottagarna kan refereras från flera aviseringar centralisera och gruppera dina meddelandekanaler. När du definierar din aktivitetsloggsavisering har du två alternativ. Du kan:

* Använd en befintlig åtgärdsgrupp i din aktivitetsloggsavisering.
* Skapa en ny åtgärdsgrupp.

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper i Azure-portalen](../../azure-monitor/platform/action-groups.md).


## <a name="next-steps"></a>Nästa steg
- Hämta en [översikt över aviseringar](../../azure-monitor/platform/alerts-overview.md).
- Lär dig mer om [skapa och ändra aviseringar för aktivitetsloggar](../../azure-monitor/platform/alerts-activity-log.md).
- Granska den [avisering webhook för aktivitetslogg](activity-log-alerts-webhook.md).
- Lär dig mer om [service health meddelanden](../../azure-monitor/platform/service-notifications.md).

