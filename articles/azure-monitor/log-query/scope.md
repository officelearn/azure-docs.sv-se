---
title: Logga omfång i Azure Monitor Log Analytics | Microsoft Docs
description: Beskriver den omfattning och ett tidsintervall för en loggfråga i Azure Monitor Log Analytics.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297294"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Logga fråga omfång och ett tidsintervall i Azure Monitor Log Analytics
När du kör en [loggfråga](log-query-overview.md) i [Log Analytics i Azure-portalen](get-started-portal.md), uppsättning data vid utvärderingen av frågan är beroende av omfång och tidsintervall som du väljer. Den här artikeln beskriver den omfattning och ett tidsintervall och hur du kan ange var beroende på dina krav. Här beskrivs också beteendet för olika typer av omfång.


## <a name="query-scope"></a>Omfång
Det specificerade omfånget definierar de poster som utvärderas av frågan. Detta omfattar vanligtvis alla poster i en enda Log Analytics-arbetsyta eller i Application Insights-programmet. Log Analytics kan du ange en omfattning för en viss övervakade Azure-resurs. På så sätt kan en resursägare kan fokusera endast på sina data, även om den här resursen skriver till flera arbetsytor.

Omfånget visas alltid överst till vänster i Log Analytics-fönstret. En ikon som visar om omfånget är en Log Analytics-arbetsyta eller ett program med Application Insights. Ingen ikon anger en annan Azure-resurs.

![Scope](media/scope/scope.png)

Omfånget bestäms av metoden du använder för att starta Log Analytics och i vissa fall kan du ändra omfattningen genom att klicka på den. I följande tabell visas de olika typerna av omfattningen som används och annan information för varje.

| Omfång | Poster i omfång | Så här väljer du | Ändra omfång |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Alla poster i Log Analytics-arbetsytan. | Välj **loggar** från den **Azure Monitor** menyn eller **Log Analytics-arbetsytor** menyn.  | Ändra omfång till någon annan resurstyp av. |
| Application Insights-program | Alla poster i Application Insights-programmet. | Välj **Analytics** från **översikt** för Application Insights. | Kan endast ändra omfång till ett annat program för Application Insights. |
| Resursgrupp | Poster som skapats av alla resurser i resursgruppen. Kan innehålla data från flera Log Analytics-arbetsytor. | Välj **loggar** från resursgruppmenyn. | Det går inte att ändra omfång.|
| Prenumeration | Poster som skapats av alla resurser i prenumerationen. Kan innehålla data från flera Log Analytics-arbetsytor. | Välj **loggar** på menyn prenumeration.   | Det går inte att ändra omfång. |
| Andra Azure-resurser | Poster som skapats av resursen. Kan innehålla data från flera Log Analytics-arbetsytor.  | Välj **loggar** från resurs-menyn.<br>ELLER<br>Välj **loggar** från den **Azure Monitor** menyn och välj sedan ett nytt scope. | Kan endast ändra omfång till samma resurstypen. |

### <a name="limitations-when-scoped-to-a-resource"></a>Begränsningar vid begränsade till en resurs

När det specificerade omfånget är en Log Analytics-arbetsyta eller ett program med Application Insights, finns alla alternativ i portalen och alla frågekommandon. När begränsade till en resurs genom följande alternativ i portalen inte tillgänglig eftersom de är associerade med en enda arbetsyta eller ett program:

- Spara
- Frågeutforskaren
- Ny aviseringsregel

Du kan inte använda följande kommandon i en fråga när begränsade till en resurs eftersom det specificerade omfånget redan innehåller några arbetsytor med data för den resurs eller en uppsättning resurser:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>Tidsintervall
Tidsintervallet som anger en uppsättning poster som utvärderas för frågan baserat på när det skapades. Detta definieras av en egenskap som är standard för varje post i arbetsytan eller programmet som anges i tabellen nedan.

| Location | Egenskap |
|:---|:---|
| Log Analytics-arbetsyta          | TimeGenerated |
| Application Insights-program | timestamp     |

Ange tidsintervall genom att välja den från tidsväljare längst ned i Log Analytics-fönstret.  Du kan välja en på förhand bestämd eller välj **anpassad** att ange en viss tidsperiod.

![Tidsväljare](media/scope/time-picker.png)

Om du anger ett filter i den fråga som använder egenskapen normaltid enligt tabellen ovan, tidsväljare ändras till **ange i fråga**, och tidsväljare är inaktiverat. I det här fallet är det mest praktiskt att placera filtret överst i frågan så att någon bearbetning som bara behöver arbeta med de filtrerade posterna.

![Filtrerad fråga](media/scope/query-filtered.png)

Om du använder den [arbetsytan](workspace-expression.md) eller [app](app-expression.md) kommando för att hämta data från en annan arbetsyta eller program, tidsväljare fungera annorlunda. Om omfånget är en Log Analytics-arbetsyta och du använder **app**, eller om omfånget är ett program med Application Insights och du använder **arbetsytan**, och sedan Log Analytics inte kan förstår att egenskapen används i den filtret bestämma tidsfiltret.

I följande exempel anges omfånget till en Log Analytics-arbetsyta.  Frågan använder **arbetsytan** att hämta data från en annan Log Analytics-arbetsyta. Tidsväljare ändras till **ange i fråga** eftersom den ser ett filter som använder den förväntade **TimeGenerated** egenskapen.

![Fråga med arbetsytan](media/scope/query-workspace.png)

Om frågan använder **app** för att hämta data från ett program med Application Insights och Log Analytics inte kan identifiera den **tidsstämpel** -egenskapen i filtret och tidsväljare förblir oförändrat. I det här fallet används båda filter. I det här exemplet bara de poster som har skapats under de senaste 24 timmarna som ingår i frågan även om det anger 7 dagar i den **där** satsen.

![Fråga med app](media/scope/query-app.png)

## <a name="next-steps"></a>Nästa steg

- Gå igenom en [självstudiekurs om hur du använder Log Analytics i Azure-portalen](get-started-portal.md).
- Gå igenom en [självstudiekurs om hur du skriver frågor](get-started-queries.md).