---
title: Datamodellering i förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Förstå datamodellering i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461014"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Datamodellering i förhandsversionen av Azure Time Series Insights

Det här dokumentet beskriver hur du arbetar med Time Series modeller följa förhandsversionen av Azure Time Series Insights. Den beskriver flera vanliga datascenarier.

Mer information om hur du använder uppdateringen [förhandsversion av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typer

### <a name="create-a-single-type"></a>Skapa en enda typ

1. Gå till panelen väljare Time Series-modeller och välj **typer** på menyn. Dölj panelen för att fokusera på modeller för Time Series-typer.

    [![Skapa en enda typ](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Välj **Lägg till**.
1. Ange all information som rör typer och välj **skapa**. Den här åtgärden skapar typer i miljön.

    [![Lägga till en typ](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Massöverför en eller flera typer

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller typen nyttolasten.
1. Välj **Överför**.

    [![Ladda upp JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Redigera en enda typ

1. Välj typ och **redigera**. 
1. Gör nödvändiga ändringar och välj **spara**.

    [![Redigera en typ](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Ta bort en typ

1. Välj typ och **ta bort**.
1. Om inga instanser är associerade med typerna, tas den bort.

    [![Ta bort en typ](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarkier

### <a name="create-a-single-hierarchy"></a>Skapa en hierarki

1. Gå till panelen väljare Time Series-modeller och välj **hierarkier** på menyn. Dölj panelen för att fokusera på modeller för Time Series-hierarkier.

    [![Välj hierarkier](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Välj **Lägg till**.

    [![Lägg till en hierarki](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Välj **Lägg till nivå** i den högra rutan.

    [![Lägga till en nivå](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Ange information för hierarkin och välj **skapa**.

    [![Skapa en nivå](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Massöverför en eller flera hierarkier

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten i hierarkin.
1. Välj **Överför**.

    [![Massinläsning uppladdning hierarkier](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Redigera en hierarki

1. Välj hierarkin och **redigera**.
1. Gör nödvändiga ändringar och välj **spara**.

    [![Redigera en hierarki](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

1. Välj hierarkin och **ta bort**. 
1. Om inga instanser är associerade med hierarkin, tas den bort.

    [![Ta bort en hierarki](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>instanser

### <a name="create-a-single-instance"></a>Skapa en enda instans

1. Gå till panelen väljare Time Series-modeller och välj **instanser** på menyn. Dölj panelen för att fokusera på modeller för Time Series-instanser.

    [![Skapa en enda instans](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Välj **Lägg till**.

    [![Lägga till en instans](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Ange instansinformation, Välj typ och hierarki associationen och välj **skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Massöverför en eller flera instanser

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten instanser.

    [![Massöverför en eller flera instanser](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

1. Välj instansen och välj **redigera**. 
1. Gör nödvändiga ändringar och välj **spara**.

    [![Redigera en enskild instans](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Mer information om Time Series modeller [datamodellering](./time-series-insights-update-tsm.md).

- Mer information om förhandsversionen av [visualisera data i förhandsversionen av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).

- Mer information om JSON-former som stöds, läsa [stöds JSON-former](./time-series-insights-send-events.md#json).
