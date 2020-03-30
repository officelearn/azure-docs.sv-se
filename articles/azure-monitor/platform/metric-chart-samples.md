---
title: Exempel på Azure Monitor-måttdiagram
description: Läs mer om hur du visualiserar dina Azure Monitor-data.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660878"
---
# <a name="metric-chart-examples"></a>Exempel på måttdiagram 

Azure-plattformen erbjuder [över tusen mått](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), varav många har dimensioner. Genom att använda [dimensionsfilter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)kan du skapa kraftfulla diagnostikvyer och instrumentpaneler som ger insikt i infrastrukturens och programmens hälsa genom att [använda delning,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)styra diagramtyp och justera diagraminställningar. Den här artikeln visar några exempel på diagram som du kan skapa med hjälp av [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) och förklarar de nödvändiga stegen för att konfigurera vart och ett av dessa diagram.

Vill du dela dina fantastiska diagram exempel med världen? Bidra till den här sidan på GitHub och dela dina egna diagramexempel här!

## <a name="website-cpu-utilization-by-server-instances"></a>Webbplats CPU-användning av serverinstanser

Det här diagrammet visar om CPU för en App Service var inom det acceptabla intervallet och bryter ner det efter instans för att avgöra om belastningen var korrekt fördelad. Du kan se från diagrammet att appen kördes på en enda serverinstans före 06.00 och sedan skalas upp genom att lägga till en annan instans.

![Linjediagram över genomsnittlig cpu-procent per serverinstans](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

Välj apptjänstresursen och leta reda på måttet **CPU-procent.** Klicka sedan på **Använd delning** och välj **förekomstdimensionen.**

## <a name="application-availability-by-region"></a>Programmets tillgänglighet per region

Visa programmets tillgänglighet efter region för att identifiera vilka geografiska platser som har problem. Det här diagrammet visar tillgänglighetsmåttet Application Insights. Du kan se att det övervakade programmet inte har några problem med tillgänglighet från östra USA-datacentret, men det har ett partiellt tillgänglighetsproblem från västra USA och Östasien.

![Diagram över genomsnittlig tillgänglighet efter platser](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

Du måste först aktivera [tillgänglighetsövervakning](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) för Application Insights för din webbplats. Därefter väljer du din Application Insights-resurs och väljer mätvärden För tillgänglighet. Använd delning på dimensionen **Kör plats.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volymen av lagringskontotransaktioner efter API-namn

Din lagringskontoresurs har en överskjutande transaktionsvolym. Du kan använda transaktionsmåttet för att identifiera vilket API som är ansvarigt för den överskjutande belastningen. Observera att följande diagram är konfigurerat med samma dimension (API-namn) vid filtrering och delning för att begränsa vyn till endast API-anrop av intresse:

![Stapeldiagram över API-transaktioner](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

I måttväljaren väljer du ditt lagringskonto och mätvärden **Transaktioner.** Växla diagramtyp till **stapeldiagram**. Klicka på **Använd delning** och välj **dimensions-API-namn**. Klicka sedan på **filtret Lägg till** och välj **API-namndimensionen** igen. Markera de API:er som du vill rita i diagrammet i filterdialogrutan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om Azure [Monitor-arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
* Läs mer om [Metric Explorer](metrics-charts.md)
