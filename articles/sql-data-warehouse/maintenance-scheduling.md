---
title: Azure Maintenance-scheman (för hands version) | Microsoft Docs
description: Med underhålls planeringen kan kunderna planera kring de nödvändiga schemalagda underhålls händelser som Azure SQL Data Warehouse tjänsten använder för att distribuera nya funktioner, uppgraderingar och korrigeringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278112"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Använd underhålls scheman för att hantera uppdateringar och underhåll av tjänster

Underhålls scheman är nu tillgängliga i alla Azure SQL Data Warehouse regioner. Den här funktionen integrerar Service Health planerade underhålls aviseringar, Resource Health kontrol lera övervakning och tjänsten för schemaläggning av Azure SQL Data Warehouse underhåll.

Du använder underhålls schemaläggning för att välja en tids period när det är praktiskt att ta emot nya funktioner, uppgraderingar och korrigeringar. Du väljer ett primärt och ett sekundärt underhålls fönster inom en sju dagars period. Ett exempel är ett primärt fönster på lördag 22:00 till söndag 01:00 och ett sekundärt fönster på onsdag 19:00 till 22:00. Om SQL Data Warehouse inte kan utföra underhåll under ditt primära underhålls fönster, kommer det att försöka utföra underhållet igen under det sekundära underhålls fönstret. Underhåll av tjänsten kan ske under både den primära och den sekundära Windows. För att säkerställa snabb avslutning av alla underhålls åtgärder kan DW400 (c) och lägre informations lager nivåer slutföra underhållet utanför ett angivet underhålls fönster.

Alla nyskapade Azure SQL Data Warehouse-instanser får ett Systemdefinierat underhålls schema som tillämpas under distributionen. Schemat kan redige ras så snart distributionen är klar.

Varje underhålls period kan vara tre till åtta timmar. Underhåll kan ske när som helst i fönstret. När underhållet startar avbryts alla aktiva sessioner och transaktioner som inte allokeras kommer att återställas. Du bör förvänta dig flera korta förluster i anslutningen eftersom tjänsten distribuerar ny kod till ditt data lager. Du får ett meddelande direkt efter att underhållet är klart på ditt data lager

Om du vill använda den här funktionen måste du identifiera ett primärt och sekundärt fönster inom olika dags intervall. Alla underhålls åtgärder bör slutföras inom det schemalagda underhålls fönstret. Inget underhåll sker utanför de angivna underhålls Fönstren utan föregående meddelande. Om informations lagret har pausats under ett schemalagt underhåll uppdateras det under återställnings åtgärden.  

## <a name="alerts-and-monitoring"></a>Aviseringar och övervakning

Genom att integrera med Service Health-meddelanden och Resource Health kontroll övervakaren kan kunderna hålla dig informerad om kommande underhålls aktiviteter. Den nya automationen drar nytta av Azure Monitor. Du kan välja hur du vill bli meddelad om kommande underhålls händelser. Bestäm också vilka automatiserade flöden som kan hjälpa dig att hantera nedtid och minimera påverkan på dina åtgärder.

Ett meddelande om 24 timmar före alla underhålls händelser, med det aktuella undantaget DW400c och lägre nivåer. Se till att informations lagret inte har några tids krävande transaktioner innan du väljer underhålls period för att minimera instans stillestånd.

> [!NOTE]
> I händelse av att vi krävs för att distribuera en tidpunkts kritisk uppdatering kan avancerade aviserings tider minska avsevärt.

Om du har fått ett meddelande om att underhåll pågår, men SQL Data Warehouse inte kan utföra underhåll under den tiden, får du ett meddelande om uppsägning. Underhåll kommer sedan att återupptas under nästa schemalagda underhålls period.

Alla aktiva underhålls händelser visas i avsnittet **service Health-planerat underhåll** . Service Health historiken innehåller en fullständig post med tidigare händelser. Du kan övervaka underhåll via Azure Service Health kontrol lera portalens instrument panel under en aktiv händelse.

### <a name="maintenance-schedule-availability"></a>Tillgänglighet för underhålls schema

Även om underhålls planeringen inte är tillgänglig i den valda regionen kan du när som helst Visa och redigera ditt underhålls schema. När underhålls planeringen blir tillgänglig i din region aktive ras det identifierade schemat omedelbart på ditt data lager.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](viewing-maintenance-schedule.md) om att visa ett underhålls schema.
- [Läs mer](changing-maintenance-schedule.md) om hur du ändrar ett underhålls schema.
- [Lär dig mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, Visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för logg aviserings regler.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Skapa och hantera åtgärds grupper.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.
