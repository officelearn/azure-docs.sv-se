---
title: Azure Monitor-vydesigner till arbetsböcker panelkonverteringar
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658634"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor-vykonverteringar av designerpaneler
[View designer](view-designer.md) är en funktion i Azure Monitor som låter dig skapa anpassade vyer som hjälper dig att visualisera data i din Log Analytics arbetsyta, med diagram, listor och tidslinjer. De håller på att fasas ut och ersättas med arbetsböcker som ger ytterligare funktioner. Den här artikeln innehåller information om hur du konverterar olika paneler till arbetsböcker.

## <a name="donut--list-tile"></a>Panel för donut & lista

![Donut lista](media/view-designer-conversion-tiles/donut-list.png)

Att återskapa & listpanelen i arbetsböcker innebär två separata visualiseringar. För donut delen finns det två alternativ.
För båda börja med att välja **Lägg till fråga** och klistra in den ursprungliga frågan från vydesignern i cellen.

**Alternativ 1:** Välj **cirkeldiagram** på den nedrullningsbara **visualiseringsrutan:** ![Menyn Cirkeldiagramvisualisering](media/view-designer-conversion-tiles/pie-chart.png)

**Alternativ 2:** Välj **Ange efter fråga** i listrutan **Visualisering** och lägg `| render piechart` till i frågan:

 ![Menyn Visualisering](media/view-designer-conversion-tiles/set-by-query.png)

**Exempel**

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Uppdaterad fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Information om hur du skapar en lista och aktiverar miniatyrdiagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Följande är ett exempel på hur den donut & listpanelen kan omtolkas i arbetsböcker:

![Arbetsböcker för donutlista](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Linjediagram & listpanel
![Lista över linjediagram](media/view-designer-conversion-tiles/line-list.png) 

Så här återskapar du linjediagramdelen frågan enligt följande:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Uppdaterad fråga
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Det finns två alternativ för att visualisera linjediagrammet

**Alternativ 1:** Välj **linjediagram** i listrutan **Visualisering:**
 
 ![Meny för linjediagram](media/view-designer-conversion-tiles/line-visualization.png)

**Alternativ 2:** Välj **Ange efter fråga** i listrutan **Visualisering** och lägg `| render linechart` till i frågan:

 ![Menyn Visualisering](media/view-designer-conversion-tiles/set-by-query.png)

**Exempel**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Information om hur du skapar en lista och aktiverar miniatyrdiagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Nedan följer ett exempel på hur linjediagrammet & listpanelen kan omtolkas i arbetsböcker:

![Arbetsböcker för linjediagramlista](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Antal & listpanel

 ![Panellista](media/view-designer-conversion-tiles/tile-list-example.png)

För nummerpanelen uppdaterar du frågan på följande sätt:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Uppdaterad fråga
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Ändra listrutan Visualisering till **Paneler** och välj sedan **Panelinställningar**.
 ![Visualisering av paneler](media/view-designer-conversion-tiles/tile-visualization.png)

Lämna avsnittet **Rubrik** tomt och välj **Vänster**. Ändra värdet för **kolumnen Använd:** till **Antal**och **Kolumnåtergivning** till **Stort tal:**

![Inställningar för paneler](media/view-designer-conversion-tiles/tile-settings.png)

 
Information om hur du skapar en lista och aktiverar miniatyrdiagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Här följer ett exempel på hur antalet & listpanelen kan omtolkas i arbetsböcker:

![Tallista arbetsböcker](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Tidslinje och lista

 ![Tidslinjelista](media/view-designer-conversion-tiles/time-list.png)

För tidslinjen uppdatera din fråga på följande sätt:

Ursprunglig fråga
```KQL
search * 
| sort by TimeGenerated desc
```

Uppdaterad fråga
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Det finns två alternativ för att visualisera frågan som ett stapeldiagram:

**Alternativ 1:** Välj **stapeldiagram** i listrutan ![ **Visualisering:** Visualisering av stapeldiagram](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Alternativ 2:** Välj **Ange efter fråga** i listrutan **Visualisering** och lägg `| render barchart` till i frågan:

 ![Menyn Visualisering](media/view-designer-conversion-tiles/set-by-query.png)

 
Information om hur du skapar en lista och aktiverar miniatyrdiagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Här följer ett exempel på hur tidslinjen & listpanelen kan omtolkas i arbetsböcker:

![Arbetsböcker för tidslinjelista](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över visa designer till arbetsböcker övergång](view-designer-conversion-overview.md)
