---
title: Aviseringar för aktivitetslogg i Azure Monitor
description: Meddelas via SMS, webhook, SMS, e-post och mer, när vissa händelser inträffar i aktivitetsloggen.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 26ecfdb33b92c91010af63ec14089dd148d6bad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669021"
---
# <a name="alerts-on-activity-log"></a>Varningar i aktivitetsloggen

## <a name="overview"></a>Översikt

Aktivitetsloggaviseringar är aviseringar som aktiveras när en ny [aktivitetslogghändelse](activity-log-schema.md) inträffar som matchar de villkor som anges i aviseringen. Baserat på ordningen och volymen på de händelser som registrerats i [Azure-aktivitetsloggen](platform-logs-overview.md)aktiveras aviseringsregeln. Aviseringsregler för aktivitetslogg är Azure-resurser, så att de kan skapas med hjälp av en Azure Resource Manager-mall. De kan också skapas, uppdateras eller tas bort i Azure-portalen. Den här artikeln introducerar begreppen bakom aktivitetsloggaviseringar. Mer information om hur du skapar eller erar aktivitetsloggvarningsregler finns i [Skapa och hantera aktivitetsloggaviseringar](alerts-activity-log.md).

> [!NOTE]
> Aviseringar **kan inte** skapas för händelser i aviseringskategori i aktivitetsloggen.

Vanligtvis skapar du aktivitetsloggaviseringar för att ta emot meddelanden när:

* Specifika åtgärder utförs på resurser i din Azure-prenumeration, ofta begränsade till vissa resursgrupper eller resurser. Du kanske till exempel vill bli meddelad när en virtuell dator i myProductionResourceGroup tas bort. Du kanske vill få ett meddelande om några nya roller tilldelas en användare i prenumerationen.
* En hälsohändelse för tjänsten inträffar. Tjänsthälsohändelser inkluderar meddelanden om incidenter och underhållshändelser som gäller för resurser i din prenumeration.

En enkel analogi för att förstå villkor för vilka varningsregler kan skapas i aktivitetsloggen är att utforska eller filtrera händelser via [Aktivitetslogg i Azure Portal](activity-log-view.md#azure-portal). I Azure Monitor - Aktivitetslogg kan man filtrera eller hitta nödvändig händelse och sedan skapa en avisering med hjälp av knappen **Lägg till aktivitetslogg.**

I båda fallen övervakar en aktivitetsloggavisering endast för händelser i prenumerationen där aviseringen skapas.

Du kan konfigurera en aktivitetsloggavisering baserat på vilken egenskap på den översta nivån i JSON-objektet för en aktivitetslogghändelse. Mer information finns [i Kategorier i aktivitetsloggen](activity-log-view.md#categories-in-the-activity-log). Mer information om hälsohändelser för tjänsten finns i [Ta emot aktivitetsloggaviseringar för tjänstmeddelanden](alerts-activity-log-service-notifications.md). 

Aktivitetsloggvarningar har några vanliga alternativ:

- **Kategori**: Administration, ServiceHälsa, Automatisk skalning, Säkerhet, Princip och Rekommendation. 
- **Scope**: Den eller de resurser som aviseringen för aktivitetsloggen har definierats för. Omfattning för en aktivitetsloggavisering kan definieras på olika nivåer:
    - Resursnivå: Till exempel för en viss virtuell dator
    - Resursgruppsnivå: Till exempel alla virtuella datorer i en viss resursgrupp
    - Prenumerationsnivå: Till exempel alla virtuella datorer i en prenumeration (eller) alla resurser i en prenumeration
- **Resursgrupp**: Som standard sparas varningsregeln i samma resursgrupp som för målet som definierats i Scope. Användaren kan också definiera resursgruppen där aviseringsregeln ska lagras.
- **Resurstyp**: Resource Manager har definierat namnområde för aviseringens mål.
- **Åtgärdsnamn**: [Åtgärdsnamnet Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) som används för rollbaserad åtkomstkontroll . Åtgärder som inte har registrerats med Azure Resource Manager kan inte användas i en aviseringsregel för aktivitetslogg.
- **Nivå**: Allvarlighetsgraden för händelsen (utförliga, informativa, varning, fel eller kritiska).
- **Status**: Status för händelsen, vanligtvis Startad, Misslyckad eller lyckades.
- **Händelse initierad av**: Kallas även "uppringaren". E-postadressen eller Azure Active Directory-identifieraren för den användare som utförde åtgärden.

> [!NOTE]
> I en prenumeration kan upp till 100 aviseringsregler skapas för en aktivitet med omfattning på antingen: en enda resurs, alla resurser i resursgruppen (eller) hela prenumerationsnivån.

När en aktivitetsloggavisering är aktiverad används en åtgärdsgrupp för att generera åtgärder eller meddelanden. En åtgärdsgrupp är en återanvändbar uppsättning meddelandemottagare, till exempel e-postadresser, webhook-url:er eller SMS-telefonnummer. Mottagarna kan refereras från flera aviseringar för att centralisera och gruppera dina meddelandekanaler. När du definierar aktivitetsloggaviseringen har du två alternativ. Du kan:

* Använd en befintlig åtgärdsgrupp i aktivitetsloggaviseringen.
* Skapa en ny åtgärdsgrupp.

Mer information om åtgärdsgrupper finns [i Skapa och hantera åtgärdsgrupper i Azure-portalen](action-groups.md).


## <a name="next-steps"></a>Nästa steg

- Få en [översikt över aviseringar](alerts-overview.md).
- Lär dig mer om [att skapa och ändra aktivitetsloggaviseringar](alerts-activity-log.md).
- Granska [webbkroksschemat för aktivitetsloggen.](activity-log-alerts-webhook.md)
- Läs mer om [hälsomeddelanden](service-notifications.md)för tjänster .
