---
title: Visa tjänstleverantörsaktivitet
description: Kunder kan visa loggad aktivitet för att se åtgärder som utförs av tjänsteleverantörer via Azure-delegerad resurshantering.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649644"
---
# <a name="view-service-provider-activity"></a>Visa tjänstleverantörsaktivitet

Kunder som har delegerade prenumerationer för Azure-delegerad resurshantering kan [visa Azure Activity-loggdata](../../azure-monitor/platform/platform-logs-overview.md) för att se alla åtgärder som vidtas. Detta ger kunderna full insyn i åtgärder som tjänstleverantörer utför via Azure-delegerad resurshantering, tillsammans med åtgärder som utförs av användare inom kundens egen Azure Active Directory (Azure AD) -klientorganisation.

> [!TIP]
> Vi tillhandahåller också en inbyggd azure-principdefinition för granskning av delegering av scope till en hanterande klientorganisation. Mer information finns i [Granskningsdelegationer i din miljö](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Visa aktivitetsloggdata

Du kan [visa aktivitetsloggen](../../azure-monitor/platform/activity-log-view.md) från **Monitor-menyn** i Azure-portalen. Om du vill begränsa resultaten till en viss prenumeration använder du filtren för att välja en viss prenumeration. Du kan också [visa och hämta aktivitetslogghändelser](../../azure-monitor/platform/activity-log-view.md) programmässigt.

> [!NOTE]
> Användare i en tjänsteleverantörs klient kan visa aktivitetsloggresultat för en delegerad prenumeration i en kundklient om de har tilldelats [rollen Reader](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsaråtkomst) när prenumerationen var inbyggd för Azure-delegerad resurshantering.

I aktivitetsloggen ser du namnet på åtgärden och dess status, tillsammans med datum och tid då den utfördes. **Händelsen som initierats av** kolumnen visar vilken användare som utförde åtgärden, oavsett om det var en användare i en tjänsteleverantörs klient som agerade via Azure-delegerad resurshantering eller en användare i kundens egen klientorganisation. Observera att namnet på användaren visas i stället för klienten eller den roll som användaren har tilldelats för den prenumerationen.

Loggad aktivitet är tillgänglig i Azure-portalen under de senaste 90 dagarna. Mer information om hur du lagrar dessa data i mer än 90 dagar finns [i Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Användare från tjänsteleverantören visas i aktivitetsloggen, men dessa användare och deras rolltilldelningar visas inte i **Åtkomstkontroll (IAM)** eller när rolltilldelningsinformation hämtas via API:er.

## <a name="set-alerts-for-critical-operations"></a>Ställ in aviseringar för kritiska åtgärder

Om du vill hålla dig medveten om kritiska åtgärder som tjänstleverantörer (eller användare i din egen klientorganisation) utför rekommenderar vi att du skapar [aktivitetsloggaviseringar](../../azure-monitor/platform/activity-log-alerts.md). Du kanske till exempel vill spåra alla administrativa åtgärder för en prenumeration eller meddelas när en virtuell dator i en viss resursgrupp tas bort. När du skapar aviseringar innehåller de åtgärder som utförs av användare i kundens egen klientorganisation samt i alla hanteringsklienter.

Mer information finns i [Skapa och hantera aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Skapa loggfrågor

Du kan skapa frågor för att analysera din loggade aktivitet eller fokusera på specifika objekt. En granskning kanske till exempel kräver att du rapporterar om alla åtgärder på administrativ nivå som utförs på en prenumeration. Du kan skapa en fråga som bara ska filtreras efter dessa åtgärder och sortera resultaten efter användare, datum eller ett annat värde.

Mer information finns [i Översikt över loggfrågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor](../../azure-monitor/index.yml).
- Lär dig hur du [visar och hanterar tjänstprovidererbjudanden](view-manage-service-providers.md) i Azure-portalen.