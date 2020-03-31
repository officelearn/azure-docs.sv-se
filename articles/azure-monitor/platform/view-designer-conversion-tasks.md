---
title: Azure Monitor-vydesigner till arbetsböcker konvertering vanliga uppgifter
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658752"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Visa designer till arbetsböcker konvertering vanliga uppgifter
[View designer](view-designer.md) är en funktion i Azure Monitor som låter dig skapa anpassade vyer som hjälper dig att visualisera data i din Log Analytics arbetsyta, med diagram, listor och tidslinjer. De håller på att fasas ut och ersättas med arbetsböcker som ger ytterligare funktioner. I den här artikeln beskrivs uppgifter som är vanliga vid konvertering av vyer till arbetsböcker.


## <a name="quickstart-with-preset-view-designer-templates"></a>Snabbstart med förinställda vydesignermallar

Arbetsböcker i Log Analytics-arbetsytor har redan mallar gjorda för att matcha några av vyerna i vydesignern. Under kategorin **Visa designerguider** väljer du **Visa övergångsguide för designer** om du vill veta mer om dina alternativ eller väljer en av de förinställda mallarna.

![Exempel på mallar](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Aktivera tidsintervallfilter
Visa designer har ett inbyggt standardtidsintervallfilter, men i arbetsböcker är den här inställningen inte aktiverad som standard. Arbetsböcker gör det möjligt för användare att skapa sina egna tidsintervallfilter som kan vara mer tillämpliga på deras dataloggar. Stegen för att generera filtret visas nedan:

Välj alternativet **Lägg till parametrar.** **Standardstilen** är inställd *på Piller*.

![Lägg till Param](media/view-designer-conversion-tasks/add-param.png)

 Välj knappen **Lägg till parameter.**

![Lägg till parameter](media/view-designer-conversion-tasks/add-parameter.png)

Skriv *Tidsfält*i textrutan Parameternamn på menyn **Parameternamn** . Ange **parametertyp** som *tidsintervallväljare*. Markera kryssrutan **Obligatoriskt?**

![Parameter-menyn](media/view-designer-conversion-tasks/parameter-menu.png)

Spara parametern i det övre vänstra hörnet på sidofältets meny. Du kan lämna listrutan som *avkopplad* som standard eller välja ett **standardvärde för Tidsförord,** till exempel *24 timmar*. Välj **Klar redigering**.

Parametrar kan användas i frågor genom att {} lägga till klammerparenteser runt parameternamnet. Mer information om parametrar finns i [dokumentationen för arbetsböcker om parametrar](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Uppdatera frågor med parametern TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Alternativ 1: Välj Tidsintervall i listrutan Tidsintervall

![Parameter för tid](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Alternativ 2: Uppdatera dina loggfrågor

I frågan lägga till `| where TimeGenerated {TimeRange}` raden: som i följande exempel:

Ursprunglig fråga
```KQL
search * 
| summarize count() by Type
```

Uppdaterad fråga
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Inklusive en lista
De flesta vydesignervyer innehåller en lista och du kan återskapa den här standardlistan i en arbetsbok.

![Panellista](media/view-designer-conversion-tasks/tile-list.png)

Lägg till en visualisering genom att klicka på **Lägg till fråga** från cellalternativen.

![Lägg till Param](media/view-designer-conversion-tasks/add-param.png)

Vydesignern använder en standardfråga som matchar syntaxen från det ursprungliga exemplet. Detta kan uppdateras genom att ändra frågan till det uppdaterade formuläret som i följande exempel:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Uppdaterad fråga
```KQL
search * 
| summarize Count = count() by Type
```

Detta genererar en lista som liknar följande:

![Exempel på lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Aktivera miniatyrdiagram
En vanlig funktion för rutnät är att lägga till miniatyrdiagram för att sammanfatta olika datamönster över tid. Vydesignern erbjuder funktionen **Aktivera miniatyrdiagram** för alla listor, liksom arbetsböcker. Om du vill inkludera miniatyrdiagram i dina data som matchar vydesignern ansluter du data till den ursprungliga frågan som i följande exempel:

Ursprunglig fråga
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Uppdaterad fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Välj **Kolumninställningar**.
![Kolumninställningar](media/view-designer-conversion-tasks/column-settings.png)

Uppdatera listrutan **Kolumnåtergivning** så att det är ett *Spark-område*.
![Miniatyrdiagram](media/view-designer-conversion-tasks/sparkline.png)

Spara inställningarna och kör frågan igen för att uppdatera tabellen så att den innehåller en miniatyrdiagram.

Det resulterande rutnätet ser ![ut ungefär så här: Exempel på miniatyrdiagram](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Avancerade cellinställningar
Om du vill spegla vydesignern kan du utföra uppgifter som att ändra storleken på arbetsboksceller eller lägga till pins och externa länkar i loggar.

Om du vill komma åt **avancerade inställningar** väljer du kugghjulsikonen längst ned i varje cell.

![Avancerade inställningar](media/view-designer-conversion-tasks/advanced-settings.png)

Detta kommer att visa en meny med olika alternativ:

![Inställningar för avancerade inställningar](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Om du vill lägga till en nål och en länk till en extern fråga markerar du motsvarande kryssrutor. Om du vill lägga till en rubrik i cellen skriver du önskad rubrik i avsnittet **Diagramrubrik.**

Som standard är alla arbetsböcker cell inställd på att ta upp hela sidbredden, men du kan justera detta genom att skala cellen ner under fliken **Format** på menyn **Avancerade inställningar**

![Avancerad inställningsstil](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Ytterligare parametrar
Välj **Lägg till parameter om** du vill skapa en ny parameter i arbetsboken. 

Om du vill välja en prenumeration skriver du *Prenumeration* i fältet **Parameternamn** på sidomenyn och väljer *Prenumerationsväljare* i listrutan **Parametertyp**

![Prenumerationsmeny](media/view-designer-conversion-tasks/subscription-filter.png)

Om du vill välja en resurs skriver du *Resurs* i fältet **Parameternamn** på sidomenyn och väljer *Resursväljare* i listrutan **Parametertyp.**

![Resurs-menyn](media/view-designer-conversion-tasks/resource-filter.png)

Detta infogar listrantar så att du kan komma åt dina olika prenumerationer och resurser.

![Listruta för prenumerationsresurser](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Nästa steg
- [Panelkonverteringar](view-designer-conversion-tiles.md)
