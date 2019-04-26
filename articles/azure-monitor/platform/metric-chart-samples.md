---
title: Azure Monitor måttdiagram-exempel
description: Lär dig att visualisera dina data med Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256731"
---
# <a name="metric-chart-samples"></a>Måttdiagram-exempel

Azure-plattformen erbjudanden [över tusen mått](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), många som saknar dimensioner. Med hjälp av [dimension filter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), tillämpar [uppdelningen](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), kontrollera diagramtyp och justera inställningar du kan skapa kraftfulla diagnostik vyer och instrumentpaneler som ger inblick i hälsotillstånd infrastruktur och program. Den här artikeln visar några exempel på diagram som du kan skapa med hjälp av [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) och beskriver de nödvändiga stegen för att konfigurera var och en av dessa diagram.

Om du vill dela dina exempel bra diagram med världen? Bidra till den här sidan på GitHub och dela egna diagram exemplen här!

## <a name="website-cpu-utilization-by-server-instances"></a>Webbplatsen CPU-användning av server-instanser

Det här diagrammet visar om CPU-för en App Service har inom det godkända intervallet och delar upp dem efter instans att avgöra om belastningen har distribuerats korrekt. Du ser i diagrammet som appen körs på en enskild server-instans innan 06.00 och sedan skalas upp genom att lägga till en annan instans.

![Linjediagram med genomsnittlig cpu-procent av server-instans](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Så här konfigurerar du det här diagrammet?

Välj din App Service-resurs och hitta den **CPU-procent** mått. Klicka sedan på **gäller dela** och välj den **instans** dimension.

## <a name="application-availability-by-region"></a>Tillgänglighet efter region

Visa programmets tillgänglighet efter region för att identifiera vilka geografiska platser har problem. Det här diagrammet visar mått för Application Insights-tillgänglighet. Du kan se att det övervakade programmet har inga problem med tillgänglighet från USA, östra datacenter, men det finns ett problem partiella tillgänglighet från USA, västra och Asien.

![Diagram över genomsnittlig tillgänglighet av platser](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Så här konfigurerar du det här diagrammet?

Du måste först aktivera [Application Insights tillgänglighet](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) övervakning för din webbplats. Efter det att välja Application Insights-resursen och välj mått för tillgänglighet. Tillämpa delning på den **Körningsplats** dimension.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volymen på lagringstransaktionerna konto per API-namn

Din resursen för lagringskonton har uppstått med en ytterligare volym av transaktioner. Du kan använda transaktioner mått för att identifiera vilka API: et är ansvarig för den ytterligare belastningen. Lägg märke till att följande diagram har konfigurerats med samma dimension (API-namn) för filtrering och dela upp om du vill begränsa vyn till endast API-anrop av intresse:

![Stapeldiagram för API-transaktioner](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Så här konfigurerar du det här diagrammet?

Välj ditt lagringskonto i Väljaren mått och **transaktioner** mått. Växla för **stapeldiagram**. Klicka på **gäller dela** och Välj dimensionens **API-namn**. Klicka sedan på den **Lägg till filter** och välj den **API-namn** dimensionen igen. Välj API: er som du vill rita på diagrammet i filterdialogrutan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om Azure Monitor [arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
* Läs mer om [Metric Explorer](metrics-charts.md)