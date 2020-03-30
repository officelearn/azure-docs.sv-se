---
title: Visa och analysera data i Azure Log Analytics | Microsoft-dokument
description: Hjälp för användare av Logganalysloggsökning till Azure Monitor-loggfrågeupplevelse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670142"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Övergång från logganalysloggsökning till Azure Monitor-loggar
Loggsökningen i Log Analytics ersattes nyligen med en ny upplevelse för att analysera Azure Monitor-loggar. Loggsöksidan är för närvarande fortfarande tillgänglig via menyalternativet **Loggar (klassiska)** på sidan **Logganalysarbetsytor** i Azure-portalen men tas bort 15 februari 2019. I den här artikeln beskrivs skillnader mellan de två upplevelserna som hjälper dig att gå över från loggsökning. 

## <a name="filter-results-of-a-query"></a>Filtrera resultat av en fråga
I Loggsökning visas en lista med filter när sökresultat levereras. Markera ett filter och klicka på **Använd** om du vill köra frågan med det markerade filtret.

![Loggsökfilter](media/log-search-transition/filter-log-search.png)

I Azure Monitor-loggar väljer du *Filtrera (förhandsgranskning)* för att visa filter. Klicka på filterikonen för att visa additionsfilter. Markera ett filter och klicka på **Använd & Kör** om du vill köra frågan med det markerade filtret.

![Filtret Loggar](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahera anpassade fält 
I Loggsökning extraherar du [anpassade fält](../platform/custom-fields.md) från listvyn, där ett fälts meny innehåller _åtgärdsutdragsfälten från Tabell_.

![Extrahera fält för loggsökning](media/log-search-transition/extract-fields-log-search.png)

I Azure Monitor-loggar extraherar du anpassade fält från tabellvyn. Expandera en post genom att klicka på pilen till vänster och klicka sedan på ellipsen för att komma åt åtgärden _Utdragsfält._

![Extrahera fält för loggar](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funktioner och datorgrupper
Om du vill spara en sökning i Loggsökning väljer du **Sparade sökningar** och **Lägg till** om du vill ange ett namn, en kategori och en frågetext. Skapa en [datorgrupp](../platform/computer-groups.md) genom att lägga till ett funktionsalias.

![Spara loggsökning](media/log-search-transition/save-search-log-search.png)

Om du vill spara den aktuella frågan i Azure Monitor-loggar väljer du **Spara**. Ändra **Spara som** _funktion_ och ange ett **funktionsalias** för att skapa en [funktion](functions.md). Välj _Spara den här frågan som en datorgrupp_ om du vill använda funktionsaliaset för en [datorgrupp](../platform/computer-groups.md).

![Spara loggfråga](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Sparade frågor
I Loggsökning är dina sparade frågor tillgängliga via åtgärdsfältets objekt **Sparade sökningar**. I Azure Monitor-loggar, komma åt sparade frågor från [Query Explorer](../log-query/get-started-portal.md#save-queries).

![Frågeutforskaren](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Öka detaljnivån på summerade rader
I Loggsökning kan du klicka på en rad i en sammanfattad fråga för att starta en annan fråga som listar detaljerade poster på den raden.

![Loggsökning detaljgranskning](media/log-search-transition/drilldown-search.png)

I Azure Monitor-loggar måste du ändra frågan för att returnera dessa poster. Expandera en av raderna i resultatet **+** och klicka på bredvid värdet för att lägga till den i frågan. Kommentera sedan ut **kommandot summarize** och kör frågan igen.

![Detaljerad detaljgranskning av Azure Monitor-loggar](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Vidta åtgärd
I Loggsökning kan du [starta en runbook](take-action.md) från ett sökresultat genom att välja **Vidta åtgärd**.

![Vidta åtgärd](media/log-search-transition/take-action-log-search.png)

Skapa [en avisering från loggfrågan i](../platform/alerts-log.md)Azure Monitor-loggar. Konfigurera en åtgärdsgrupp med en eller flera åtgärder som körs som svar på aviseringen.

![Åtgärdsgrupp](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om den nya [Azure Monitor-loggupplevelsen](get-started-portal.md).
