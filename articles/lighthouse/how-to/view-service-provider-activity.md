---
title: Visa tjänstleverantörsaktivitet
description: Kunder kan visa loggad aktivitet för att se åtgärder som utförs av tjänst leverantörer via Azure-delegerad resurs hantering.
ms.date: 01/15/2020
ms.topic: how-to
ms.openlocfilehash: 42af2aa7fca5031eb50bd421a5207320858b70cf
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636452"
---
# <a name="view-service-provider-activity"></a>Visa tjänstleverantörsaktivitet

Kunder som har delegerade prenumerationer för Azure-delegerad resurs hantering kan [Visa Azures aktivitets logg](../../azure-monitor/platform/platform-logs-overview.md) data för att se alla åtgärder som vidtagits. Detta ger kunderna full insyn i åtgärder som tjänst leverantörer utför via Azures delegerade resurs hantering, tillsammans med åtgärder som utförs av användare i kundens egna Azure Active Directory (Azure AD)-klient.

> [!TIP]
> Vi tillhandahåller också en Azure Policy inbyggd princip definition för att granska delegering av omfattningar till en hanterings klient. Mer information finns i [Granska delegeringar i din miljö](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Visa aktivitets logg data

Du kan [Visa aktivitets loggen](../../azure-monitor/platform/activity-log-view.md) från **Monitor** -menyn i Azure Portal. Om du vill begränsa resultaten till en speciell prenumeration använder du filtren för att välja en speciell prenumeration. Du kan också [Visa och hämta aktivitets logg händelser](../../azure-monitor/platform/activity-log-view.md) program mässigt.

> [!NOTE]
> Användare i en tjänst leverantörs klient organisation kan visa aktivitets logg resultat för en delegerad prenumeration i en kund klient organisation om de har beviljats rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst) när prenumerationen har registrerats för Azure-delegerad resurs hantering.

I aktivitets loggen visas namnet på åtgärden och dess status, tillsammans med datum och tid då den utfördes. **Händelsen som initieras av** kolumnen visar vilken användare som utförde åtgärden, om det var en användare i en tjänst leverantörs klient som agerar via Azure delegerad resurs hantering eller en användare i kundens egen klient. Observera att namnet på användaren visas, i stället för klient organisationen eller rollen som användaren har tilldelats för den prenumerationen.

Loggad aktivitet är tillgänglig i Azure Portal under de senaste 90 dagarna. Information om hur du lagrar data i mer än 90 dagar finns i [samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Användare från tjänst leverantören visas i aktivitets loggen, men de här användarna och deras roll tilldelningar visas inte i **Access Control (IAM)** eller när roll tilldelnings information hämtas via API: er.

## <a name="set-alerts-for-critical-operations"></a>Ange aviseringar för kritiska åtgärder

För att vara medveten om kritiska åtgärder som tjänst leverantörer (eller användare i din egen klient) utför, rekommenderar vi att du skapar [aktivitets logg aviseringar](../../azure-monitor/platform/activity-log-alerts.md). Du kanske till exempel vill spåra alla administrativa åtgärder för en prenumeration eller bli informerad när en virtuell dator i en viss resurs grupp tas bort. När du skapar aviseringar, kommer de att omfatta åtgärder som utförs av användare i kundens egen klient organisation samt i alla hanterings klienter.

Mer information finns i [skapa och hantera aktivitets logg aviseringar](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Skapa logg frågor

Du kan skapa frågor för att analysera din loggade aktivitet eller fokusera på vissa objekt. T. ex. kanske en granskning kräver att du rapporterar om alla åtgärder på administrativ nivå som utförs på en prenumeration. Du kan skapa en fråga för att filtrera på de här åtgärderna och sortera resultaten efter användare, datum eller annat värde.

Mer information finns i [Översikt över logg frågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor](../../azure-monitor/index.yml).
- Lär dig att [Visa och hantera Service Provider-erbjudanden](view-manage-service-providers.md) i Azure Portal.