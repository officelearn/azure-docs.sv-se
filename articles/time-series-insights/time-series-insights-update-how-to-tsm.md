---
title: Data modellering i miljöer med förhands granskning – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data modellering i Azure Time Series Insights för hands version.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: fb1bfb30d531f8b93489c8fc1dfbc6b8172339a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006463"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Data modellering i Azure Time Series Insights för hands version

Den här artikeln beskriver hur du arbetar med tids serie modellen i Azure Time Series Insights för hands versionen. Det beskriver flera vanliga data scenarier.

Läs [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)om du vill veta mer om hur du använder uppdateringen.

## <a name="types"></a>Typer

### <a name="create-a-single-type"></a>Skapa en enskild typ

1. Gå till panelen tids serie modell väljare och välj **typer** på menyn. Dölj panelen för att fokusera på modell typerna för tids serier.

    [![fönstret typer](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Välj **+ Lägg till**.
1. Ange all information som gäller typer och välj **skapa**. Den här åtgärden skapar typer i miljön.

    [![val för att lägga till en typ](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Mass uppladdning av en eller flera typer

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller typen nytto Last.
1. Välj **Överför**.

    [![val av Mass uppladdning av en eller flera typer](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Redigera en enskild typ

1. Välj typ och välj **Redigera**. 
1. Gör de ändringar som krävs och välj **Spara**.

    [![val för redigering av en typ](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Ta bort en typ

1. Välj typ och välj **ta bort**.
1. Om inga instanser är associerade med typerna tas de bort.

    [![knappen Ta bort](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarkier

### <a name="create-a-single-hierarchy"></a>Skapa en enskild hierarki

1. Gå till panelen Välj tids serie modell väljare och välj **hierarkier** på menyn. Dölj panelen för att fokusera på tids serie modell hierarkier.

    [rutan ![hierarkier](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Välj **+ Lägg till**.

    [![knappen Lägg till](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Välj **+ Lägg till nivå** i den högra rutan.

    [![knappen "Lägg till nivå"](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Ange information om hierarkin och välj **skapa**.

    [![information om hierarkin och knappen "skapa"](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Mass uppladdning av en eller flera hierarkier

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller hierarkins nytto Last.
1. Välj **Överför**.

    [![val för Mass uppladdning av hierarkier](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Redigera en enskild hierarki

1. Välj hierarkin och välj **Redigera**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![val för att redigera en enskild hierarki](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

1. Välj hierarkin och välj **ta bort**. 
1. Om inga instanser är associerade med hierarkin tas den bort.

    [![knappen Ta bort](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instanser

### <a name="create-a-single-instance"></a>Skapa en enskild instans

1. Gå till panelen tids serie modell väljare och välj **instanser** på menyn. Dölj panelen för att fokusera på tids serie modell instanserna.

    [![instans fönstret](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Välj **Lägg till**.

    [![val för att lägga till en instans](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Ange instans information, Välj associationen typ och hierarki och välj **skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Mass uppladdning av en eller flera instanser

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller nytto lasten för instanser.

    [![val för Mass uppladdning av en eller flera instanser](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

1. Markera instansen och välj **Redigera**. 
1. Gör de ändringar som krävs och välj **Spara**.

    [![val för redigering av en enda instans](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Mer information om tids serie modellen finns i [data modellering](./time-series-insights-update-tsm.md).

- Om du vill veta mer om för hands versionen kan du läsa [visualisera data i Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

- Läs de [JSON-former som stöds](./time-series-insights-send-events.md#supported-json-shapes)för att lära dig mer om JSON-former som stöds.
