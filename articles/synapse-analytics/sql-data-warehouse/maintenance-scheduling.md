---
title: Underhållsscheman för Synapse SQL-pool
description: Underhållsplanering gör det möjligt för kunder att planera runt de nödvändiga schemalagda underhållshändelser som Azure Synapse Analytics använder för att distribuera nya funktioner, uppgraderingar och korrigeringar.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 43fc32e910c51e8b70e15aa49584a18e5b703fca
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631583"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Använd underhållsscheman för att hantera serviceuppdateringar och underhåll

Underhållsschemafunktionen integrerar servicehälsoplanerade underhållsmeddelanden, resurshälsokontrollövervakaren och underhållsschemaläggningstjänsten för Synapse SQL-pool (datalager) i Azure Synapse Analytics.

Du bör använda underhållsplanering för att välja ett tidsfönster när det är praktiskt att ta emot nya funktioner, uppgraderingar och korrigeringsfiler. Du måste välja ett primärt och ett sekundärt underhållsfönster inom en sjudagarsperiod, varje fönster måste vara inom separata dagintervall.

Du kan till exempel schemalägga ett primärt fönster på lördag 22:00 till söndag 01:00 och sedan schemalägga ett sekundärt fönster onsdag 19:00 till 22:00. Om underhåll inte kan utföras under det primära underhållsfönstret försöker det igen under det sekundära underhållsfönstret. Underhåll av service kan ibland ske under både de primära och sekundära fönstren. För att säkerställa ett snabbt slutförande av alla underhållsåtgärder kan DW400c och lägre informationslagernivåer slutföra underhållet utanför ett angivet underhållsfönster.

Alla nyskapade informationslagerinstanser har en systemdefinierad underhållsschema som tillämpas under distributionen. Schemat kan redigeras så snart distributionen är klar.

Även om ett underhållsfönster kan vara mellan tre och åtta timmar betyder det inte att informationslagret är offline under hela tiden. Underhåll kan ske när som helst inom det fönstret och du bör förvänta dig en enda frånkoppling under den perioden som varar ~5 -6 minuter när tjänsten distribuerar ny kod till ditt informationslager. DW400c och lägre kan uppleva flera korta förluster i anslutning vid olika tidpunkter under underhållsfönstret. När underhållet startar avbryts alla aktiva sessioner och icke-genomförda transaktioner återställs. För att minimera förekomststopptid, se till att ditt informationslager inte har några långvariga transaktioner före den valda underhållsperioden.

Alla underhållsåtgärder bör slutföras inom de angivna underhållsfönstren om vi inte behöver distribuera en tidskänslig uppdatering. Om ditt informationslager pausas under ett schemalagt underhåll uppdateras det under återuppta-åtgärden. Du meddelas omedelbart efter att underhållet av informationslager har slutförts.

## <a name="alerts-and-monitoring"></a>Varningar och övervakning

Integrering med servicehälsomeddelanden och Resource Health Check Monitor gör det möjligt för kunder att hålla sig informerade om förestående underhållsaktiviteter. Den här automatiseringen drar nytta av Azure Monitor. Du kan bestämma hur du vill bli meddelad om förestående underhållshändelser. Du kan också välja vilka automatiserade flöden som hjälper dig att hantera driftstopp och minimera driftspåverkan.

Ett 24-timmars förhandsmeddelande föregår alla underhållshändelser som inte är för DWC400c- och lägre nivåer.

> [!NOTE]
> I händelse av att vi är skyldiga att distribuera en tidskritisk uppdatering kan avancerade meddelandetider minskas avsevärt.

Om du har fått ett förhandsmeddelande om att underhåll kommer att ske, men underhåll inte kan utföras under tidsperioden i meddelandet, får du ett meddelande om avbokning. Underhållet återupptas sedan under nästa schemalagda underhållsperiod.

Alla aktiva underhållshändelser visas i avsnittet **Service Health - Planned Maintenance.** Tjänstens hälsohistorik innehåller en fullständig förteckning över tidigare händelser. Du kan övervaka underhåll via Azure Service Health check portal instrumentpanelen under en aktiv händelse.

### <a name="maintenance-schedule-availability"></a>Tillgänglighet för underhållsschema

Även om underhållsplanering inte är tillgängligt i den valda regionen kan du när som helst visa och redigera underhållsschemat. När underhållsplanering blir tillgängligt i din region blir det identifierade schemat omedelbart aktivt på din Synapse SQL-pool.

## <a name="view-a-maintenance-schedule"></a>Visa ett underhållsschema

Som standard har alla nyskapade informationslagerinstanser ett åtta timmars primärt och sekundärt underhållsfönster som tillämpas under distributionen. Som nämnts ovan kan du ändra fönstren så snart distributionen är klar. Inget underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande.

Så här visar du underhållsschemat som har tillämpats på Din Synapse SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj den Synapse SQL-pool som du vill visa.
3. Den valda Synapse SQL-poolen öppnas på översiktsbladet. Underhållsschemat som tillämpas på informationslagret visas under **Underhållsschema**.

![Överblickblad](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Ändra ett underhållsschema

Ett underhållsschema kan uppdateras eller ändras när som helst. Om den valda instansen går igenom en aktiv underhållscykel sparas inställningarna. De kommer att bli aktiva under nästa identifierade underhållsperiod. [Läs mer](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om att övervaka ditt informationslager under en aktiv underhållshändelse.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identifiera de primära och sekundära fönstren

De primära och sekundära fönstren måste ha separata dagintervall. Ett exempel är ett primärt fönster tisdag–torsdag och ett sekundärt fönster på lördag–söndag.

Så här ändrar du underhållsschemat för Synapse SQL-poolen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj den Synapse SQL-pool som du vill uppdatera. Sidan öppnas på översiktsbladet.
Öppna sidan för inställningar för underhållsschema genom att välja sammanfattningslänken **Underhållsschema** på översiktsbladet. Du kan också välja alternativet **Underhållsschema** på resursmenyn till vänster.

    ![Alternativ för översiktsblad](./media/maintenance-scheduling/maintenance-change-option.png)

3. Identifiera önskat dagintervall för ditt primära underhållsfönster med hjälp av alternativen högst upp på sidan. Det här valet avgör om ditt primära fönster kommer att inträffa på en veckodag eller under helgen. Ditt val uppdaterar listrårsvärdena.
Under förhandsversionen kanske vissa regioner ännu inte har stöd för alla tillgängliga **dagalternativ.**

   ![Bladet underhållsinställningar](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Välj önskade primära och sekundära underhållsfönster med hjälp av listrutorna:
   - **Dag**: Önskad dag för att utföra underhåll under det valda fönstret.
   - **Starttid**: Önskad starttid för underhållsfönstret.
   - **Tidsfönster**: Önskad varaktighet för tidsfönstret.

   **Sammanfattningsområdet Schema** längst ned på bladet uppdateras baserat på de värden som du har valt.
  
5. Välj **Spara**. Ett meddelande visas som bekräftar att det nya schemat nu är aktivt.

   Om du sparar ett schema i en region som inte stöder schemaläggning av underhåll visas följande meddelande. Dina inställningar sparas och aktiveras när funktionen blir tillgänglig i den valda regionen.

   ![Meddelande om regionens tillgänglighet](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Nästa steg

- [Läs mer](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om hur du skapar, visar och hanterar aviseringar med hjälp av Azure Monitor.
- [Läs mer](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om webhook-åtgärder för loggvarningsregler.
- [Läs mer](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Skapa och hantera åtgärdsgrupper.
- [Läs mer](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om Azure Service Health.
