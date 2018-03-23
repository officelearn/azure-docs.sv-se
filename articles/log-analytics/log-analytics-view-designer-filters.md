---
title: Filter i Azure Log Analytics vyer | Microsoft Docs
description: Ett filter i vyn logganalys tillåter användare att filtrera data i vyn av värdet för en viss egenskap utan att ändra själva vyn.  Den här artikeln beskriver hur du använder ett filter och lägga till ett i en anpassad vy.
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
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="filters-in-log-analytics-views"></a>Filter i logganalys-vyer
En **filter** i en [Log Analytics visa](log-analytics-view-designer.md) tillåter användare att filtrera data i vyn av värdet för en viss egenskap utan att ändra själva vyn.  Du kan till exempel användare av vyn för att filtrera vyn för endast data från en viss dator eller datorer.  Du kan skapa flera filter på en enda vy så att användarna kan filtrera efter flera egenskaper.  Den här artikeln beskriver hur du använder ett filter och lägga till ett i en anpassad vy.

## <a name="using-a-filter"></a>Använda ett filter
Klicka på **Filter** att öppna filterfönstret för en vy.  På så sätt kan du välja ett tidsintervall och värden för eventuella filter som är tillgängliga för vyn.  När du väljer ett filter, visas en lista över tillgängliga värden.  Du kan välja ett eller flera värden, eller så kan du ange dem i. Vyn uppdateras automatiskt för att filtrera på de värden som du anger. 

Om inget värde har valts för ett filter, tillämpas filtret inte på vyn.  Om du tar bort alla värden för ett filter, kommer inte längre filtret tillämpas.


![Filter, exempel](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>Skapa ett filter

Skapa ett filter från den **filter** när [Redigera vyn](log-analytics-view-designer.md).  Filtret är globala för vyn och gäller för alla delar i vyn.  

![Filterinställningar](media/log-analytics-view-designer/filters-settings.png)

I följande tabell beskrivs inställningarna för ett filter.

| Inställning | Beskrivning |
|:---|:---|
| Fältnamn | Namnet på fältet som används för filtrering.  Det måste matcha fältet sammanfatta i **frågan för värden**. |
| Frågan för värden | Frågan ska köras för att fylla i listrutan för filter för användaren.  Detta måste använda antingen [sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) eller [distinkta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) ange unika värden för ett visst fält och den måste matcha den **fältnamn**.  Du kan använda [sortera](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) sortera värdena som visas för användaren. |
| Tagga | Namn på fältet som används i frågor som stöder filtret och även visas för användaren. |

### <a name="examples"></a>Exempel

Följande tabell innehåller några exempel på vanliga filter.  

| Fältnamn | Frågan för värden | Tagga |
|:--|:--|:--|
| Dator   | Pulsslag &#124; distinkta datorn &#124; sortera efter dator asc | Datorer |
| EventLevelName | Event &#124; distinct EventLevelName | Allvarsgrad |
| SeverityLevel | Syslog &#124; distinkta SeverityLevel | Allvarsgrad |
| SvcChangeType | ConfigurationChange &#124; distinkta svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Ändra vyn frågor

För ett filter för att ha någon effekt, måste du ändra frågor i vyn för att filtrera på den valda värdet.  Om du inte ändrar frågor i vyn kommer alla värden som användaren väljer har ingen effekt.

Syntaxen för ett filtervärde i en fråga är: 

    where ${filter name}  

Till exempel om vyn innehåller en fråga returnerar händelser och använder ett filter som kallas datorer, kan du använda följande.

    Event | where ${Computers} | summarize count() by EventLevelName

Du kan använda följande fråga för att använda både filter om du har lagt till ett annat filter som kallas allvarlighetsgrad.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [visualiseringen delar](log-analytics-view-designer-parts.md) kan du lägga till den anpassade vyn.