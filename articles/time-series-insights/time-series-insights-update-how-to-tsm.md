---
title: Datamodellering i förhandsversionsmiljöer – Insikter i Azure Time Series | Microsoft-dokument
description: Läs mer om datamodellering i förhandsversionen av Azure Time Series Insights.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470759"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Förhandsversion av datamodellering i Azure Time Series Insights

I den här artikeln beskrivs hur du arbetar med Time Series Model i förhandsversionen av Azure Time Series Insights. Den beskriver flera vanliga datascenarier.

> [!TIP]
> * Läs om [modellen](time-series-insights-update-tsm.md)för förhandsversionstid.
> * Läs mer om hur du navigerar i förhandsversionen av förhandsversionen av [Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Instanser

Utforskaren för Azure Time Series Insights stöder **instans-,** **READ-,** **UPDATE-** och **DELETE-åtgärder** i webbläsaren. 

Börja med att välja **modellvyn** i **vyn** Analysera tidsseriestatistik explorer.

### <a name="create-a-single-instance"></a>Skapa en enskild instans

1. Gå till sidan Time Series Model-väljare och välj **Instanser** på menyn. Alla instanser som är associerade med den valda time series insights-miljön visas.

    [![Skapa en enskild instans genom att först välja Instanser.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![Lägg till en förekomst genom att välja knappen + Lägg till.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Ange förekomstinformation, välj typ- och hierarkiasociation och välj **Skapa**.

### <a name="bulk-upload-one-or-more-instances"></a>Massuppladdning av en eller flera instanser

> [!TIP]
> Du kan spara dina förekomster på skrivbordet i JSON. Den nedladdade JSON-filen kan sedan laddas upp genom följande steg.

1. Välj **Ladda upp JSON**.
1. Markera filen som innehåller förekomsterna nyttolast.

    [![Massuppladdningsinstanser via JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Välj **Överför**.

### <a name="edit-a-single-instance"></a>Redigera en enskild instans

1. Markera förekomsten och välj **redigerings-** eller **pennikonen**. 
1. Gör de nödvändiga ändringarna och välj **Spara**.

    [![Redigera en enda instans.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Ta bort en instans

1. Markera instansen och välj **ikonen** **ta bort** eller papperskorgen .

   [![Ta bort en instans genom att välja Ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Bekräfta borttagning genom att välja **Ta bort**.

> [!NOTE]
> En instans måste passera en fältverifieringskontroll för att tas bort.

## <a name="hierarchies"></a>Hierarkier

Utforskaren azure time series insights stöder **hierarki-se,** **läs,** **uppdatera**och **ta bort** åtgärder i webbläsaren. 

Börja med att välja **modellvyn** i **vyn** Analysera tidsseriestatistik explorer.

### <a name="create-a-single-hierarchy"></a>Skapa en enda hierarki

1. Gå till sidan Time Series Model-väljare och välj **Hierarkier** på menyn. Alla hierarkier som är associerade med den valda time series insights-miljön visas.

    [![Skapa en hierarki genom fönstret.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Välj **+ Lägg till**.

    [![Knappen Hierarki + Lägg till.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Välj **+ Lägg till nivå** i den högra rutan.

    [![Lägg till en nivå i hierarkin.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Ange hierarkiinformation och välj **Spara**.

    [![Ange hierarkiinformation.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Massuppladdning av en eller flera hierarkier

> [!TIP]
> Du kan spara dina hierarkier på skrivbordet i JSON. Den nedladdade JSON-filen kan sedan laddas upp genom följande steg.

1. Välj **Ladda upp JSON**.
1. Markera filen som innehåller hierarkinyttolasten.
1. Välj **Överför**.

    [![Val för massuppladdning av hierarkier.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Redigera en enda hierarki

1. Markera hierarkin och välj **redigerings-** eller **pennikonen**.
1. Gör de nödvändiga ändringarna och välj **Spara**.

    [![Val för redigering av en enda hierarki.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Ta bort en hierarki

1. Markera hierarkin och välj **ikonen** **ta bort** eller papperskorgen . 

    [![Ta bort en hierarki genom att markera knappen Ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Bekräfta borttagning genom att välja **Ta bort**.

## <a name="types"></a>Typer

Utforskaren för Azure Time Series Insights stöder typ **CREATE,** **LÄS,** **UPPDATERA**och **TA BORT** åtgärder i webbläsaren. 

Börja med att välja **modellvyn** i **vyn** Analysera tidsseriestatistik explorer.

### <a name="create-a-single-type"></a>Skapa en enda typ

1. Gå till sidan Time Series Model-väljare och välj **Typer** på menyn. Alla typer som är associerade med den valda time series insights-miljön visas.

    [![Fönstret Typ av tidsseriemodell.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Välj **+ Lägg till** om du vill visa **popup-modalen Lägg till en ny typ.**
1. Ange egenskaper och variabler för din typ. När du har angett det väljer du **Spara**. 

    [![Konfigurationsinställningar för att lägga till en typ.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Massuppladdning av en eller flera typer

> [!TIP]
> Du kan spara dina typer på skrivbordet i JSON. Den nedladdade JSON-filen kan sedan laddas upp genom följande steg.

1. Välj **Ladda upp JSON**.
1. Markera filen som innehåller typen nyttolast.
1. Välj **Överför**.

    [![Masstyper uppladdningsalternativ.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Redigera en enda typ

1. Markera typen och välj **redigerings-** eller **pennikonen**.
1. Gör de nödvändiga ändringarna och välj **Spara**.

    [![Redigera en typ i fönstret.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Ta bort en typ

1. Välj typ och välj ikonen **ta bort** eller **papperskorgen**. .

   [![Ta bort en typ genom att välja Ta bort.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Bekräfta borttagning genom att välja **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Mer information om Time Series Model finns i [Datamodellering](./time-series-insights-update-tsm.md).

- Mer information om förhandsversionen finns [i Visualisera data i förhandsversionen av Azure Time Series Insights](./time-series-insights-update-explorer.md)Preview .

- Om du vill veta mer om JSON-former som stöds läser du [JSON-former som stöds](./time-series-insights-send-events.md#supported-json-shapes).
