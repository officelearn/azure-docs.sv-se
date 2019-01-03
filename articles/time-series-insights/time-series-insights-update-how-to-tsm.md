---
title: Datamodellering i förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Förstå datamodellering i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555412"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Datamodellering i förhandsversionen av Azure Time Series Insights

Det här dokumentet beskriver hur du arbetar med Time Series modeller följa förhandsversionen av Azure Time Series Insights. Den beskriver flera vanliga datascenarier.

Mer information om hur du använder uppdateringen [förhandsversion av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typer

### <a name="create-a-single-type"></a>Skapa en enda typ

1. Gå till panelen väljare Time Series-modeller och välj **typer** på menyn. Dölj panelen för att fokusera på modeller för Time Series-typer.

    ![Portal_one][1]

1. Välj **Lägg till**.
1. Ange all information som rör typer och välj **skapa**. Den här åtgärden skapar typer i miljön.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Massöverför en eller flera typer

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller typen nyttolasten.
1. Välj **Överför**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Redigera en enda typ

Välj typ och **redigera**. Gör nödvändiga ändringar och välj **spara**.

![Portal_four][4]

### <a name="delete-a-type"></a>Ta bort en typ

Välj typ och **ta bort**. Om inga instanser är associerade med typerna, tas den bort.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarkier

### <a name="create-a-single-hierarchy"></a>Skapa en hierarki

1. Gå till panelen väljare Time Series-modeller och välj **hierarkier** på menyn. Dölj panelen för att fokusera på modeller för Time Series-hierarkier.

    ![Portal_six][6]

1. Välj **Lägg till**.

    ![Portal_seven][7]

1. Välj **Lägg till nivå** i den högra rutan.

    ![Portal_eight][8]

1. Ange information för hierarkin och välj **skapa**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Massöverför en eller flera hierarkier

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten i hierarkin.
1. Välj **Överför**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Redigera en hierarki

Välj hierarkin och **redigera**. Gör nödvändiga ändringar och välj **spara**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

Välj hierarkin och **ta bort**. Om inga instanser är associerade med hierarkin, tas den bort.

![Portal_twelve][12]

## <a name="instances"></a>Instanser

### <a name="create-a-single-instance"></a>Skapa en enda instans

1. Gå till panelen väljare Time Series-modeller och välj **instanser** på menyn. Dölj panelen för att fokusera på modeller för Time Series-instanser.

    ![Portal_thirteen][13]

1. Välj **Lägg till**.

    ![Portal_fourteen][14]

1. Ange instansinformation, Välj typ och hierarki associationen och välj **skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Massöverför en eller flera instanser

1. Välj **ladda upp JSON**.
1. Välj den fil som innehåller nyttolasten instanser.

    ![Portal_fifteen][15]

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

Välj instansen och välj **redigera**. Gör nödvändiga ändringar och välj **spara**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Ta bort en instans

Välj instansen och välj **ta bort**. Om inga händelser är associerade med förekomsterna, tas den bort.

## <a name="next-steps"></a>Nästa steg

- Mer information om Time Series modeller [datamodellering](./time-series-insights-update-tsm.md).
- Mer information om förhandsversionen av [visualisera data i förhandsversionen av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).
- Mer information om JSON-former som stöds, läsa [stöds JSON-former](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png