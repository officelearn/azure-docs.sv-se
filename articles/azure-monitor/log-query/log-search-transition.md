---
title: Visa och analysera data i Azure Log Analytics | Microsoft Docs
description: Hjälp för användare av Log Analytics loggs ökning för att Azure Monitor logg fråga.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: f34389785c0f4a43bfcab3e50f2dcd132b2cb629
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505813"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Över gång från Log Analytics loggs ökning till Azure Monitor loggar
Loggs ökningen i Log Analytics ersattes nyligen med en ny upplevelse för analys av Azure Monitors loggar. Sidan för loggs ökning är för närvarande fortfarande tillgänglig via meny alternativet **loggar (klassisk)** på sidan **Log Analytics arbets ytor** i Azure Portal men kommer att tas bort 15 februari 2019. I den här artikeln beskrivs skillnaderna mellan de två upplevelserna som hjälper dig att övergå från loggs ökning. 

## <a name="filter-results-of-a-query"></a>Filtrera resultat från en fråga
I loggs ökningen visas en lista med filter som Sök Resultat levereras. Välj ett filter och klicka på **tillämpa** för att köra frågan med det valda filtret.

![Loggs öknings filter](media/log-search-transition/filter-log-search.png)

I Azure Monitor loggar väljer du *filter (för hands version)* för att visa filter. Klicka på filter ikonen för att Visa tilläggs filter. Välj ett filter och klicka på **använd & kör** för att köra frågan med det valda filtret.

![Loggar filter](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahera anpassade fält 
I loggs ökning extraherar du [anpassade fält](../platform/custom-fields.md) från listvyn där fältets meny innehåller åtgärden _extrahera fält från tabellen_.

![Hämta fält för loggs ökning](media/log-search-transition/extract-fields-log-search.png)

I Azure Monitor loggar extraherar du anpassade fält från vyn tabell. Expandera en post genom att klicka på pilen till vänster och klicka sedan på ellipsen för att få åtkomst till åtgärden _extrahera fält_ .

![Loggar extrahera fält](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funktioner och dator grupper
Om du vill spara en sökning i loggs ökning väljer du **sparade sökningar** och **lägger till** för att ange namn, kategori och frågetext. Skapa en [dator grupp](../platform/computer-groups.md) genom att lägga till ett funktions Ali Aset.

![Spara loggs ökning](media/log-search-transition/save-search-log-search.png)

Om du vill spara den aktuella frågan i Azure Monitor loggar väljer du **Spara**. Ändra **Spara som** och ange ett **funktions Ali Aset** _för att skapa_ en [funktion](functions.md). Välj _Spara den här frågan som en dator grupp_ om du vill använda funktions Ali Aset för en [dator grupp](../platform/computer-groups.md).

![Spara logg fråga](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Sparade frågor
I loggs ökningen är dina sparade frågor tillgängliga via åtgärds fältets **sparade sökningar**. I Azure Monitor loggar får du åtkomst till sparade frågor från [query Explorer](../log-query/get-started-portal.md#save-queries).

![Query Explorer](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Öka detalj nivån för summerade rader
I loggs ökningen kan du klicka på en rad i en summerad fråga för att starta en annan fråga som visar detaljerade poster på raden.

![Loggs öknings specificering](media/log-search-transition/drilldown-search.png)

I Azure Monitor loggar måste du ändra frågan för att returnera dessa poster. Expandera en av raderna i resultaten och klicka på **+** bredvid värdet för att lägga till den i frågan. Kommentera sedan ut kommandot **sammanfatta** och kör frågan igen.

![Azure Monitor loggar-nedbrytning](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Vidta åtgärd
I loggs ökning kan du [starta en Runbook](../platform/action-groups.md) från ett Sök resultat genom att välja **vidta åtgärd**.

![Vidta åtgärd](media/log-search-transition/take-action-log-search.png)

I Azure Monitor loggar [skapar du en avisering från logg frågan](../platform/alerts-log.md). Konfigurera en åtgärds grupp med en eller flera åtgärder som ska köras som svar på aviseringen.

![Åtgärdsgrupp](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den nya [upplevelsen av Azure Monitor loggar](get-started-portal.md).
