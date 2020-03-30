---
title: Azure Monitor-vydesigner till arbetsböcker konverteringsalternativ
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658718"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor-vydesigner till arbetsböcker konverteringsalternativ
[View designer](view-designer.md) är en funktion i Azure Monitor som låter dig skapa anpassade vyer som hjälper dig att visualisera data i din Log Analytics arbetsyta, med diagram, listor och tidslinjer. De håller på att fasas ut och ersättas med arbetsböcker som ger ytterligare funktioner. I den här artikeln jämförs grundläggande begrepp mellan de två och alternativen för att konvertera vyer till arbetsböcker.

## <a name="basic-workbook-designs"></a>Grundläggande arbetsboksdesign

Vydesignern har en fast statisk representation, medan arbetsböcker ger frihet att inkludera och ändra hur data representeras. Bilderna nedan visar två exempel på hur du kan ordna arbetsböcker när du konverterar vyer.

[Lodrät arbetsbok](view-designer-conversion-examples.md#vertical)
![Lodrät](media/view-designer-conversion-options/view-designer-vertical.png)

[Fliken Arbetsbok](view-designer-conversion-examples.md#tabbed)
![](media/view-designer-conversion-options/distribution-tab.png)
![Datatypsfördelning Datatyper över tidsflik](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Konvertering av paneler
Visa designer använder funktionen översiktspanel för att representera och sammanfatta det övergripande tillståndet. Dessa finns representerade i sju brickor, allt från tal till diagram. I arbetsböcker kan användare skapa liknande visualiseringar och fästa dem så att de liknar det ursprungliga stilsättet med översiktspaneler. 

![Galleri](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Visa konvertering av instrumentpaneler
Visningsdesignerpaneler består vanligtvis av två avsnitt, en visualisering och en lista som matchar data från visualiseringen, till exempel panelen **Donut & List.**

![Ring](media/view-designer-conversion-options/donut-example.png)

Med arbetsböcker tillåter vi användaren att välja att fråga en eller båda delarna av vyn. Att formulera frågor i arbetsböcker är en enkel tvåstegsprocess. Först genereras data från frågan och för det andra återges data som en visualisering.  Ett exempel på hur den här vyn skulle återskapas i arbetsböcker är följande:

![Konvertera](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Nästa steg
- [Komma åt arbetsböcker & behörigheter](view-designer-conversion-access.md)