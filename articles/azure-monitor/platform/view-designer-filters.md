---
title: Filter i Azure Monitor vyer | Microsoft Docs
description: Ett filter i en Azure Monitor-vy låter användare filtrera data i vyn med värdet för en viss egenskap utan att ändra vyn.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658582"
---
# <a name="filters-in-azure-monitor-views"></a>Filter i Azure Monitor vyer
Ett **filter** i en [Azure Monitor-vy](view-designer.md) låter användare filtrera data i vyn med värdet för en viss egenskap utan att ändra vyn.  Du kan till exempel tillåta att användare av vyn filtrerar vyn för data enbart från en viss dator eller uppsättning datorer.  Du kan skapa flera filter i en enskild vy så att användarna kan filtrera efter flera egenskaper.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.

## <a name="using-a-filter"></a>Använda ett filter
Klicka på datum tidsintervallet överst i vyn för att öppna List rutan där du kan ändra datum tidsintervallet för vyn.

![Filter exempel](media/view-designer-filters/filters-example-time.png)

Klicka på **+** för att lägga till ett filter med anpassade filter som har definierats för vyn. Välj antingen ett värde för filtret i list rutan eller ange ett värde. Fortsätt att lägga till filter genom att **+** Klicka på. 


![Filter exempel](media/view-designer-filters/filters-example-custom.png)

Om du tar bort alla värden för ett filter kommer filtret inte längre att tillämpas.


## <a name="creating-a-filter"></a>Skapa ett filter

Skapa ett filter från fliken **filter** när du [redigerar en vy](view-designer.md).  Filtret är globalt för vyn och gäller för alla delar i vyn.  

![Filterinställningar](media/view-designer-filters/filters-settings.png)

I följande tabell beskrivs inställningarna för ett filter.

| Inställningen | Beskrivning |
|:---|:---|
| Fältnamn | Namnet på det fält som används för filtrering.  Det här fältet måste matcha det sammanfattande fältet i **fråga efter värden**. |
| Fråga efter värden | Fråga att köra för att fylla i list rutan filter för användaren.  Frågan måste använda antingen [sammanfatta](/azure/kusto/query/summarizeoperator) eller [distinkt](/azure/kusto/query/distinctoperator) för att tillhandahålla unika värden för ett visst fält och måste matcha **fält namnet**.  Du kan använda [sort](/azure/kusto/query/sortoperator) för att sortera värdena som visas för användaren. |
| Tagga | Namnet på det fält som används i frågor som stöder filtret och visas också för användaren. |

### <a name="examples"></a>Exempel

Följande tabell innehåller några exempel på vanliga filter.  

| Fältnamn | Fråga efter värden | Tagga |
|:--|:--|:--|
| Dator   | Pulsslag &#124; distinkt dator &#124; sortera efter dator ASC | Datorer |
| EventLevelName | Event &#124; DISTINCT EventLevelName | Severity |
| SeverityLevel | Syslog &#124; DISTINCT SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinkt svcChangeType | Ändrings typs |


## <a name="modify-view-queries"></a>Ändra visnings frågor

För att ett filter ska ha någon påverkan måste du ändra alla frågor i vyn för att filtrera efter de valda värdena.  Om du inte ändrar några frågor i vyn, kommer alla värden som användaren väljer att ha ingen påverkan.

Syntaxen för att använda ett filter värde i en fråga är: 

    where ${filter name}  

Om din vy till exempel har en fråga som returnerar händelser och använder ett filter som kallas _datorer_, kan du använda följande fråga.

    Event | where ${Computers} | summarize count() by EventLevelName

Om du har lagt till ett annat filter som heter allvarlighets grad kan du använda följande fråga för att använda båda filtren.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om de [visualiserings delar](view-designer-parts.md) som du kan lägga till i din anpassade vy.
