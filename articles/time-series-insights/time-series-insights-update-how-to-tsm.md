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
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470759"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Data modellering i Azure Time Series Insights för hands version

Den här artikeln beskriver hur du arbetar med tids serie modellen i Azure Time Series Insights för hands versionen. Det beskriver flera vanliga data scenarier.

> [!TIP]
> * Läs om [tids serie modellen](time-series-insights-update-tsm.md)för för hands versioner.
> * Läs mer om hur du navigerar i förhands gransknings gränssnittet i [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

## <a name="instances"></a>instanser

Azure Time Series Insights Explorer stöder instans åtgärderna **skapa**, **läsa**, **Uppdatera**och **ta bort** i webbläsaren. 

Börja genom att välja vyn **modell** i vyn **analysera** i Time Series Insights Explorer.

### <a name="create-a-single-instance"></a>Skapa en enskild instans

1. Gå till panelen tids serie modell väljare och välj **instanser** på menyn. Alla instanser som är kopplade till den valda Time Series Insightss miljön visas.

    [![skapa en enda instans genom att först välja instanser.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![lägga till en instans genom att klicka på knappen + Lägg till.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Ange instans information, Välj associationen typ och hierarki och välj **skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Mass uppladdning av en eller flera instanser

> [!TIP]
> Du kan spara dina instanser på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller nytto lasten för instanser.

    [![Mass överförings instanser via JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

1. Markera instansen och välj **redigerings** -eller **Penn ikonen**. 
1. Gör de ändringar som krävs och välj **Spara**.

    [![redigera en enskild instans.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Ta bort en instans

1. Välj instansen och välj ikonen **ta bort** eller **pappers korgen**.

   [![ta bort en instans genom att välja ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

> [!NOTE]
> En instans måste ha godkänt en fält verifierings kontroll för att kunna tas bort.

## <a name="hierarchies"></a>Hierarkier

Azure Time Series Insights Explorer stöder hierarkier **skapa**, **läsa**, **Uppdatera**och **ta bort** åtgärder i webbläsaren. 

Börja genom att välja vyn **modell** i vyn **analysera** i Time Series Insights Explorer.

### <a name="create-a-single-hierarchy"></a>Skapa en enskild hierarki

1. Gå till panelen Välj tids serie modell väljare och välj **hierarkier** på menyn. Alla hierarkier som är associerade med den valda Time Series Insightss miljön visas.

    [![skapa en hierarki i fönstret.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![hierarki + Lägg till-knapp.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Välj **+ Lägg till nivå** i den högra rutan.

    [![lägga till en nivå i hierarkin.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Ange information om hierarkin och välj **Spara**.

    [![ange information om hierarkin.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Mass uppladdning av en eller flera hierarkier

> [!TIP]
> Du kan spara dina hierarkier på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller hierarkins nytto Last.
1. Välj **Överför**.

    [![val för Mass uppladdning av hierarkier.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Redigera en enskild hierarki

1. Välj hierarkin och välj **redigerings** -eller **Penn ikonen**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![val för att redigera en enskild hierarki.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

1. Välj hierarkin och välj ikonen **ta bort** eller **pappers korgen**. 

    [![ta bort en hierarki genom att klicka på knappen Ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

## <a name="types"></a>Typer

Azure Time Series Insights Explorer stöder åtgärder för att **skapa**, **läsa**, **Uppdatera**och **ta bort** i webbläsaren. 

Börja genom att välja vyn **modell** i vyn **analysera** i Time Series Insights Explorer.

### <a name="create-a-single-type"></a>Skapa en enskild typ

1. Gå till panelen tids serie modell väljare och välj **typer** på menyn. Alla typer som är associerade med den valda Time Series Insightss miljön visas.

    [fönstret modell typer för ![tids serier.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Välj **+ Lägg** till för att visa **Lägg till en ny typ** av popup-fönster.
1. Ange egenskaper och variabler för din typ. När du har angett väljer du **Spara**. 

    [Lägg till en typ genom att ![konfigurations inställningar.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Mass uppladdning av en eller flera typer

> [!TIP]
> Du kan spara dina typer på Skriv bordet i JSON. Den nedladdade JSON-filen kan sedan överföras genom följande steg.

1. Välj **Ladda upp JSON**.
1. Välj den fil som innehåller typen nytto Last.
1. Välj **Överför**.

    [överförings alternativ för ![Mass typer.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Redigera en enskild typ

1. Välj typ och välj sedan ikonen **Redigera** eller **penna**.
1. Gör de ändringar som krävs och välj **Spara**.

    [![redigera en typ i fönstret.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Ta bort en typ

1. Välj typ och välj ikonen **ta bort** eller **pappers korgen**. .

   [![ta bort en typ genom att välja ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Bekräfta borttagningen genom att välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

- Mer information om tids serie modellen finns i [data modellering](./time-series-insights-update-tsm.md).

- Om du vill veta mer om för hands versionen kan du läsa [visualisera data i Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

- Läs de [JSON-former som stöds](./time-series-insights-send-events.md#supported-json-shapes)för att lära dig mer om JSON-former som stöds.
