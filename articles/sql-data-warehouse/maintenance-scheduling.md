---
title: Azure underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemalägga Underhåll kan kunderna planera runt händelserna behövs schemalagt underhåll Azure SQL Data warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228468"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Hantera uppdateringar av tjänsten och underhåll med hjälp av underhållsscheman

Azure SQL Data Warehouse Underhåll schemaläggning finns nu i förhandsversion. Den här nya funktionen integreras sömlöst Service Health planerat underhåll meddelanden, kontrollera Resource Health Monitor och Azure SQL Data Warehouse Underhåll schemaläggningstjänsten.

Schemalägga Underhåll kan du schemalägga ett tidsfönster när det är lämpligt att ta emot nya funktioner, uppgraderingar, och korrigeringar. Kunder som väljer en primär och en sekundär underhållsperiod inom en period på 7 dagar, d.v.s. lördag 22:00 – söndag 01:00 (primär) och onsdag 19:00 – 22:00 (sekundär). Om vi inte att utföra underhåll under primära underhållsperiod försöker vi den igen under sekundära underhållsperiod.

Alla nyligen skapade Azure SQL Data Warehouse har instanser ett systemdefinierade underhållsschema som tillämpas under distributionen. Schemat kan redigeras när distributionen är klar.

Varje underhållsperiod kan vara mellan 3 och 8 timmar, med 3hrs för närvarande är det kortaste tillgängliga alternativet. Underhåll kan inträffa när som helst i fönstret och du bör förvänta dig en kort förlust av anslutning som tjänsten distribuerar ny kod till ditt informationslager. 

I förhandsversionen funktionen ber vi kunder att identifiera sina primära och sekundära windows inom separat dag intervall.   
Alla underhållsåtgärder bör slutföras inom tider för schemalagt underhåll och inget Underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande. Om ditt informationslager är pausat under ett schemalagt underhåll, kommer att uppdateras under åtgärden återuppta.  


## <a name="alerts-and-monitoring"></a>Aviseringar och övervakning

Sömlös integrering med meddelanden om hälsostatus för tjänsten och resurshälsan Kontrollera övervakaren gör det möjligt för kunder att hålla dig informerad om kommande Underhåll aktivitet. Nya automation drar nytta av Azure Monitor och gör att kunder kan bestämma hur de vill meddelas om nära förestående underhållshändelser och som automatiserade flöden ska aktiveras för att hantera avbrott och minimera påverkan på sin verksamhet.

Alla underhållshändelser föregås av en avisering i förväg 24 timme. För att minimera driftstopp instans, bör du kontrollera att det finns inga långvariga transaktioner på ditt informationslager innan början av din valda underhållsperiod. När Underhåll startar avbryts alla aktiva sessioner och icke-incheckade transaktioner återställs ditt informationslager får en kort förlust av anslutning. Du kommer att meddelas omedelbart när underhållet har slutförts på ditt informationslager. 

Om du har fått en avisering i förväg att Underhåll sker, men vi kan inte utföra underhåll under den tiden får du ett meddelande om annullering. Underhåll fortsätter sedan under nästa schemalagda underhållsperiod.
 
Alla aktiva underhållshändelser visas i den ”Service Health - planerat underhåll” avsnittet. En fullständig post av tidigare händelser kommer att sparas som en del av Tjänstehälsa för historik. Underhåll kan övervakas via Azure Service Health Kontrollera portalens instrumentpanel när en aktiva händelser.

### <a name="maintenance-schedule-availability"></a>Underhåll schema tillgänglighet

Även om Underhåll schemaläggning inte ännu finns tillgänglig i den valda regionen, kan du fortfarande visa och redigera ditt underhållsschema när som helst. När Underhåll schemaläggning blir tillgängligt i din region blir schemat identifieras omedelbart aktiv på ditt informationslager.


## <a name="next-steps"></a>Nästa steg

- [Läs mer](viewing-maintenance-schedule.md) om hur du visar en Underhåll schema 
- [Läs mer](changing-maintenance-schedule.md) om hur du ändrar ett underhållsschema
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, visa och hantera aviseringar med Azure Monitor
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om Webhook-åtgärder för loggaviseringsregler
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health







