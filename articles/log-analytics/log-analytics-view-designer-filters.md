---
title: Filter i Azure Log Analytics vyer | Microsoft Docs
description: Ett filter i vyn Log Analytics kan du filtrera data i vyn av värdet för en viss egenskap utan att ändra själva vyn.  Den här artikeln beskriver hur du använder ett filter och addera en till en anpassad vy.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: dcdc84f100ce534ea517f0201b0c090c3059a318
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160937"
---
# <a name="filters-in-log-analytics-views"></a>Filter i Log Analytics-vyer
En **filter** i en [Log Analytics visa](log-analytics-view-designer.md) tillåter användare att filtrera data i vyn av värdet för en viss egenskap utan att ändra själva vyn.  Du kan till exempel tillåta användare av vyn för att filtrera vyn för endast data från en viss dator eller datorer.  Du kan skapa flera filter i en enda vy så att användarna kan filtrera efter flera egenskaper.  Den här artikeln beskriver hur du använder ett filter och addera en till en anpassad vy.

## <a name="using-a-filter"></a>Med hjälp av ett filter
Klicka på data tidsintervall överst i en vy för att öppna nedrullningsbara menyn där du kan ändra data tidsintervallet för vyn.

![Filter, exempel](media/log-analytics-view-designer/filters-example-time.png)

Klicka på den **+** Lägg till ett filter med hjälp av anpassade filter som definieras för vyn. Välja ett värde för filtret från listrutan eller skriver in ett värde. Fortsätt att lägga till filter genom att klicka på den **+**. 


![Filter, exempel](media/log-analytics-view-designer/filters-example-custom.png)

Om du tar bort alla värden för ett filter, kommer inte längre filtret tillämpas.


## <a name="creating-a-filter"></a>Skapa ett filter

Skapa ett filter från den **filter** fliken när [en redigeringsvyn](log-analytics-view-designer.md).  Filtret är globala för vyn och gäller för alla delar i vyn.  

![Filterinställningar](media/log-analytics-view-designer/filters-settings.png)

I följande tabell beskrivs inställningarna för ett filter.

| Inställning | Beskrivning |
|:---|:---|
| Fältnamn | Namnet på fältet som används för filtrering.  Det måste matcha fältet sammanfatta i **fråga för värden**. |
| Fråga för värden | Frågan ska köras för att fylla i listrutan för filter för användaren.  Detta måste använda antingen [sammanfatta](/azure/kusto/query/summarizeoperator) eller [distinkta](/azure/kusto/query/distinctoperator) ange unika värden för ett visst fält det måste matcha den **fältnamn**.  Du kan använda [sortera](/azure/kusto/query/sortoperator) att sortera de värden som visas för användaren. |
| Tagga | Namn på fältet som används i frågor som stöd för filtret och visas också till användaren. |

### <a name="examples"></a>Exempel

Följande tabell innehåller några exempel på vanliga filter.  

| Fältnamn | Fråga för värden | Tagga |
|:--|:--|:--|
| Dator   | Pulsslag &#124; distinct Computer &#124; sortera efter dator asc | Datorer |
| EventLevelName | Händelsen &#124; distinkta EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinkta SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinkta svcChangeType | changeType |


## <a name="modify-view-queries"></a>Ändra vy frågor

Du måste ändra eventuella frågor i vyn för att filtrera på de valda värdena för ett filter att ha någon effekt.  Om du inte ändrar eventuella frågor i vyn har ingen effekt alla värden som användaren väljer.

Syntaxen för ett filtervärde i en fråga är: 

    where ${filter name}  

Till exempel om vyn har en fråga returnerar händelserna och använder ett filter som kallas datorer, kan du använda följande.

    Event | where ${Computers} | summarize count() by EventLevelName

Du kan använda följande fråga för båda filter om du har lagt till ett annat filter som kallas allvarlighetsgrad.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Nästa steg
* Läs mer om den [visualisering delar](log-analytics-view-designer-parts.md) du kan lägga till den anpassade vyn.