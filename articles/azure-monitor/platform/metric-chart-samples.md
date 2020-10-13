---
title: Exempel på Azure Monitor Metric-diagram
description: Lär dig mer om att visualisera Azure Monitor data.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9b2ab664f319de07fd70bd1a22b1ba6d64ac208f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320263"
---
# <a name="metric-chart-examples"></a>Exempel på mått diagram 

Azure-plattformen erbjuder [över ett tusen mått](./metrics-supported.md), varav många har dimensioner. Genom att använda [Dimensions filter](./metrics-charts.md), använda [delning](./metrics-charts.md), styra diagram typ och justera diagram inställningar kan du skapa kraftfulla diagnostiska vyer och instrument paneler som ger insikt i din infrastruktur och dina programs hälsa. I den här artikeln visas några exempel på de diagram som du kan bygga med [Metrics Explorer](./metrics-charts.md) och förklarar de steg som krävs för att konfigurera vart och ett av dessa diagram.

Vill du dela dina Fantastiska diagram exempel med världen? Bidra till den här sidan på GitHub och dela dina egna diagram exempel här!

## <a name="website-cpu-utilization-by-server-instances"></a>Webbplats-CPU-användning per Server instans

Det här diagrammet visar om CPU för ett App Service befinner sig inom ett acceptabelt intervall och avbryter det efter instansen för att avgöra om belastningen distribuerades korrekt. Du kan se från diagrammet att appen kördes på en enskild Server instans före 6, och sedan skala upp genom att lägga till en annan instans.

![Linje diagram över genomsnittlig CPU-procent per Server instans](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

Välj din App Service resurs och hitta måttet för **processor procent** . Klicka sedan på **tillämpa delning** och välj dimensionen **instans** .

## <a name="application-availability-by-region"></a>Program tillgänglighet per region

Visa ditt programs tillgänglighet efter region för att identifiera vilka geografiska platser som har problem. Det här diagrammet visar måttet Application Insights tillgänglighet. Du ser att det övervakade programmet inte har problem med tillgänglighet från USA: USA, östra, men har ett begränsat tillgänglighets problem från västra USA och Asien, östra.

![Diagram över genomsnittlig tillgänglighet per platser](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

Du måste först aktivera övervakning av [Application Insights tillgänglighet](../app/monitor-web-app-availability.md) för din webbplats. Efter det väljer du Application Insights resursen och sedan tillgänglighets måttet. Använd delning på dimensionen för **körnings plats** .

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volym lagrings konto transaktioner per API-namn

Din lagrings konto resurs har en överbelastad mängd transaktioner. Du kan använda Transactions-måttet för att identifiera vilket API som ansvarar för överbelastningen. Observera att följande diagram har kon figurer ATS med samma dimension (API-namn) i filtrering och delning för att begränsa vyn till endast API-anropen av intresset:

![Stapeldiagram över API-transaktioner](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hur konfigurerar du det här diagrammet?

I mått väljaren väljer du ditt lagrings konto och måttet för **transaktioner** . Växla diagram typ till **stapeldiagram**. Klicka på **Använd delning** och välj dimensions- **API-namn**. Klicka sedan på **Lägg till filter** och Välj dimension för **API-namn** en gång. I dialog rutan filter väljer du de API: er som du vill rita i diagrammet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om Azure Monitor- [arbetsböcker](./workbooks-overview.md)
* Läs mer om [Metric Explorer](metrics-charts.md)

