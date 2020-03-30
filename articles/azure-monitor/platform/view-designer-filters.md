---
title: Filter i Azure Monitor-vyer | Microsoft-dokument
description: Ett filter i en Azure Monitor-vy gör det möjligt för användare att filtrera data i vyn efter värdet för en viss egenskap utan att ändra själva vyn.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658582"
---
# <a name="filters-in-azure-monitor-views"></a>Filter i Azure Monitor-vyer
Ett **filter** i en [Azure Monitor-vy](view-designer.md) gör det möjligt för användare att filtrera data i vyn efter värdet för en viss egenskap utan att ändra själva vyn.  Du kan till exempel tillåta att användare av vyn filtrerar vyn för data endast från en viss dator eller en viss uppsättning datorer.  Du kan skapa flera filter i en enda vy så att användare kan filtrera efter flera egenskaper.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.

## <a name="using-a-filter"></a>Använda ett filter
Klicka på datumtidsintervallet högst upp i en vy för att öppna listrutan där du kan ändra datumtidsintervallet för vyn.

![Exempel på filter](media/view-designer-filters/filters-example-time.png)

Klicka **+** på om du vill lägga till ett filter med anpassade filter som har definierats för vyn. Välj antingen ett värde för filtret i listrutan eller skriv in ett värde. Fortsätt att lägga till **+** filter genom att klicka på . 


![Exempel på filter](media/view-designer-filters/filters-example-custom.png)

Om du tar bort alla värden för ett filter används inte längre filtret längre.


## <a name="creating-a-filter"></a>Skapa ett filter

Skapa ett filter på fliken **Filter** när [du redigerar en vy](view-designer.md).  Filtret är globalt för vyn och gäller för alla delar i vyn.  

![Filterinställningar](media/view-designer-filters/filters-settings.png)

I följande tabell beskrivs inställningarna för ett filter.

| Inställning | Beskrivning |
|:---|:---|
| Fältnamn | Namn på det fält som används för filtrering.  Det här fältet måste matcha summeringsfältet i **Fråga efter värden**. |
| Fråga efter värden | Fråga om du vill köra för att fylla i filterrullgardinsmenyn för användaren.  Den här frågan måste använda antingen [sammanfattning](/azure/kusto/query/summarizeoperator) eller [distinkt](/azure/kusto/query/distinctoperator) för att ge unika värden för ett visst fält, och den måste matcha **fältnamnet**.  Du kan använda [sortera](/azure/kusto/query/sortoperator) för att sortera de värden som visas för användaren. |
| Tagga | Namn på fältet som används i frågor som stöder filtret och som också visas för användaren. |

### <a name="examples"></a>Exempel

Följande tabell innehåller några exempel på vanliga filter.  

| Fältnamn | Fråga efter värden | Tagga |
|:--|:--|:--|
| Dator   | Pulsslag &#124; olika dator &#124; sortera efter dator asc | Datorer |
| EventLevelName | Händelse &#124; olika EventLevelName | Severity |
| AllvarlighetsgradNivå | Syslog &#124; distinkt allvarlighetsgradNivå | Severity |
| SvcChangeType | ConfigurationChange &#124; distinkt svcChangeType | Changetype |


## <a name="modify-view-queries"></a>Ändra vyfrågor

För att ett filter ska ha någon effekt måste du ändra alla frågor i vyn för att filtrera på de valda värdena.  Om du inte ändrar några frågor i vyn har alla värden som användaren väljer ingen effekt.

Syntaxen för att använda ett filtervärde i en fråga är: 

    where ${filter name}  

Om vyn till exempel har en fråga som returnerar händelser och använder ett filter som heter _Datorer_kan du använda följande fråga.

    Event | where ${Computers} | summarize count() by EventLevelName

Om du har lagt till ett annat filter med namnet Allvarlighetsgrad kan du använda följande fråga för att använda båda filtren.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Nästa steg
* Läs mer om de [visualiseringsdelar](view-designer-parts.md) som du kan lägga till i din anpassade vy.
