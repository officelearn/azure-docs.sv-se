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
ms.openlocfilehash: 0e5df583112bbb6db9651004e5deaceb6b5b9d12
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958897"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Använd underhålls scheman för att hantera uppdateringar och underhåll av tjänster

Underhålls scheman är nu tillgängliga i alla Azure SQL Data Warehouse regioner. Funktionen underhålls schema integrerar Service Health planerade underhålls aviseringar, Resource Health kontrol lera övervakning och tjänsten för schemaläggning av Azure SQL Data Warehouse underhåll.

Du använder underhålls schemaläggning för att välja en tids period när det är praktiskt att ta emot nya funktioner, uppgraderingar och korrigeringar. Du väljer ett primärt och ett sekundärt underhålls fönster inom en sju dagars period. Om du vill använda den här funktionen måste du identifiera ett primärt och sekundärt fönster inom olika dags intervall.

Du kan till exempel schemalägga ett primärt fönster på lördag 22:00 till söndag 01:00 och sedan schemalägga ett sekundärt fönster på onsdag 19:00 till 22:00. Om SQL Data Warehouse inte kan utföra underhåll under ditt primära underhålls fönster, kommer det att försöka utföra underhållet igen under det sekundära underhålls fönstret. Underhåll av tjänsten kan ske under både den primära och den sekundära Windows. För att säkerställa snabb avslutning av alla underhålls åtgärder kan DW400 (c) och lägre informations lager nivåer slutföra underhållet utanför ett angivet underhålls fönster.

Alla nyskapade Azure SQL Data Warehouse-instanser får ett Systemdefinierat underhålls schema som tillämpas under distributionen. Schemat kan redige ras så snart distributionen är klar.

Varje underhålls period kan vara mellan tre och åtta timmar. Underhåll kan ske när som helst i fönstret. När underhållet startar avbryts alla aktiva sessioner och transaktioner som inte allokeras kommer att återställas. Du bör förvänta dig flera korta förluster i anslutningen eftersom tjänsten distribuerar ny kod till ditt data lager. Du får ett meddelande direkt efter att ditt underhåll av data lagret har slutförts.

 Alla underhålls åtgärder bör slutföras inom det schemalagda underhålls fönstret. Inget underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande. Om informations lagret har pausats under ett schemalagt underhåll uppdateras det under återställnings åtgärden. 

## <a name="alerts-and-monitoring"></a>Aviseringar och övervakning

Genom att integrera med Service Health-meddelanden och Resource Health kontroll övervakaren kan kunderna hålla dig informerad om kommande underhålls aktiviteter. Den nya automationen drar nytta av Azure Monitor. Du kan välja hur du vill bli meddelad om kommande underhålls händelser. Du kan också välja vilka automatiserade flöden som hjälper dig att hantera drift stopp och minimera drifts påverkan.

Ett meddelande om 24-timmarsformat föregås av alla underhålls händelser som inte är för DWC400c och lägre nivåer. Se till att informations lagret inte har några tids krävande transaktioner innan du väljer underhålls period för att minimera instans stillestånd.

> [!NOTE]
> I händelse av att vi krävs för att distribuera en tidpunkts kritisk uppdatering kan avancerade aviserings tider minska avsevärt.

Om du har fått ett meddelande om att underhåll pågår, men SQL Data Warehouse inte kan utföra underhåll under den tiden, får du ett meddelande om uppsägning. Underhåll kommer sedan att återupptas under nästa schemalagda underhålls period.

Alla aktiva underhålls händelser visas i avsnittet **service Health-planerat underhåll** . Service Health historiken innehåller en fullständig post med tidigare händelser. Du kan övervaka underhåll via Azure Service Health kontrol lera portalens instrument panel under en aktiv händelse.

### <a name="maintenance-schedule-availability"></a>Tillgänglighet för underhålls schema

Även om underhålls planeringen inte är tillgänglig i den valda regionen kan du när som helst Visa och redigera ditt underhålls schema. När underhålls planeringen blir tillgänglig i din region aktive ras det identifierade schemat omedelbart på ditt data lager.

## <a name="view-a-maintenance-schedule"></a>Visa ett underhålls schema 

### <a name="portal"></a>Portalen

Som standard har alla nyligen skapade instanser av Azure SQL-informationslager en åtta timmar lång primär och sekundär underhållsperiod under distributionen. Som anges ovan kan du ändra Windows så snart distributionen är klar. Inget underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande.

Om du vill visa underhållsschemat som tillämpas på ditt informationslager gör du följande:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Välj det informations lager som du vill visa. 
3.  Det valda data lagret öppnas på bladet översikt. Det underhålls schema som tillämpas på data lagret visas under **underhålls schema**.

![Översikts blad](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Ändra ett underhålls schema 

### <a name="portal"></a>Portalen
Ett underhålls schema kan uppdateras eller ändras när som helst. Om den valda instansen går genom en aktiv underhålls cykel sparas inställningarna. De blir aktiva under nästa identifierade underhålls period. [Lär dig mer](https://docs.microsoft.com/azure/service-health/resource-health-overview) om att övervaka ditt informations lager under en aktiv underhålls händelse. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identifiera de primära och sekundära Fönstren

De primära och sekundära Fönstren måste ha separata dags intervall. Ett exempel är ett primärt fönster på tisdag – torsdag och ett sekundärt fönster på lördag – söndag.

Utför följande steg för att ändra underhålls schema för ditt informations lager:
1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Välj det informations lager som du vill uppdatera. Sidan öppnas på bladet översikt. 
3.  Öppna sidan för underhålls schema inställningar genom att välja **sammanfattnings länken underhålls schema (för hands version)** på översikts bladet. Alternativt väljer du alternativet **underhålls schema** på resurs menyn i den vänstra sidan.  

    ![Översikt över blad alternativ](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifiera det önskade dags intervallet för ditt primära underhålls fönster med hjälp av alternativen överst på sidan. Det här valet avgör om ditt primära fönster kommer att ske på en veckodag eller över helgen. Ditt val uppdaterar List Rute värden. Under för hands versionen kanske vissa regioner ännu inte har stöd för den fullständiga uppsättningen tillgängliga **dags** alternativ.

   ![Bladet underhålls inställningar](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Välj önskat primärt och sekundärt underhålls fönster med hjälp av list rutorna:
   - **Dag**: Prioriterad dag för att utföra underhåll under den valda perioden.
   - **Start tid**: Önskad start tid för underhålls perioden.
   - **Tids period**: Önskad varaktighet för din tidsperiod.

   Platsen för **schema Sammanfattning** längst ned på bladet uppdateras baserat på de värden som du har valt. 
  
6. Välj **Spara**. Ett meddelande visas som bekräftar att ditt nya schema nu är aktivt. 

   Om du sparar ett schema i en region som inte har stöd för underhålls planeringen visas följande meddelande. Inställningarna sparas och aktive ras när funktionen blir tillgänglig i den valda regionen.    

   ![Meddelande om region tillgänglighet](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, Visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för logg aviserings regler.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Skapa och hantera åtgärds grupper.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.