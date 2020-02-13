---
title: Konverterings alternativ för Azure Monitor Visa designer till arbets böcker
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171558"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Konverterings alternativ för Azure Monitor Visa designer till arbets böcker
[View Designer](view-designer.md) är en funktion i Azure Monitor som gör att du kan skapa anpassade vyer som hjälper dig att visualisera data i arbets ytan Log Analytics, med diagram, listor och tids linjer. De fasas ut och ersätts med arbets böcker som tillhandahåller ytterligare funktioner. I den här artikeln jämförs grundläggande begrepp mellan de två och alternativen för att konvertera vyer till arbets böcker.

## <a name="basic-workbook-designs"></a>Grundläggande arbets boks design

View Designer har en fast statisk format representation, medan arbets böcker ger frihet att ta med och ändra hur data representeras. I bilderna nedan visas två exempel på hur du kan ordna arbets böcker när du konverterar vyer.

[Lodrät arbets bok](view-designer-conversion-examples.md#vertical)
![lodräta](media/view-designer-conversion-options/view-designer-vertical.png)

[Arbets bok](view-designer-conversion-examples.md#tabbed)
![fliken data typ distribution](media/view-designer-conversion-options/distribution-tab.png)
![data typer över tid-fliken](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Panel konvertering
Visa designer använder funktionen översikts panel för att representera och sammanfatta det övergripande läget. Dessa representeras i sju paneler och sträcker sig från siffror till diagram. I arbets böcker kan användare skapa liknande visualiseringar och fästa dem på samma sätt som översikts panelernas ursprungliga format. 

![Galleri](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Visa instrument panels konvertering
Visa designer-paneler består vanligt vis av två avsnitt, en visualisering och en lista som matchar data från visualiseringen, till exempel **ring & List** panel.

![Ring](media/view-designer-conversion-options/donut-example.png)

Med arbets böcker låter du användaren välja att fråga en eller båda avsnitten i vyn. Att utforma frågor i arbets böcker är en enkel process i två steg. Först genereras data från frågan, och sedan återges data som en visualisering.  Ett exempel på hur den här vyn återskapas i arbets böcker är följande:

![Konvertera](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Nästa steg
- [Åtkomst till arbets böcker & behörigheter](view-designer-conversion-access.md)