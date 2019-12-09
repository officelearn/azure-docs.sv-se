---
title: Visa Service Provider-aktivitet
description: Kunder kan visa loggad aktivitet för att se åtgärder som utförs av tjänst leverantörer via Azure-delegerad resurs hantering.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932689"
---
# <a name="view-service-provider-activity"></a>Visa Service Provider-aktivitet

Kunder som har delegerade prenumerationer för Azure-delegerad resurs hantering kan [Visa Azures aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) data för att se alla åtgärder som vidtagits. Detta ger kunderna full insyn i åtgärder som tjänst leverantörer utför via Azures delegerade resurs hantering, tillsammans med de som görs av användarna i kundens egna Azure Active Directory (Azure AD)-klient.

## <a name="view-activity-log-data"></a>Visa aktivitets logg data

Du kan [Visa aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) från **Monitor** -menyn i Azure Portal. Om du vill begränsa resultaten till en speciell prenumeration kan du använda filtren för att välja en speciell prenumeration. Du kan också [Visa och hämta aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) program mässigt.

> [!NOTE]
> Användare i en tjänst leverantörs klient organisation kan visa aktivitets logg resultat för en delegerad prenumeration i en kund klient organisation om de har beviljats rollen [läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst) när prenumerationen har registrerats för Azure-delegerad resurs hantering.

I aktivitets loggen visas namnet på åtgärden och dess status, tillsammans med datum och tid då den utfördes. **Händelsen som initieras av** kolumnen visar vilken användare som utförde åtgärden, om det var en användare i en tjänst leverantörs klient som agerar via Azure delegerad resurs hantering eller en användare i kundens egen klient. Observera att namnet på användaren visas, i stället för klient organisationen eller rollen som användaren har tilldelats för den prenumerationen.

Loggad aktivitet är tillgänglig i Azure Portal under de senaste 90 dagarna. Information om hur du lagrar data i mer än 90 dagar finns i [samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## <a name="set-alerts-for-critical-operations"></a>Ange aviseringar för kritiska åtgärder

För att vara medveten om kritiska åtgärder som tjänst leverantörer (eller användare i din egen klient) utför, rekommenderar vi att du skapar [aktivitets logg aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Du kanske till exempel vill spåra alla administrativa åtgärder för en prenumeration eller bli informerad när en virtuell dator i en viss resurs grupp tas bort. När du skapar aviseringar innehåller de åtgärder som vidtas av användare i kundens egen klient organisation samt i alla hanterings klienter.

Mer information finns i [skapa och hantera aktivitets logg aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## <a name="create-log-queries"></a>Skapa logg frågor

Du kan skapa frågor för att analysera din loggade aktivitet eller fokusera på vissa objekt. T. ex. kanske en granskning kräver att du rapporterar om alla åtgärder på administrativ nivå som utförs på en prenumeration. Du kan skapa en fråga för att filtrera på de här åtgärderna och sortera resultaten efter användare, datum eller annat värde.

Mer information finns i [Översikt över logg frågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/).
- Lär dig att [Visa och hantera Service Provider-erbjudanden](view-manage-service-providers.md) i Azure Portal.