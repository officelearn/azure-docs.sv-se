---
title: Loggfrågeomfattning i Azure Monitor Log Analytics | Microsoft-dokument
description: Beskriver omfattning och tidsintervall för en loggfråga i Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249599"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Loggfrågaomfattning och tidsintervall i Azure Monitor Log Analytics
När du kör en [loggfråga](log-query-overview.md) [i Log Analytics i Azure-portalen](get-started-portal.md)beror den uppsättning data som utvärderas av frågan på omfattningen och det tidsintervall som du väljer. I den här artikeln beskrivs omfattning och tidsintervall och hur du kan ställa in var och en beroende på dina behov. Den beskriver också beteendet hos olika typer av scope.


## <a name="query-scope"></a>Frågeomfång
Frågeomfånget definierar de poster som utvärderas av frågan. Detta inkluderar vanligtvis alla poster i en enda Log Analytics-arbetsyta eller Application Insights-program. Med Logganalys kan du också ange ett scope för en viss övervakad Azure-resurs. Detta gör det möjligt för en resursägare att fokusera endast på sina data, även om resursen skrivs till flera arbetsytor.

Scopet visas alltid längst upp till vänster i logganalysfönstret. En ikon anger om omfattningen är en Log Analytics-arbetsyta eller ett program för programinsikter. Ingen ikon anger en annan Azure-resurs.

![Omfång](media/scope/scope.png)

Omfattningen bestäms av den metod du använder för att starta Log Analytics, och i vissa fall kan du ändra omfånget genom att klicka på den. I följande tabell visas de olika typer av scope som används och olika information för varje.

| Frågeomfång | Poster i omfång | Så här väljer du | Ändra omfattning |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Alla poster på log analytics-arbetsytan. | Välj **Loggar** på **Azure Monitor-menyn** eller menyn **Logganalysarbetsytor.**  | Kan ändra omfång till någon annan resurstyp. |
| Programinsiktsprogram | Alla poster i application insights-programmet. | Välj **Analys** från **översiktssidan** för Application Insights. | Det går bara att ändra omfånget till ett annat Application Insights-program. |
| Resursgrupp | Poster som skapats av alla resurser i resursgruppen. Kan innehålla data från flera Log Analytics-arbetsytor. | Välj **Loggar** på resursgruppsmenyn. | Det går inte att ändra omfattningen.|
| Prenumeration | Poster som skapats av alla resurser i prenumerationen. Kan innehålla data från flera Log Analytics-arbetsytor. | Välj Loggar på **prenumerationsmenyn.**   | Det går inte att ändra omfattningen. |
| Andra Azure-resurser | Poster som skapats av resursen. Kan innehålla data från flera Log Analytics-arbetsytor.  | Välj **Loggar** på resursmenyn.<br>ELLER<br>Välj **Loggar** på **Azure Monitor-menyn** och välj sedan ett nytt scope. | Det går bara att ändra omfånget till samma resurstyp. |

### <a name="limitations-when-scoped-to-a-resource"></a>Begränsningar när begränsad till en resurs

När frågeomfattningen är en Log Analytics-arbetsyta eller ett programinsiktsprogram är alla alternativ i portalen och alla frågekommandon tillgängliga. När följande alternativ i portalen inte är tillgängliga för en resurs är de dock inte tillgängliga eftersom de är associerade med en enda arbetsyta eller ett enda program:

- Spara
- Frågeutforskaren
- Ny varningsregel

Du kan inte använda följande kommandon i en fråga när den begränsas till en resurs, eftersom frågeomfattningen redan innehåller arbetsytor med data för den resursen eller en uppsättning resurser:

- [App](app-expression.md)
- [Arbetsytan](workspace-expression.md)
 

## <a name="query-limits"></a>Frågegränser
Du kan ha affärskrav för en Azure-resurs för att skriva data till flera Log Analytics-arbetsytor. Arbetsytan behöver inte vara i samma region som resursen, och en enda arbetsyta kan samla in data från resurser i en mängd olika regioner.  

Att ange omfånget till en resurs eller uppsättning resurser är en särskilt kraftfull funktion i Log Analytics eftersom det gör att du automatiskt kan konsolidera distribuerade data i en enda fråga. Det kan avsevärt påverka prestanda men om data behöver hämtas från arbetsytor i flera Azure-regioner.

Log Analytics hjälper till att skydda mot alltför stora omkostnader från frågor som sträcker sig över arbetsytor i flera regioner genom att utfärda en varning eller ett fel när ett visst antal regioner används. Frågan får en varning om omfattningen innehåller arbetsytor i fem eller fler regioner. det kommer fortfarande att köras, men det kan ta för lång tid att slutföra.

![Varning för frågor](media/scope/query-warning.png)

Frågan blockeras från att köras om scopet innehåller arbetsytor i 20 eller fler regioner. I det här fallet uppmanas du att minska antalet arbetsyteområden och försöka köra frågan igen. Listrutan visar alla regioner i frågans omfattning och du bör minska antalet regioner innan du försöker köra frågan igen.

![Frågan misslyckades](media/scope/query-failed.png)


## <a name="time-range"></a>Tidsintervall
Tidsintervallet anger den uppsättning poster som utvärderas för frågan baserat på när posten skapades. Detta definieras av en standardegenskap för varje post på arbetsytan eller programmet enligt följande tabell.

| Location | Egenskap |
|:---|:---|
| Log Analytics-arbetsyta          | TimeGenerated |
| Programinsiktsprogram | timestamp     |

Ange tidsintervallet genom att välja det från tidsväljaren högst upp i logganalysfönstret.  Du kan välja en fördefinierad period eller välja **Anpassad** för att ange ett visst tidsintervall.

![Tidplockare](media/scope/time-picker.png)

Om du anger ett filter i frågan som använder standardtidsegenskapen enligt tabellen ovan ändras tidsväljaren till **Ange i fråga**och tidsväljaren är inaktiverad. I det här fallet är det mest effektivt att placera filtret högst upp i frågan så att efterföljande bearbetning bara behöver arbeta med de filtrerade posterna.

![Filtrerad fråga](media/scope/query-filtered.png)

Om du använder kommandot [arbetsyta](workspace-expression.md) eller [app](app-expression.md) för att hämta data från en annan arbetsyta eller ett annat program kan tidsväljaren bete sig annorlunda. Om omfattningen är en Log Analytics-arbetsyta och du använder **appen**, eller om omfattningen är ett Application Insights-program och du använder **arbetsytan,** kanske Log Analytics inte förstår att egenskapen som används i filtret ska bestämma tidsfiltret.

I följande exempel är scopet inställt på en Log Analytics-arbetsyta.  Frågan använder **arbetsytan** för att hämta data från en annan Log Analytics-arbetsyta. Tidsväljaren ändras till **Ange i fråga** eftersom den ser ett filter som använder egenskapen **förväntad TimeGenerated.**

![Fråga med arbetsyta](media/scope/query-workspace.png)

Om frågan använder **appen** för att hämta data från ett Application Insights-program känner Log Analytics inte igen **tidsstämpelegenskapen** i filtret och tidsväljaren förblir oförändrad. I det här fallet tillämpas båda filtren. I exemplet inkluderas endast poster som skapats under de senaste 24 timmarna **where** i frågan även om den anger 7 dagar i where-satsen.

![Fråga med app](media/scope/query-app.png)

## <a name="next-steps"></a>Nästa steg

- Gå igenom en [självstudiekurs om hur du använder Log Analytics i Azure-portalen](get-started-portal.md).
- Gå igenom en [handledning om att skriva frågor](get-started-queries.md).
