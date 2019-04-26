---
title: Visa och analysera data i Azure Log Analytics | Microsoft Docs
description: Hjälp för användare av loggsökning i Log Analytics till Azure Monitor log-fråga upplevelse.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519906"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Övergång från loggsökning i Log Analytics till Azure Monitor-loggar
Loggsökning i Log Analytics har nyligen har ersatts med en ny upplevelse för att analysera loggar med Azure Monitor. Sidan Log search är för närvarande fortfarande är tillgängliga via den **loggar (klassisk)** menyalternativ i det **Log Analytics-arbetsytor** sida i Azure portal, men kommer att tas bort den 15 februari 2019. Den här artikeln beskrivs skillnaderna mellan de båda upplevelserna för att du övergår från loggsökning. 

## <a name="filter-results-of-a-query"></a>Filtrera resultatet av en fråga
I Loggsökning visas en lista över filter som sökresultat levereras. Välj ett filter och klicka på **tillämpa** att köra frågan med det valda filtret.

![Logga sökfilter](media/log-search-transition/filter-log-search.png)

I Azure Monitor-loggar, Välj *Filter (förhandsversion)* att visa filter. Klicka på filterikonen för att visa ytterligare filter. Välj ett filter och klicka på **tillämpa och kör** att köra frågan med det valda filtret.

![Loggar filter](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahera anpassade fält 
I Log Search för att extrahera [anpassade fält](../platform/custom-fields.md) från listvyn, där ett fält menyn innefattar åtgärden _extrahera fält från tabellen_.

![Logga extrahera sökfält](media/log-search-transition/extract-fields-log-search.png)

I Azure Monitor-loggarna för att extrahera fält från tabellvyn. Expandera en post genom att klicka på pilen till vänster och sedan klicka på ellipsen för att komma åt den _extrahera fält_ åtgärd.

![Loggar extrahera fält](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funktioner och datorgrupper
Välj för att spara en sökning i Loggsökning **sparade sökningar** och **Lägg till** måste ange ett namn, kategori och frågetexten. Skapa en [datorgrupp](../platform/computer-groups.md) genom att lägga till ett funktionsalias.

![Spara loggsökning](media/log-search-transition/save-search-log-search.png)

Om du vill spara den aktuella frågan i Azure Monitor-loggar, Välj **spara**. Ändra **Spara som** till _funktionen_ och ger en **Funktionsalias** att skapa en [funktionen](functions.md). Välj _spara frågan som en datorgrupp_ att använda funktionsalias för en [datorgrupp](../platform/computer-groups.md).

![Spara loggfråga](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Sparade frågor
I Loggsökning, din sparade frågor som är tillgängliga via uppgiften fältet **sparade sökningar**. I Azure Monitor-loggar, få åtkomst till sparade frågor från [Frågeutforskaren](../log-query/get-started-portal.md#save-queries).

![Frågeutforskaren](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Öka detaljnivån för sammanfattad rader
Du kan klicka på en rad i en sammanfattande fråga för att starta en annan fråga som visar detaljinformation på den raden i Log Search.

![Log Search detaljgranska](media/log-search-transition/drilldown-search.png)

I Azure Monitor-loggar, måste du ändra frågan så att dessa poster. Expandera någon av raderna i resultatet och klicka på den **+** bredvid värdet som ska lägga till i frågan. Sedan kommentera ut den **sammanfatta** kommandot och kör frågan igen.

![Azure Monitor loggar detaljgranska](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Vidta åtgärd
I Loggsökning, kan du [startar en runbook](take-action.md) från ett sökresultat genom att välja **vidta åtgärder**.

![Vidta åtgärd](media/log-search-transition/take-action-log-search.png)

I Azure Monitor-loggar, [skapar en avisering från loggfråga](../platform/alerts-log.md). Konfigurera en grupp med en eller flera åtgärder som körs som svar på aviseringen.

![Åtgärdsgrupp](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om den nya [Azure Monitor loggar upplevelse](get-started-portal.md).