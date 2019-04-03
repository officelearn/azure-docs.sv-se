---
title: Azure-underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemaläggning av underhållsläge ger kunder möjlighet att planera runt de nödvändiga schemalagda underhållshändelser som Azure SQL Data Warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846913"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Använda underhållsscheman för att hantera uppdateringar av tjänsten och underhåll

Underhållsscheman är nu tillgängliga i alla regioner för Azure SQL Data Warehouse. Den här funktionen integrerar Service Health planerat underhåll meddelanden, kontrollera Resource Health Monitor och Azure SQL Data Warehouse Underhåll schemaläggningstjänsten.

Du kan använda Underhåll schemaläggning för att välja ett tidsfönster när det är lämpligt att ta emot nya funktioner, uppgraderingar och uppdateringar. Du väljer en primär och en sekundär underhållsperiod inom en period på sju dagar. Ett exempel är en primär fönster för lördag 22:00 till söndag 01:00 och ett fönster för Onsdag 19:00 till 22:00. Om SQL Data Warehouse inte kan utföra underhåll under primära underhållsperiod, försöker underhållet igen under sekundära underhållsperiod. Underhåll kan inträffa under både primärt och sekundära windows. För att säkerställa snabb slutförandet av alla underhållsåtgärder, kunde lägre data warehouse nivåerna och DW400(c) slutföra Underhåll utanför en angiven underhållsperiod.

Alla nyligen skapade Azure SQL Data Warehouse har instanser ett systemdefinierade underhållsschema som tillämpas under distributionen. Schemat kan redigeras när distributionen är klar.

Varje underhållsperiod kan vara tre till åtta timmar. Underhåll kan inträffa när som helst i fönstret. Du bör förvänta dig en kort förlust av anslutning som tjänsten distribuerar ny kod till ditt informationslager.

Om du vill använda den här funktionen behöver du identifiera ett primära och sekundära fönster i separata dag intervall. Alla underhållsåtgärder bör slutföras inom tider för schemalagt underhåll. Inget Underhåll sker utanför de angivna underhållsfönster utan föregående meddelande. Om ditt informationslager är pausat under ett schemalagt underhåll, kommer att uppdateras under åtgärden återuppta.  

## <a name="alerts-and-monitoring"></a>Aviseringar och övervakning

Integrering med Tjänstehälsa för meddelanden och en resurs Kontrollera hälsoövervakning kan kunderna håller dig informerad om kommande Underhåll aktivitet. Nya automation drar nytta av Azure Monitor. Du kan bestämma hur du vill meddelas om nära förestående underhållshändelser. Också bestämma vilka automatiserade flöden kan hjälpa dig att hantera avbrott och minimera påverkan på din verksamhet.

En 24-timmarsformat förhandsinformation föregår alla underhållshändelser, med det aktuella undantaget DW400c och lägre nivåer. Se till att ditt informationslager har inga långvariga transaktioner innan din valda underhållsperiod för att minimera driftstopp för instansen. När Underhåll startar, avbryts alla aktiva sessioner. Icke-incheckade transaktioner återställs och ditt informationslager får en kort förlust av anslutning. Du kommer att meddelas omedelbart när underhållet har slutförts på ditt informationslager.

Om du har fått en avisering i förväg att Underhåll sker, men SQL Data Warehouse kan inte utföra underhåll under den tiden får du ett meddelande om annullering. Underhåll fortsätter sedan under nästa schemalagda underhållsperiod.

Alla aktiva underhållshändelser visas i den **Service Health - planerat underhåll** avsnittet. Tjänstehälsa historiken innehåller en fullständig post av tidigare händelser. Du kan övervaka Underhåll via Azure Service Health Kontrollera portalens instrumentpanel när en aktiva händelser.

### <a name="maintenance-schedule-availability"></a>Underhåll schema tillgänglighet

Även om schemaläggning av underhållsläge är inte tillgänglig i den valda regionen, kan du visa och redigera ditt underhållsschema när som helst. När Underhåll schemaläggning blir tillgängligt i din region blir identifierade schemat omedelbart aktiv på ditt informationslager.

## <a name="next-steps"></a>Nästa steg

- [Läs mer](viewing-maintenance-schedule.md) om hur du visar ett underhållsschema.
- [Läs mer](changing-maintenance-schedule.md) om hur du ändrar ett underhållsschema.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) skapa och hantera åtgärdsgrupper.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.
